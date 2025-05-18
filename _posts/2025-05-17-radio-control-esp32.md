---
title: "Radio Control para Dron ESP32"
date: 2025-05-17T14:30:00-04:00
categories:
  - Tutorial
  - Proyectos
tags:
  - ESP32
  - Drones
  - Radio Control
  - NRF24L01
---

Este tutorial explica cómo construir un radio control personalizado para el [micro dron FPV con ESP32]({% post_url 2025-05-17-micro-dron-esp32 %}). Usaremos otro ESP32 como transmisor y el módulo NRF24L01+ para la comunicación.

## Índice
- [Índice](#índice)
- [Lista de Materiales {#lista-de-materiales}](#lista-de-materiales-lista-de-materiales)
  - [Componentes Principales](#componentes-principales)
  - [Materiales Adicionales](#materiales-adicionales)
- [Esquema de Conexiones {#esquema-de-conexiones}](#esquema-de-conexiones-esquema-de-conexiones)
- [Firmware del Transmisor {#firmware-del-transmisor}](#firmware-del-transmisor-firmware-del-transmisor)
- [Protocolo de Comunicación {#protocolo-de-comunicación}](#protocolo-de-comunicación-protocolo-de-comunicación)
- [Calibración {#calibración}](#calibración-calibración)

## Lista de Materiales {#lista-de-materiales}

### Componentes Principales
- ESP32-WROOM-32D
- NRF24L01+ con regulador de voltaje
- 2x Joysticks analógicos
- Display OLED 128x64 (opcional)
- Batería LiPo 1S 3.7V
- Interruptor
- 4x Potenciómetros para trim

### Materiales Adicionales
- Caja impresa en 3D
- Cables
- Conectores
- PCB (opcional)

## Esquema de Conexiones {#esquema-de-conexiones}

| ESP32 Pin | Componente | Función |
|-----------|------------|----------|
| GPIO 36   | Joystick 1 | Throttle |
| GPIO 39   | Joystick 1 | Yaw      |
| GPIO 34   | Joystick 2 | Pitch    |
| GPIO 35   | Joystick 2 | Roll     |
| GPIO 25   | NRF24L01+  | CE       |
| GPIO 26   | NRF24L01+  | CSN      |
| GPIO 23   | NRF24L01+  | MOSI     |
| GPIO 19   | NRF24L01+  | MISO     |
| GPIO 18   | NRF24L01+  | SCK      |
| GPIO 21   | OLED       | SDA      |
| GPIO 22   | OLED       | SCL      |

## Firmware del Transmisor {#firmware-del-transmisor}

```cpp
#include <Wire.h>
#include <RF24.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <EEPROM.h>

// Estructura de datos de control
struct ControlData {
    float throttle;
    float roll;
    float pitch;
    float yaw;
} controls;

class RadioTransmitter {
private:
    RF24 radio;
    Adafruit_SSD1306 display;
    
    // Configuración de pines
    const int THROTTLE_PIN = 36;
    const int YAW_PIN = 39;
    const int PITCH_PIN = 34;
    const int ROLL_PIN = 35;
    
    // Calibración
    struct StickCalibration {
        int min;
        int center;
        int max;
        int deadzone;
    };
    
    StickCalibration sticks[4];  // throttle, yaw, pitch, roll

public:
    RadioTransmitter() : 
        radio(25, 26),  // CE, CSN
        display(128, 64, &Wire, -1)
    {
    }

    bool initialize() {
        // Inicializar display
        if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
            Serial.println("Display initialization failed");
            return false;
        }
        
        // Inicializar radio
        if (!radio.begin()) {
            Serial.println("Radio initialization failed");
            return false;
        }
        
        // Configurar radio
        radio.setPALevel(RF24_PA_LOW);
        radio.setDataRate(RF24_250KBPS);
        radio.openWritingPipe(0xF0F0F0F0E1LL);
        
        // Cargar calibración
        loadCalibration();
        
        return true;
    }

    void update() {
        // Leer valores de joysticks
        controls.throttle = readStick(THROTTLE_PIN, 0);
        controls.yaw = readStick(YAW_PIN, 1);
        controls.pitch = readStick(PITCH_PIN, 2);
        controls.roll = readStick(ROLL_PIN, 3);
        
        // Enviar datos
        radio.write(&controls, sizeof(controls));
        
        // Actualizar display
        updateDisplay();
    }

    void calibrate() {
        display.clearDisplay();
        display.setTextSize(1);
        display.setTextColor(SSD1306_WHITE);
        
        // Centros
        display.println("Center sticks");
        display.println("Press any key...");
        display.display();
        while(!Serial.available()) delay(10);
        Serial.read();
        
        captureStickCenters();
        
        // Límites
        display.clearDisplay();
        display.println("Move sticks to extremes");
        display.println("Press any key when done");
        display.display();
        while(!Serial.available()) delay(10);
        Serial.read();
        
        captureStickLimits();
        
        // Guardar
        saveCalibration();
    }

private:
    float readStick(int pin, int index) {
        int raw = analogRead(pin);
        
        // Aplicar deadzone
        if (abs(raw - sticks[index].center) < sticks[index].deadzone) {
            return 0.0f;
        }
        
        // Mapear a -1.0 a 1.0 (o 0.0 a 1.0 para throttle)
        if (index == 0) {  // throttle
            return map(raw, sticks[index].min, sticks[index].max, 0.0f, 1.0f);
        } else {
            if (raw < sticks[index].center) {
                return map(raw, sticks[index].min, sticks[index].center, -1.0f, 0.0f);
            } else {
                return map(raw, sticks[index].center, sticks[index].max, 0.0f, 1.0f);
            }
        }
    }

    void updateDisplay() {
        display.clearDisplay();
        display.setTextSize(1);
        display.setCursor(0,0);
        
        display.print("T:"); display.print(controls.throttle * 100);
        display.print(" Y:"); display.println(controls.yaw * 100);
        display.print("P:"); display.print(controls.pitch * 100);
        display.print(" R:"); display.println(controls.roll * 100);
        
        // Visualización gráfica
        drawStickPosition(64, 16, controls.roll, controls.pitch);  // Stick derecho
        drawStickPosition(64, 48, controls.yaw, controls.throttle);  // Stick izquierdo
        
        display.display();
    }

    void drawStickPosition(int centerX, int centerY, float x, float y) {
        display.drawCircle(centerX, centerY, 15, SSD1306_WHITE);
        display.fillCircle(
            centerX + (x * 14),
            centerY - (y * 14),
            2,
            SSD1306_WHITE
        );
    }

    void captureStickCenters() {
        for (int i = 0; i < 4; i++) {
            sticks[i].center = analogRead(i == 0 ? THROTTLE_PIN :
                                       i == 1 ? YAW_PIN :
                                       i == 2 ? PITCH_PIN : ROLL_PIN);
            sticks[i].deadzone = 100;  // Valor inicial de deadzone
        }
    }

    void captureStickLimits() {
        // Inicializar límites con valores centrales
        for (int i = 0; i < 4; i++) {
            sticks[i].min = sticks[i].center;
            sticks[i].max = sticks[i].center;
        }
        
        // Capturar por 10 segundos
        unsigned long startTime = millis();
        while (millis() - startTime < 10000) {
            updateStickLimits(0, analogRead(THROTTLE_PIN));
            updateStickLimits(1, analogRead(YAW_PIN));
            updateStickLimits(2, analogRead(PITCH_PIN));
            updateStickLimits(3, analogRead(ROLL_PIN));
            delay(10);
        }
    }

    void updateStickLimits(int index, int value) {
        if (value < sticks[index].min) sticks[index].min = value;
        if (value > sticks[index].max) sticks[index].max = value;
    }

    void saveCalibration() {
        EEPROM.begin(512);
        int addr = 0;
        for (int i = 0; i < 4; i++) {
            EEPROM.put(addr, sticks[i]);
            addr += sizeof(StickCalibration);
        }
        EEPROM.commit();
    }

    void loadCalibration() {
        EEPROM.begin(512);
        int addr = 0;
        for (int i = 0; i < 4; i++) {
            EEPROM.get(addr, sticks[i]);
            addr += sizeof(StickCalibration);
        }
    }
};
```

## Protocolo de Comunicación {#protocolo-de-comunicación}

La comunicación entre el transmisor y el receptor usa un protocolo simple:

1. **Paquete de Control**: 16 bytes
   - Throttle (float): 0.0 a 1.0
   - Roll (float): -1.0 a 1.0
   - Pitch (float): -1.0 a 1.0
   - Yaw (float): -1.0 a 1.0

2. **Frecuencia**: 50Hz
3. **Canal**: Configurable (por defecto 0xF0F0F0F0E1)
4. **Velocidad**: 250Kbps para mejor alcance

## Calibración {#calibración}

La calibración del radio control es importante para:

1. **Precisión**:
   - Determinar los valores máximos y mínimos reales de cada stick
   - Establecer los puntos centrales exactos
   - Configurar zonas muertas apropiadas

2. **Proceso de Calibración**:
   1. Encender el transmisor manteniendo un botón específico
   2. Centrar todos los sticks y confirmar
   3. Mover todos los sticks a sus extremos
   4. La calibración se guarda automáticamente en EEPROM

3. **Ajuste de Trim**:
   - Usar potenciómetros para ajuste fino
   - Los valores de trim se suman a las lecturas calibradas
   - También se guardan en EEPROM

Los valores de calibración se mantienen entre reinicios y solo necesitan recalibrarse si:
- Se cambian los joysticks
- Se observa deriva en los controles
- Se actualiza el firmware

La interfaz OLED muestra información útil durante la calibración y el uso normal:
- Posición actual de los sticks
- Valores numéricos de cada canal
- Estado de la batería
- Intensidad de señal
