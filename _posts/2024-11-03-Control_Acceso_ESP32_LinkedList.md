---
layout: post
title: "Control de Acceso con ESP32 y LinkedList"
author: "Francisco Macias"
date: 2024-11-03 20:55:00 +0800
subtitle: "Tutorial completo para implementar un sistema de acceso en C++ con ESP32"
categories: 
   - Proyectos, 
   - IoT,
   - C++
tags:
   - ESP32
   - LinkedList
   - IoT
   - Tutorial
description: "Aprende a desarrollar un sistema de control de acceso utilizando ESP32 y C++ con almacenamiento y visualización web."
---

## Descripción General
Este tutorial tiene como objetivo guiarte paso a paso en la creación de un sistema de control de acceso utilizando el ESP32 y C++. 
A través de la estructura de datos LinkedList, podrás registrar entradas y salidas de usuarios, almacenarlos localmente en el ESP32 
y enviarlos a una base de datos remota. Al final, también construiremos una interfaz web en React.js para la visualización de registros.

## Niveles de Desarrollo

### Nivel 1: Creación de la Estructura de LinkedList

**Objetivo:** Crear una LinkedList básica en C++ que almacene información de los usuarios, como ID, nombre y timestamp de entrada.

#### Paso 1: Configuración Inicial del Proyecto
1. **Instalación de Arduino IDE:** Asegúrate de tener la última versión de Arduino IDE con soporte para ESP32.
2. **Configuración del ESP32 en Arduino IDE:** Ve a **Preferencias** > **Gestor de URLs adicionales** y añade la URL de ESP32: 
   `https://dl.espressif.com/dl/package_esp32_index.json`.
3. **Librerías Necesarias:** Instala las librerías `WiFi.h` y `SPIFFS.h` desde el Gestor de Librerías en Arduino IDE.

#### Paso 2: Definición de la Clase LinkedList
```cpp
struct UserNode {
    int id;
    String name;
    long timestamp;
    UserNode* next;

    UserNode(int id, String name, long timestamp) 
        : id(id), name(name), timestamp(timestamp), next(nullptr) {}
};

class LinkedList {
public:
    UserNode* head;

    LinkedList() : head(nullptr) {}

    void addUser(int id, String name, long timestamp) {
        UserNode* newNode = new UserNode(id, name, timestamp);
        newNode->next = head;
        head = newNode;
    }

    void removeUser(int id) {
        UserNode* temp = head;
        UserNode* prev = nullptr;
        while (temp != nullptr && temp->id != id) {
            prev = temp;
            temp = temp->next;
        }
        if (temp == nullptr) return;  // Usuario no encontrado
        if (prev != nullptr) prev->next = temp->next;
        else head = temp->next;
        delete temp;
    }
};
```

#### Explicación de Conceptos
- **Struct UserNode:** Define los datos de cada usuario y un puntero `next` que enlaza al siguiente nodo.
- **Clase LinkedList:** Administra la lista enlazada permitiendo agregar y eliminar usuarios.

#### Ejercicio:
- Implementa una función `printUsers()` que recorra y muestre los usuarios registrados en la LinkedList.

---

### Nivel 2: Interacción con Botones para Registro de Entrada y Salida

**Objetivo:** Añadir interacción física con el ESP32 mediante botones para simular la entrada y salida de usuarios.

#### Paso 1: Configuración de Pines y Botones
- Define pines de entrada para conectar botones de "Entrada" y "Salida".
```cpp
#define BUTTON_ENTRY 14
#define BUTTON_EXIT 27
```

- Configura los pines en el `setup()` del ESP32:
```cpp
void setup() {
    pinMode(BUTTON_ENTRY, INPUT_PULLUP);
    pinMode(BUTTON_EXIT, INPUT_PULLUP);
    // ... (Inicializar Serial para depuración)
}
```

#### Paso 2: Implementación de la Función de Registro de Acceso
- Añade una función en el loop que registre al usuario en la lista enlazada al presionar el botón de entrada y lo elimine con el botón de salida.

```cpp
LinkedList userList;

void loop() {
    if (digitalRead(BUTTON_ENTRY) == LOW) {
        userList.addUser(1, "Juan Perez", millis());
        delay(500); // Evitar múltiples lecturas
    }
    if (digitalRead(BUTTON_EXIT) == LOW) {
        userList.removeUser(1);
        delay(500);
    }
}
```

---

### Nivel 3: Autenticación Básica con PIN

**Objetivo:** Añadir un nivel de seguridad al sistema mediante autenticación básica.

#### Paso 1: Ampliación de la Clase `UserNode` para Incluir PIN
```cpp
struct UserNode {
    int id;
    String name;
    long timestamp;
    int pin; // Nuevo atributo de PIN
    UserNode* next;

    UserNode(int id, String name, long timestamp, int pin) 
        : id(id), name(name), timestamp(timestamp), pin(pin), next(nullptr) {}
};
```

#### Paso 2: Función de Autenticación
- Implementa una función de autenticación básica para verificar el PIN del usuario.

```cpp
bool authenticateUser(int id, int enteredPin) {
    UserNode* current = userList.head;
    while (current != nullptr) {
        if (current->id == id && current->pin == enteredPin) return true;
        current = current->next;
    }
    return false;
}
```

---

### Nivel 4: Almacenamiento Local y Exportación de Datos

**Objetivo:** Guardar los registros de acceso en el sistema de archivos del ESP32 (SPIFFS) y permitir su exportación.

#### Paso 1: Configuración de SPIFFS
```cpp
#include "SPIFFS.h"

void setup() {
    if (!SPIFFS.begin(true)) {
        Serial.println("Error al montar SPIFFS");
        return;
    }
}
```

#### Paso 2: Guardar y Exportar Datos
```cpp
void saveUserData(UserNode* user) {
    File file = SPIFFS.open("/data.txt", FILE_APPEND);
    if (!file) return;
    file.printf("ID: %d, Name: %s, Timestamp: %ld\n", user->id, user->name.c_str(), user->timestamp);
    file.close();
}
```

---

### Nivel 5: Conexión a Base de Datos Remota e Interfaz Web con React.js

**Objetivo:** Configurar el ESP32 para enviar datos a un servidor remoto y desarrollar una interfaz en React.js.

#### Paso 1: Configuración de WiFi y Envío de Datos HTTP
- Configura el WiFi en el ESP32 para conectarse a una red.
```cpp
#include <WiFi.h>
const char* ssid = "your-SSID";
const char* password = "your-PASSWORD";
WiFi.begin(ssid, password);
```

#### Paso 2: Interfaz en React.js para Visualización
Desarrolla una interfaz básica en React.js para mostrar el historial de acceso en tiempo real. Esta sección se ampliará en el tutorial.

---

## Conclusión
Al finalizar este tutorial, habrás aprendido a implementar LinkedList en un sistema de control de acceso, interactuar con botones, autenticar usuarios, y guardar/exportar datos en el ESP32. Además, habrás conectado el sistema a una base de datos remota y construido una interfaz en React.js para la visualización.