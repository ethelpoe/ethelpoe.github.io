---
title: "Sistema de Control de Asistencia con ESP32 y Sensor Biométrico"
date: 2025-05-17T09:00:00-04:00
categories:
  - Proyectos ESP32
  - Control de Acceso
tags:
  - ESP32
  - Biométrico
  - Web Server
  - Base de Datos
  - Sistema Embebido
---

Este proyecto implementa un sistema portátil de control de asistencia usando un ESP32 y un sensor de huellas dactilares, con capacidad de almacenamiento offline y exportación de datos vía web.

## Componentes del Sistema

### Hardware Necesario
1. **Componentes Principales**:
   - ESP32 DevKit V1
   - Sensor biométrico AS608/FPM10A
   - Display OLED 128x64 (SSD1306)
   - Módulo RTC DS3231
   - Módulo SD Card
   - Batería LiPo 3.7V 2000mAh
   - Módulo TP4056 (carga de batería)

2. **Componentes Adicionales**:
   - Resistencias (10kΩ, 4.7kΩ)
   - Capacitores (100nF, 10µF)
   - LED RGB
   - Buzzer
   - Switch On/Off
   - Caja impresa 3D

## Implementación del Sistema

### 1. Configuración Base ESP32
```cpp
// Bibliotecas necesarias
#include <Arduino.h>
#include <Adafruit_Fingerprint.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <RTClib.h>
#include <SPI.h>
#include <SD.h>
#include <ESPAsyncWebServer.h>
#include <SPIFFS.h>
#include <ArduinoJson.h>

// Definición de pines
#define RX_PIN 16        // Sensor biométrico RX
#define TX_PIN 17        // Sensor biométrico TX
#define SD_CS 5          // SD Card CS
#define LED_R 25         // LED RGB - Rojo
#define LED_G 26         // LED RGB - Verde
#define LED_B 27         // LED RGB - Azul
#define BUZZER 14        // Buzzer

// Instancias globales
Adafruit_Fingerprint finger = Adafruit_Fingerprint(&Serial2);
RTC_DS3231 rtc;
Adafruit_SSD1306 display(128, 64, &Wire, -1);
AsyncWebServer server(80);

// Estructura de datos para registros
struct AttendanceRecord {
    uint32_t fingerID;
    uint32_t timestamp;
    String roomID;
    bool uploaded;
    char studentId[16];  // Agregado para referencia
    char name[32];       // Agregado para referencia
};
```

### 2. Sistema de Archivos y Almacenamiento
```cpp
// Gestión de archivos en SD
class StorageManager {
private:
    String currentFileName;
    
public:
    bool begin() {
        if (!SD.begin(SD_CS)) {
            return false;
        }
        currentFileName = getTodayFileName();
        return true;
    }
    
    String getTodayFileName() {
        DateTime now = rtc.now();
        char filename[32];
        sprintf(filename, "/ATT_%04d%02d%02d.csv", 
                now.year(), now.month(), now.day());
        return String(filename);
    }
    
    bool saveRecord(const AttendanceRecord& record, const User* user) {
        File file = SD.open(currentFileName, FILE_APPEND);
        if (!file) return false;
        
        // Formato: ID,Timestamp,RoomID,StudentID,Name,Uploaded
        file.printf("%d,%d,%s,%s,%s,%d\n", 
                   record.fingerID, 
                   record.timestamp,
                   record.roomID.c_str(),
                   user ? user->studentId : "UNKNOWN",
                   user ? user->name : "UNKNOWN",
                   record.uploaded ? 1 : 0);
        file.close();
        return true;
    }
    
    String getRecordsAsJSON() {
        StaticJsonDocument<4096> doc;
        JsonArray records = doc.createNestedArray("records");
        
        File root = SD.open("/");
        while (File file = root.openNextFile()) {
            if (String(file.name()).endsWith(".csv")) {
                parseCSVToJSON(file, records);
            }
        }
        
        String output;
        serializeJson(doc, output);
        return output;
    }
};
```

### 3. Interfaz de Usuario
```cpp
class UserInterface {
private:
    void showStatus(const char* message, bool success) {
        display.clearDisplay();
        display.setTextSize(1);
        display.setTextColor(SSD1306_WHITE);
        display.setCursor(0,0);
        display.println(message);
        display.display();
        
        digitalWrite(LED_R, !success);
        digitalWrite(LED_G, success);
        digitalWrite(LED_B, 0);
        
        tone(BUZZER, success ? 1000 : 500, 100);
    }
    
public:
    void begin() {
        display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
        display.clearDisplay();
        display.display();
    }
    
    void showWelcome() {
        display.clearDisplay();
        display.setTextSize(2);
        display.setTextColor(SSD1306_WHITE);
        display.setCursor(0,0);
        display.println("Sistema");
        display.println("Asistencia");
        display.display();
    }
};
```

### 4. Gestión de Usuarios
```cpp
// Estructura para almacenar información del usuario
struct User {
    uint32_t fingerID;
    char name[32];
    char studentId[16];
    char group[8];
    bool isActive;
};

class UserManager {
private:
    const char* USER_FILE = "/users.dat";
    std::map<uint32_t, User> users;

public:
    bool addUser(const User& user) {
        File file = SD.open(USER_FILE, FILE_APPEND);
        if (!file) return false;
        
        file.write((uint8_t*)&user, sizeof(User));
        file.close();
        
        users[user.fingerID] = user;
        return true;
    }
    
    bool enrollNewUser(const char* name, const char* studentId, const char* group) {
        uint8_t nextFingerId = getNextAvailableId();
        
        display.clearDisplay();
        display.setTextSize(1);
        display.println("Registrando nuevo");
        display.println("usuario:");
        display.println(name);
        display.println("Coloque su dedo...");
        display.display();
        
        if (enrollFingerprint(nextFingerId)) {
            User newUser = {
                .fingerID = nextFingerId,
                .isActive = true
            };
            strncpy(newUser.name, name, sizeof(newUser.name) - 1);
            strncpy(newUser.studentId, studentId, sizeof(newUser.studentId) - 1);
            strncpy(newUser.group, group, sizeof(newUser.group) - 1);
            
            return addUser(newUser);
        }
        return false;
    }
    
    const User* getUserByFingerID(uint32_t fingerID) {
        auto it = users.find(fingerID);
        if (it != users.end()) {
            return &(it->second);
        }
        return nullptr;
    }
    
private:
    uint8_t getNextAvailableId() {
        uint8_t id = 1;
        while (users.find(id) != users.end()) id++;
        return id;
    }
    
    bool enrollFingerprint(uint8_t id) {
        int p = -1;
        display.clearDisplay();
        display.println("Esperando dedo...");
        display.display();
        
        while (p != FINGERPRINT_OK) {
            p = finger.getImage();
        }
        
        p = finger.image2Tz(1);
        if (p != FINGERPRINT_OK) return false;
        
        display.clearDisplay();
        display.println("Retire el dedo");
        display.display();
        delay(2000);
        
        display.clearDisplay();
        display.println("Coloque el mismo");
        display.println("dedo otra vez");
        display.display();
        
        p = 0;
        while (p != FINGERPRINT_OK) {
            p = finger.getImage();
        }
        
        p = finger.image2Tz(2);
        if (p != FINGERPRINT_OK) return false;
        
        p = finger.createModel();
        if (p != FINGERPRINT_OK) return false;
        
        p = finger.storeModel(id);
        return p == FINGERPRINT_OK;
    }
};
```

### 5. Servidor Web
```cpp
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
    <title>Sistema de Asistencia</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background: #f0f2f5;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .button {
            background-color: #4CAF50;
            border: none;
            color: white;
            padding: 15px 32px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 4px 2px;
            cursor: pointer;
            border-radius: 4px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Control de Asistencia</h1>
        <button class="button" onclick="downloadData()">Descargar Registros</button>
        <div id="records"></div>
    </div>
    <script>
        function downloadData() {
            fetch('/records')
                .then(response => response.blob())
                .then(blob => {
                    const url = window.URL.createObjectURL(blob);
                    const a = document.createElement('a');
                    a.style.display = 'none';
                    a.href = url;
                    a.download = 'asistencia.csv';
                    document.body.appendChild(a);
                    a.click();
                    window.URL.revokeObjectURL(url);
                });
        }
    </script>
</body>
</html>
)rawliteral";

const char register_html[] PROGMEM = R"rawliteral(
    <div class="form-container">
        <h2>Registrar Nuevo Usuario</h2>
        <form id="registerForm">
            <div class="form-group">
                <label for="name">Nombre Completo:</label>
                <input type="text" id="name" name="name" required>
            </div>
            <div class="form-group">
                <label for="studentId">Matrícula:</label>
                <input type="text" id="studentId" name="studentId" required>
            </div>
            <div class="form-group">
                <label for="group">Grupo:</label>
                <input type="text" id="group" name="group" required>
            </div>
            <button type="submit" class="button">Iniciar Registro</button>
        </form>
        <div id="status"></div>
    </div>
    <style>
        .form-container {
            margin-top: 20px;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 8px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        .form-group label {
            display: block;
            margin-bottom: 5px;
        }
        .form-group input {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
    </style>
)rawliteral";
```

### 6. Implementación Principal
```cpp
class AttendanceSystem {
private:
    StorageManager storage;
    UserInterface ui;
    String currentRoom;
    
public:
    bool begin() {
        // Inicializar componentes
        if (!finger.begin(57600)) {
            return false;
        }
        
        if (!rtc.begin()) {
            return false;
        }
        
        if (!storage.begin()) {
            return false;
        }
        
        ui.begin();
        setupWebServer();
        return true;
    }
    
    void setupWebServer() {
        server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
            request->send_P(200, "text/html", index_html);
        });
        
        server.on("/records", HTTP_GET, [this](AsyncWebServerRequest *request){
            String records = storage.getRecordsAsJSON();
            request->send(200, "application/json", records);
        });
        
        server.on("/register", HTTP_GET, [](AsyncWebServerRequest *request){
            request->send_P(200, "text/html", register_html);
        });
        
        server.begin();
    }
    
    void loop() {
        // Verificar huella
        uint8_t result = finger.getImage();
        if (result == FINGERPRINT_OK) {
            if (finger.image2Tz() == FINGERPRINT_OK) {
                result = finger.fingerFastSearch();
                if (result == FINGERPRINT_OK) {
                    registerAttendance(finger.fingerID);
                }
            }
        }
    }
    
private:
    void registerAttendance(uint32_t fingerID) {
        DateTime now = rtc.now();
        AttendanceRecord record = {
            .fingerID = fingerID,
            .timestamp = now.unixtime(),
            .roomID = currentRoom,
            .uploaded = false
        };
        
        const User* user = userManager.getUserByFingerID(fingerID);
        if (storage.saveRecord(record, user)) {
            ui.showStatus("Registro exitoso", true);
        } else {
            ui.showStatus("Error al guardar", false);
        }
    }
};
```

## Diseño 3D de la Caja

### Especificaciones
```
Dimensiones externas: 150x100x50mm
Material: PLA/PETG
Características:
- Ventana para sensor biométrico
- Ventana para display OLED
- Acceso para carga USB
- Ventilación
- Soporte para batería
- Tapa deslizante
```

## Configuración y Uso

### 1. Primera Configuración
1. Cargar firmware al ESP32
2. Establecer fecha/hora en RTC
3. Registrar huellas maestras
4. Configurar ID de aula

### 2. Uso Diario
1. Encender dispositivo
2. Verificar batería y SD
3. Seleccionar aula
4. Tomar asistencia
5. Exportar datos al final

### 3. Exportación de Datos
1. Conectar a red WiFi del dispositivo
2. Acceder a página web (192.168.4.1)
3. Descargar registros
4. Convertir a Excel

## Mantenimiento

### 1. Diario
- Verificar batería
- Limpiar sensor
- Respaldar datos

### 2. Semanal
- Verificar espacio SD
- Actualizar base de datos
- Sincronizar RTC

### 3. Mensual
- Verificar firmware
- Limpiar registros antiguos
- Calibrar sensor

## Solución de Problemas

### 1. Problemas de Lectura
- Limpiar sensor
- Verificar posición del dedo
- Reregistrar huella

### 2. Problemas de Almacenamiento
- Verificar SD
- Comprobar formato
- Liberar espacio

### 3. Problemas de Batería
- Verificar conexiones
- Medir voltaje
- Reemplazar si necesario

## Documentación

### 1. Manual Técnico
- Esquemáticos
- Código fuente
- Procedimientos de calibración

### 2. Manual de Usuario
- Guía de inicio rápido
- Procedimientos diarios
- Solución de problemas

### 3. Base de Datos
- Estructura de archivos
- Formato CSV
- Procedimientos de respaldo

*Este sistema combina hardware y software para crear una solución práctica de control de asistencia, ideal para entornos sin conectividad constante.*
