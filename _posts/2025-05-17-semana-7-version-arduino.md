---
title: "Semana 7: Implementación con Arduino - Elevador de 2 Pisos"
date: 2025-05-17T07:00:00-04:00
categories:
  - Circuitos Digitales
  - Guía del Instructor
tags:
  - Arduino
  - Proyecto Final
  - Microcontrolador
  - Programación
---

Esta es una versión modernizada del proyecto del elevador utilizando un Arduino en lugar de componentes discretos.

## Materiales Necesarios

### Hardware
- Arduino UNO o similar
- 2 LEDs rojos (indicadores de piso)
- 2 LEDs verdes (botones de llamada)
- 1 LED amarillo (emergencia)
- 5 botones pulsadores
- Resistencias 330Ω para LEDs
- Resistencias 10kΩ para botones
- Motor DC o Servo
- Cables dupont

### Conexiones
```cpp
// Definición de pines
#define BUTTON_FLOOR1    2  // Botón piso 1
#define BUTTON_FLOOR2    3  // Botón piso 2
#define BUTTON_EMERGENCY 4  // Botón emergencia
#define LED_FLOOR1      5  // LED indicador piso 1
#define LED_FLOOR2      6  // LED indicador piso 2
#define LED_EMERGENCY   7  // LED emergencia
#define MOTOR_PIN1      8  // Control motor
#define MOTOR_PIN2      9  // Control motor
```

## Código Completo Arduino

```cpp
// Incluir bibliotecas necesarias
#include <Arduino.h>

// Clase para manejar el debouncing de botones
class Button {
private:
    uint8_t pin;
    bool lastReading;
    unsigned long lastDebounceTime;
    const unsigned long DEBOUNCE_DELAY = 50;

public:
    Button(uint8_t buttonPin) : pin(buttonPin), lastReading(false), lastDebounceTime(0) {
        pinMode(pin, INPUT_PULLUP);
    }

    bool isPressed() {
        bool currentReading = !digitalRead(pin);  // Invertido por pull-up
        if (currentReading != lastReading) {
            lastDebounceTime = millis();
        }

        if ((millis() - lastDebounceTime) > DEBOUNCE_DELAY) {
            lastReading = currentReading;
            return currentReading;
        }
        return false;
    }
};

// Clase principal del elevador
class Elevator {
public:
    enum State {
        IDLE,
        MOVING_UP,
        MOVING_DOWN,
        EMERGENCY
    };

private:
    State currentState;
    uint8_t currentFloor;
    Button* floor1Button;
    Button* floor2Button;
    Button* emergencyButton;

public:
    Elevator() : 
        currentState(IDLE), 
        currentFloor(1) {
        // Configurar pines
        pinMode(LED_FLOOR1, OUTPUT);
        pinMode(LED_FLOOR2, OUTPUT);
        pinMode(LED_EMERGENCY, OUTPUT);
        pinMode(MOTOR_PIN1, OUTPUT);
        pinMode(MOTOR_PIN2, OUTPUT);

        // Crear botones
        floor1Button = new Button(BUTTON_FLOOR1);
        floor2Button = new Button(BUTTON_FLOOR2);
        emergencyButton = new Button(BUTTON_EMERGENCY);

        updateLEDs();
    }

    void update() {
        if (emergencyButton->isPressed()) {
            currentState = EMERGENCY;
            stopMotor();
            digitalWrite(LED_EMERGENCY, HIGH);
            return;
        }

        switch (currentState) {
            case IDLE:
                handleIdleState();
                break;
            case MOVING_UP:
                handleMovingUp();
                break;
            case MOVING_DOWN:
                handleMovingDown();
                break;
            case EMERGENCY:
                if (!emergencyButton->isPressed()) {
                    currentState = IDLE;
                    digitalWrite(LED_EMERGENCY, LOW);
                }
                break;
        }

        updateLEDs();
    }

private:
    void handleIdleState() {
        if (floor1Button->isPressed() && currentFloor != 1) {
            currentState = MOVING_DOWN;
            moveDown();
        }
        else if (floor2Button->isPressed() && currentFloor != 2) {
            currentState = MOVING_UP;
            moveUp();
        }
    }

    void handleMovingUp() {
        // Simular llegada al piso (en realidad necesitarías sensores)
        delay(2000);  // Tiempo de movimiento
        currentFloor = 2;
        currentState = IDLE;
        stopMotor();
    }

    void handleMovingDown() {
        // Simular llegada al piso (en realidad necesitarías sensores)
        delay(2000);  // Tiempo de movimiento
        currentFloor = 1;
        currentState = IDLE;
        stopMotor();
    }

    void moveUp() {
        digitalWrite(MOTOR_PIN1, HIGH);
        digitalWrite(MOTOR_PIN2, LOW);
    }

    void moveDown() {
        digitalWrite(MOTOR_PIN1, LOW);
        digitalWrite(MOTOR_PIN2, HIGH);
    }

    void stopMotor() {
        digitalWrite(MOTOR_PIN1, LOW);
        digitalWrite(MOTOR_PIN2, LOW);
    }

    void updateLEDs() {
        digitalWrite(LED_FLOOR1, currentFloor == 1);
        digitalWrite(LED_FLOOR2, currentFloor == 2);
    }
};

// Variables globales
Elevator* elevator;

void setup() {
    elevator = new Elevator();
}

void loop() {
    elevator->update();
    delay(10);  // Pequeña pausa para estabilidad
}
```

## Ventajas de esta Implementación

1. **Menos Componentes**
   - No necesitas flip-flops ni decodificadores
   - Circuito más simple y fácil de cablear
   - Menor probabilidad de errores de conexión

2. **Más Flexibilidad**
   - Fácil de modificar el comportamiento
   - Puedes añadir características adicionales
   - Depuración más simple con Serial.print()

3. **Mejor Mantenimiento**
   - Cambios de lógica sin recablear
   - Actualizaciones de software posibles
   - Diagnóstico de problemas más sencillo

## Extensiones Posibles

1. **Agregar Sensores**
```cpp
#define SENSOR_FLOOR1 10
#define SENSOR_FLOOR2 11

// En la clase Elevator
void handleMovingUp() {
    if (digitalRead(SENSOR_FLOOR2)) {
        currentFloor = 2;
        currentState = IDLE;
        stopMotor();
    }
}
```

2. **Display de 7 Segmentos**
```cpp
// Incluir biblioteca
#include <TM1637Display.h>

// En la clase Elevator
TM1637Display display(CLK_PIN, DIO_PIN);
void updateDisplay() {
    display.showNumberDec(currentFloor);
}
```

3. **Comunicación Serial**
```cpp
void debugPrint() {
    Serial.print("Estado: ");
    Serial.print(currentState);
    Serial.print(" Piso: ");
    Serial.println(currentFloor);
}
```

*Esta versión modernizada mantiene los conceptos fundamentales de máquinas de estado y control digital, pero los implementa de una manera más práctica y accesible para los estudiantes actuales.*
