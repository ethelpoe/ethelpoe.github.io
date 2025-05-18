---
title: "Semana 8: Pantalla Holográfica POV - Proyecto Final"
date: 2025-05-17T08:00:00-04:00
categories:
  - Circuitos Digitales
  - Proyecto Final
tags:
  - POV Display
  - Arduino
  - LED
  - Motores
  - Timing
---

Este proyecto final integra conceptos de circuitos secuenciales, timing y programación para crear una pantalla holográfica basada en el fenómeno de persistencia de la visión (POV).

## Video Demostrativo

[Ver en YouTube](https://www.youtube.com/watch?v=RXTmUNzOTNc)

## Fundamentos Teóricos

### Persistencia de la Visión (POV)
- El ojo humano retiene una imagen durante ~1/16 segundos
- A más de 24 FPS, percibimos movimiento continuo
- Aprovechamos esto para crear imágenes "flotantes"

### Conceptos Clave
1. **Timing Preciso**:
   - Control de velocidad del motor
   - Sincronización de LEDs
   - Muestreo de posición

2. **Multiplexación**:
   - Control individual de LEDs
   - Patrones de encendido/apagado
   - Generación de imágenes

## Materiales Necesarios

### Hardware Base
- Arduino Nano/UNO
- Motor DC 12V (~1800 RPM)
- 8 LEDs RGB (WS2812B)
- Sensor Hall
- Imán pequeño
- PCB circular o stripboard
- Slip ring (4 canales mínimo)
- Rodamiento central
- Base estable

### Componentes Adicionales
- Fuente 12V 2A
- Regulador 5V (7805)
- Capacitores (100µF, 100nF)
- Resistencias (330Ω, 10kΩ)
- Transistor MOSFET (control motor)
- Cables y conectores

## Implementación por Etapas

### 1. Base Mecánica
```cpp
// Dimensiones recomendadas
#define RADIO_BASE 100      // mm
#define LARGO_BRAZO 150     // mm
#define ESPACIADO_LED 15    // mm
#define PESO_BALANCE 50     // g
```

### 2. Control de Motor y Timing
```cpp
// Control básico de velocidad y timing
#define MOTOR_PIN 9
#define HALL_PIN 2
#define TARGET_RPM 1800

volatile unsigned long lastRevolution = 0;
volatile float currentRPM = 0;

void setupMotorControl() {
    pinMode(MOTOR_PIN, OUTPUT);
    pinMode(HALL_PIN, INPUT_PULLUP);
    attachInterrupt(digitalPinToInterrupt(HALL_PIN), 
                   revolutionCounter, FALLING);
}

void revolutionCounter() {
    unsigned long currentTime = micros();
    currentRPM = 60000000.0 / (currentTime - lastRevolution);
    lastRevolution = currentTime;
}

void adjustMotorSpeed() {
    int pwmValue = map(currentRPM, 0, 2000, 255, 0);
    analogWrite(MOTOR_PIN, pwmValue);
}
```

### 3. Control de LEDs
```cpp
#include <FastLED.h>

#define NUM_LEDS 8
#define LED_PIN 6
#define BRIGHTNESS 200

CRGB leds[NUM_LEDS];
const int DISPLAY_COLUMNS = 120;  // Resolución circular
uint8_t displayBuffer[DISPLAY_COLUMNS];

void setupLEDs() {
    FastLED.addLeds<WS2812B, LED_PIN, GRB>(leds, NUM_LEDS);
    FastLED.setBrightness(BRIGHTNESS);
}

void displayPattern(uint8_t position) {
    for (int i = 0; i < NUM_LEDS; i++) {
        if (displayBuffer[(position + i) % DISPLAY_COLUMNS]) {
            leds[i] = CRGB::Red;  // O cualquier color
        } else {
            leds[i] = CRGB::Black;
        }
    }
    FastLED.show();
}
```

### 4. Generación de Patrones
```cpp
// Estructura para almacenar imágenes
struct POVPattern {
    const uint8_t* pattern;
    uint8_t width;
    uint8_t height;
};

// Ejemplo de patrón simple (corazón)
const uint8_t PROGMEM heartPattern[] = {
    0b01101100,
    0b11111110,
    0b11111110,
    0b01111100,
    0b00111000,
    0b00010000
};

POVPattern heart = {heartPattern, 8, 6};

void loadPattern(const POVPattern& pattern) {
    // Cargar patrón en displayBuffer
    for (int col = 0; col < pattern.width; col++) {
        for (int row = 0; row < pattern.height; row++) {
            bool pixel = pgm_read_byte(&pattern.pattern[row]) & (1 << (7-col));
            displayBuffer[col] = pixel ? 255 : 0;
        }
    }
}
```

## Integración del Sistema

### Diagrama de Conexiones
```
[Arduino]
D2 -----> Sensor Hall
D6 -----> Data LEDs
D9 -----> MOSFET Gate (Motor)
5V -----> LEDs VCC
GND ----> Común

[Fuente 12V]
+12V ---> Motor (+)
GND ----> Motor (-)
```

### Código Principal
```cpp
void setup() {
    setupMotorControl();
    setupLEDs();
    loadPattern(heart);
}

void loop() {
    static uint8_t currentPosition = 0;
    
    if (currentRPM >= TARGET_RPM * 0.95) {
        displayPattern(currentPosition);
        currentPosition = (currentPosition + 1) % DISPLAY_COLUMNS;
    }
    
    adjustMotorSpeed();
}
```

## Calibración y Ajustes

### 1. Balanceo Mecánico
- Usar contrapesos
- Verificar vibraciones
- Ajustar centro de masa

### 2. Timing
```cpp
// Ajuste fino de timing
void calculateTiming() {
    float revolutionTime = 60000000.0 / currentRPM; // en microsegundos
    float anglePerColumn = 360.0 / DISPLAY_COLUMNS;
    float timePerColumn = revolutionTime / DISPLAY_COLUMNS;
    
    // Ajustar delays según sea necesario
    delayMicroseconds(timePerColumn);
}
```

### 3. Brillo y Contraste
```cpp
// Ajuste de brillo según velocidad
void adjustBrightness() {
    uint8_t dynamicBrightness = map(currentRPM, 
                                   1000, 2000, 
                                   100, 255);
    FastLED.setBrightness(dynamicBrightness);
}
```

## Depuración y Solución de Problemas

### Problemas Comunes

1. **Imagen Borrosa**
   - Verificar RPM
   - Ajustar timing
   - Revisar balanceo

2. **Patrones Incorrectos**
   - Calibrar sensor Hall
   - Verificar sincronización
   - Revisar buffer de datos

3. **Vibraciones**
   - Mejorar balanceo
   - Verificar rodamiento
   - Ajustar base

## Extensiones del Proyecto

### 1. Múltiples Capas
```cpp
#define LAYER_COUNT 3
struct Layer {
    POVPattern pattern;
    CRGB color;
    uint8_t offset;
};
```

### 2. Efectos Animados
```cpp
void animatePattern() {
    static uint8_t frame = 0;
    loadPattern(animations[frame]);
    frame = (frame + 1) % ANIMATION_FRAMES;
}
```

### 3. Control Bluetooth
```cpp
void setupBluetooth() {
    Serial1.begin(9600);
    // Comandos para cambiar patrones
    // Control de velocidad
    // Cambio de colores
}
```

## Evaluación del Proyecto

### Criterios Técnicos (50%)
1. Estabilidad mecánica
2. Calidad de imagen
3. Precisión de timing
4. Implementación de código

### Criterios de Diseño (30%)
1. Originalidad de patrones
2. Uso efectivo del color
3. Interfaz de usuario
4. Documentación

### Presentación (20%)
1. Demostración funcional
2. Explicación técnica
3. Manejo de preguntas
4. Material visual

## Documentación Requerida

### 1. Informe Técnico
- Diseño mecánico
- Esquemáticos
- Código comentado
- Pruebas realizadas

### 2. Manual de Usuario
- Instrucciones de montaje
- Guía de operación
- Mantenimiento
- Solución de problemas

### 3. Presentación
- Video demostrativo
- Diagramas explicativos
- Resultados obtenidos
- Mejoras propuestas

*Este proyecto final permite a los estudiantes integrar conocimientos de electrónica digital, programación y mecánica, mientras crean un dispositivo visualmente impactante que demuestra principios fundamentales de la persistencia de la visión.*
