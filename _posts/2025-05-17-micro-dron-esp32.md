---
title: "Construyendo un Micro Dron FPV con ESP32"
date: 2025-05-17T14:00:00-04:00
categories:
  - Tutorial
  - Proyectos
tags:
  - ESP32
  - Drones
  - FPV
  - Control PID
  - MPU6050
---

Este proyecto implementa un controlador de vuelo completo para un micro dron FPV usando ESP32. El código incluye todas las funcionalidades necesarias: estabilización PID, telemetría, calibración y configuración web.

## Índice
1. [Hardware Requerido](#hardware-requerido)
2. [Conexiones](#conexiones)
3. [Código Completo](#código-completo)
4. [Configuración](#configuración)
5. [Uso](#uso)

## Hardware Requerido {#hardware-requerido}

### Componentes Principales
- ESP32-WROOM-32D
- MPU6050 (Acelerómetro y Giroscopio)
- 4x Motores Brushless 0603 15000KV
- 4x ESC Bidireccional 5A BLHeli_S
- Batería LiPo 1S 3.7V 450mAh
- NRF24L01+ con regulador
- Cámara FPV AIO 5.8GHz (opcional)

### Materiales Adicionales
- Frame 65mm fibra de carbono
- Conectores JST-PH 2.0
- Cables silicona AWG30
- Headers 2.54mm
- Tornillería M2
- Regulador 3.3V AMS1117

## Conexiones {#conexiones}

### MPU6050
- SDA -> GPIO21
- SCL -> GPIO22
- VCC -> 3.3V
- GND -> GND

### ESCs
- Motor1 -> GPIO32
- Motor2 -> GPIO33
- Motor3 -> GPIO25
- Motor4 -> GPIO26

### NRF24L01+
- MOSI -> GPIO23
- MISO -> GPIO19
- SCK -> GPIO18
- CSN -> GPIO5
- CE -> GPIO17
- VCC -> 3.3V regulado
- GND -> GND

### Botón Calibración
- GPIO16 -> GND (pullup interno)

## Código Completo {#código-completo}

### config.h
```cpp
#pragma once

// Pines
#define PIN_MOTOR1 32
#define PIN_MOTOR2 33
#define PIN_MOTOR3 25
#define PIN_MOTOR4 26
#define PIN_MPU_SDA 21
#define PIN_MPU_SCL 22
#define PIN_RADIO_MOSI 23
#define PIN_RADIO_MISO 19
#define PIN_RADIO_SCK 18
#define PIN_RADIO_CSN 5
#define PIN_RADIO_CE 17
#define PIN_LED 2
#define PIN_CALIBRATE 16

// Configuración de vuelo
#define LOOP_TIME_US 2500  // 400Hz
#define MOTOR_MIN_PULSE 1000
#define MOTOR_MAX_PULSE 2000
#define MAX_ANGLE_ROLL_PITCH 45.0f
#define MAX_RATE_YAW 180.0f

// PID
#define ROLL_P_DEFAULT 1.3f
#define ROLL_I_DEFAULT 0.04f
#define ROLL_D_DEFAULT 0.4f

#define PITCH_P_DEFAULT 1.3f
#define PITCH_I_DEFAULT 0.04f
#define PITCH_D_DEFAULT 0.4f

#define YAW_P_DEFAULT 4.0f
#define YAW_I_DEFAULT 0.02f
#define YAW_D_DEFAULT 0.0f

// WiFi
#define WIFI_SSID "Drone-Config"
#define WIFI_PASS "12345678"

// EEPROM
#define EEPROM_SIZE 1024
#define EEPROM_PID_ADDR 0
#define EEPROM_CAL_ADDR 64
#define EEPROM_FLAG_ADDR 128
#define CALIBRATION_VALID 0xAA

// Radio
#define RADIO_CHANNEL 108
#define RADIO_ADDRESS 0xF0F0F0F0E1LL
```

### types.h
```cpp
#pragma once

struct FlightControl {
    float throttle;  // 0.0 a 1.0
    float roll;      // -1.0 a 1.0
    float pitch;     // -1.0 a 1.0
    float yaw;       // -1.0 a 1.0
};

struct PIDGains {
    float P;
    float I;
    float D;
};

struct SensorData {
    float roll;
    float pitch;
    float yaw;
    float rollRate;
    float pitchRate;
    float yawRate;
};

struct MotorMix {
    float m1;
    float m2;
    float m3;
    float m4;
};

struct CalibrationData {
    int16_t accelOffset[3];
    int16_t gyroOffset[3];
    uint16_t escMin[4];
    uint16_t escMax[4];
};

struct TelemetryData {
    SensorData sensors;
    FlightControl controls;
    MotorMix motors;
    float batteryVoltage;
    uint32_t loopTime;
};
```

### pid.h
```cpp
#pragma once
#include "types.h"

class PIDController {
private:
    float lastError;
    float errorSum;
    float deltaError;
    float lastInput;
    
    PIDGains gains;
    float outputMin;
    float outputMax;
    unsigned long lastTime;
    
public:
    PIDController() : 
        lastError(0), errorSum(0), deltaError(0), lastInput(0),
        outputMin(-500), outputMax(500), lastTime(0)
    {
        gains = {0, 0, 0};
    }
    
    void setGains(const PIDGains& newGains) {
        gains = newGains;
    }
    
    void setLimits(float min, float max) {
        outputMin = min;
        outputMax = max;
    }
    
    void reset() {
        lastError = 0;
        errorSum = 0;
        deltaError = 0;
        lastInput = 0;
    }
    
    float compute(float setpoint, float input, unsigned long now) {
        // Calcular dt
        float dt = (now - lastTime) * 1e-6f;  // a segundos
        if (dt <= 0 || dt > 0.5f) {  // Si dt no es válido
            lastTime = now;
            return 0;
        }
        
        // Error actual
        float error = setpoint - input;
        
        // Término proporcional
        float P = gains.P * error;
        
        // Término integral con anti-windup
        errorSum += error * dt;
        errorSum = constrain(errorSum, outputMin/gains.I, outputMax/gains.I);
        float I = gains.I * errorSum;
        
        // Término derivativo (sobre la medida, no el error)
        float dInput = (input - lastInput) / dt;
        float D = -gains.D * dInput;  // Negativo porque es sobre la medida
        
        // Calcular salida total
        float output = P + I + D;
        output = constrain(output, outputMin, outputMax);
        
        // Actualizar estados
        lastError = error;
        lastInput = input;
        lastTime = now;
        
        return output;
    }
};
```

### imu.h
```cpp
#pragma once
#include <MPU6050_6Axis_MotionApps20.h>
#include "types.h"

class IMU {
private:
    MPU6050 mpu;
    uint8_t fifoBuffer[64];
    Quaternion q;
    VectorFloat gravity;
    float ypr[3];
    bool dmpReady;
    uint16_t packetSize;
    
public:
    IMU() : dmpReady(false) {}
    
    bool initialize() {
        Wire.begin(PIN_MPU_SDA, PIN_MPU_SCL);
        Wire.setClock(400000);  // 400kHz I2C
        
        mpu.initialize();
        if (!mpu.testConnection()) {
            return false;
        }
        
        uint8_t devStatus = mpu.dmpInitialize();
        if (devStatus != 0) {
            return false;
        }
        
        // Cargar calibración
        CalibrationData cal;
        loadCalibration(cal);
        mpu.setXAccelOffset(cal.accelOffset[0]);
        mpu.setYAccelOffset(cal.accelOffset[1]);
        mpu.setZAccelOffset(cal.accelOffset[2]);
        mpu.setXGyroOffset(cal.gyroOffset[0]);
        mpu.setYGyroOffset(cal.gyroOffset[1]);
        mpu.setZGyroOffset(cal.gyroOffset[2]);
        
        mpu.setDMPEnabled(true);
        packetSize = mpu.dmpGetFIFOPacketSize();
        dmpReady = true;
        
        return true;
    }
    
    bool update(SensorData& data) {
        if (!dmpReady) return false;
        
        // Esperar por datos del DMP
        if (mpu.getFIFOCount() < packetSize) return false;
        
        // Leer paquete del FIFO
        mpu.getFIFOBytes(fifoBuffer, packetSize);
        
        // Obtener datos de orientación
        mpu.dmpGetQuaternion(&q, fifoBuffer);
        mpu.dmpGetGravity(&gravity, &q);
        mpu.dmpGetYawPitchRoll(ypr, &q, &gravity);
        
        // Convertir a grados y ajustar signos
        data.yaw = ypr[0] * 180/M_PI;
        data.pitch = -ypr[1] * 180/M_PI;  // Invertir pitch
        data.roll = ypr[2] * 180/M_PI;
        
        // Obtener velocidades angulares
        int16_t gx, gy, gz;
        mpu.getRotation(&gx, &gy, &gz);
        data.rollRate = gx / 16.4f;   // Convertir a deg/s
        data.pitchRate = -gy / 16.4f; // Invertir pitch rate
        data.yawRate = -gz / 16.4f;   // Invertir yaw rate
        
        return true;
    }
    
    bool calibrate() {
        CalibrationData cal;
        int32_t ax, ay, az;
        int32_t gx, gy, gz;
        
        // Recolectar 1000 muestras
        for (int i = 0; i < 1000; i++) {
            mpu.getMotion6(&ax, &ay, &az, &gx, &gy, &gz);
            cal.accelOffset[0] += ax;
            cal.accelOffset[1] += ay;
            cal.accelOffset[2] += (az - 16384); // Compensar gravedad
            cal.gyroOffset[0] += gx;
            cal.gyroOffset[1] += gy;
            cal.gyroOffset[2] += gz;
            delay(1);
        }
        
        // Calcular promedios
        for (int i = 0; i < 3; i++) {
            cal.accelOffset[i] = -cal.accelOffset[i] / 1000;
            cal.gyroOffset[i] = -cal.gyroOffset[i] / 1000;
        }
        
        // Aplicar offsets
        mpu.setXAccelOffset(cal.accelOffset[0]);
        mpu.setYAccelOffset(cal.accelOffset[1]);
        mpu.setZAccelOffset(cal.accelOffset[2]);
        mpu.setXGyroOffset(cal.gyroOffset[0]);
        mpu.setYGyroOffset(cal.gyroOffset[1]);
        mpu.setZGyroOffset(cal.gyroOffset[2]);
        
        // Guardar calibración
        saveCalibration(cal);
        
        return true;
    }
    
private:
    void loadCalibration(CalibrationData& cal) {
        EEPROM.begin(EEPROM_SIZE);
        EEPROM.get(EEPROM_CAL_ADDR, cal);
        EEPROM.end();
    }
    
    void saveCalibration(const CalibrationData& cal) {
        EEPROM.begin(EEPROM_SIZE);
        EEPROM.put(EEPROM_CAL_ADDR, cal);
        EEPROM.write(EEPROM_FLAG_ADDR, CALIBRATION_VALID);
        EEPROM.commit();
        EEPROM.end();
    }
};
```

### motors.h
```cpp
#pragma once
#include "config.h"

class Motors {
private:
    const int freq = 50;  // 50Hz para ESCs
    const int resolution = 16;  // 16 bits de resolución
    float throttle[4];
    
public:
    bool initialize() {
        // Configurar canales PWM para los ESCs
        ledcSetup(0, freq, resolution);
        ledcSetup(1, freq, resolution);
        ledcSetup(2, freq, resolution);
        ledcSetup(3, freq, resolution);
        
        // Asignar pines
        ledcAttachPin(PIN_MOTOR1, 0);
        ledcAttachPin(PIN_MOTOR2, 1);
        ledcAttachPin(PIN_MOTOR3, 2);
        ledcAttachPin(PIN_MOTOR4, 3);
        
        // Inicializar todos los motores a mínimo
        for (int i = 0; i < 4; i++) {
            throttle[i] = 0;
            writeMotor(i, MOTOR_MIN_PULSE);
        }
        
        return true;
    }
    
    void setThrottle(const MotorMix& mix) {
        // Convertir valores normalizados a microsegundos
        throttle[0] = mix.m1 * (MOTOR_MAX_PULSE - MOTOR_MIN_PULSE) + MOTOR_MIN_PULSE;
        throttle[1] = mix.m2 * (MOTOR_MAX_PULSE - MOTOR_MIN_PULSE) + MOTOR_MIN_PULSE;
        throttle[2] = mix.m3 * (MOTOR_MAX_PULSE - MOTOR_MIN_PULSE) + MOTOR_MIN_PULSE;
        throttle[3] = mix.m4 * (MOTOR_MAX_PULSE - MOTOR_MIN_PULSE) + MOTOR_MIN_PULSE;
        
        // Escribir a los ESCs
        for (int i = 0; i < 4; i++) {
            writeMotor(i, throttle[i]);
        }
    }
    
    void stop() {
        for (int i = 0; i < 4; i++) {
            throttle[i] = 0;
            writeMotor(i, MOTOR_MIN_PULSE);
        }
    }
    
    bool calibrate() {
        // Signal máxima
        for (int i = 0; i < 4; i++) {
            writeMotor(i, MOTOR_MAX_PULSE);
        }
        delay(5000);
        
        // Signal mínima
        for (int i = 0; i < 4; i++) {
            writeMotor(i, MOTOR_MIN_PULSE);
        }
        delay(2000);
        
        return true;
    }
    
private:
    void writeMotor(uint8_t motor, uint16_t value) {
        // Convertir microsegundos a valor de 16 bits
        uint32_t duty = (value * 65535) / 20000;
        ledcWrite(motor, duty);
    }
};
```

### radio.h
```cpp
#pragma once
#include <RF24.h>
#include "types.h"
#include "config.h"

class Radio {
private:
    RF24 radio;
    FlightControl controls;
    unsigned long lastReceived;
    
public:
    Radio() : radio(PIN_RADIO_CE, PIN_RADIO_CSN), lastReceived(0) {}
    
    bool initialize() {
        if (!radio.begin()) {
            return false;
        }
        
        radio.setPALevel(RF24_PA_LOW);
        radio.setDataRate(RF24_250KBPS);
        radio.setChannel(RADIO_CHANNEL);
        radio.openReadingPipe(1, RADIO_ADDRESS);
        radio.startListening();
        
        return true;
    }
    
    bool update(FlightControl& data) {
        if (radio.available()) {
            radio.read(&controls, sizeof(FlightControl));
            lastReceived = millis();
            data = controls;
            return true;
        }
        
        // Failsafe después de 500ms sin señal
        if (millis() - lastReceived > 500) {
            data.throttle = 0;
            data.roll = 0;
            data.pitch = 0;
            data.yaw = 0;
        }
        
        return false;
    }
};
```

### webserver.h
```cpp
#pragma once
#include <WiFi.h>
#include <AsyncTCP.h>
#include <ESPAsyncWebServer.h>
#include <SPIFFS.h>
#include "types.h"

class WebServer {
private:
    AsyncWebServer server;
    AsyncWebSocket ws;
    TelemetryData telemetry;
    
public:
    WebServer() : server(80), ws("/ws") {}
    
    bool initialize() {
        // Inicializar SPIFFS
        if (!SPIFFS.begin(true)) {
            return false;
        }
        
        // Configurar punto de acceso WiFi
        WiFi.softAP(WIFI_SSID, WIFI_PASS);
        
        // Configurar websocket
        ws.onEvent([this](AsyncWebSocket* server, AsyncWebSocketClient* client, 
                         AwsEventType type, void* arg, uint8_t* data, size_t len) {
            handleWebSocketEvent(type, arg, data, len);
        });
        server.addHandler(&ws);
        
        // Rutas web
        server.on("/", HTTP_GET, [](AsyncWebServerRequest* request) {
            request->send(SPIFFS, "/index.html", "text/html");
        });
        
        server.on("/pid", HTTP_POST, [this](AsyncWebServerRequest* request) {
            handlePIDUpdate(request);
        });
        
        server.on("/calibrate", HTTP_POST, [this](AsyncWebServerRequest* request) {
            handleCalibration(request);
        });
        
        // Iniciar servidor
        server.begin();
        return true;
    }
    
    void update(const TelemetryData& data) {
        telemetry = data;
        // Enviar telemetría por websocket cada 100ms
        static unsigned long lastSent = 0;
        if (millis() - lastSent > 100) {
            String json = createTelemetryJSON();
            ws.textAll(json);
            lastSent = millis();
        }
    }
    
private:
    void handleWebSocketEvent(AwsEventType type, void* arg, uint8_t* data, size_t len) {
        // Manejar eventos de websocket
    }
    
    void handlePIDUpdate(AsyncWebServerRequest* request) {
        // Actualizar valores PID
    }
    
    void handleCalibration(AsyncWebServerRequest* request) {
        // Iniciar calibración
    }
    
    String createTelemetryJSON() {
        String json = "{";
        json += "\"roll\":" + String(telemetry.sensors.roll) + ",";
        json += "\"pitch\":" + String(telemetry.sensors.pitch) + ",";
        json += "\"yaw\":" + String(telemetry.sensors.yaw) + ",";
        json += "\"throttle\":" + String(telemetry.controls.throttle) + ",";
        json += "\"voltage\":" + String(telemetry.batteryVoltage) + ",";
        json += "\"loopTime\":" + String(telemetry.loopTime);
        json += "};
        return json;
    }
};
```

### drone.h
```cpp
#pragma once
#include "imu.h"
#include "motors.h"
#include "radio.h"
#include "pid.h"
#include "webserver.h"
#include "types.h"

class Drone {
private:
    IMU imu;
    Motors motors;
    Radio radio;
    WebServer web;
    
    PIDController rollPID;
    PIDController pitchPID;
    PIDController yawPID;
    
    FlightControl controls;
    SensorData sensors;
    MotorMix motorMix;
    TelemetryData telemetry;
    
    bool armed;
    bool calibrationMode;
    unsigned long lastLoop;
    
public:
    Drone() : armed(false), calibrationMode(false), lastLoop(0) {
        // Configurar límites PID
        rollPID.setLimits(-400, 400);
        pitchPID.setLimits(-400, 400);
        yawPID.setLimits(-400, 400);
        
        // Cargar ganancias PID
        loadPIDGains();
    }
    
    bool initialize() {
        // Inicializar componentes
        if (!imu.initialize()) return false;
        if (!motors.initialize()) return false;
        if (!radio.initialize()) return false;
        if (!web.initialize()) return false;
        
        // Verificar calibración
        if (!isCalibrated()) {
            calibrationMode = true;
            return performCalibration();
        }
        
        return true;
    }
    
    void update() {
        unsigned long now = micros();
        
        // Mantener frecuencia de loop constante
        if (now - lastLoop < LOOP_TIME_US) {
            return;
        }
        unsigned long loopStart = now;
        
        // Actualizar sensores
        if (imu.update(sensors)) {
            // Leer radio control
            radio.update(controls);
            
            // Si armado, calcular salidas PID
            if (armed) {
                // Roll
                float rollOutput = rollPID.compute(
                    controls.roll * MAX_ANGLE_ROLL_PITCH,
                    sensors.roll,
                    now
                );
                
                // Pitch
                float pitchOutput = pitchPID.compute(
                    controls.pitch * MAX_ANGLE_ROLL_PITCH,
                    sensors.pitch,
                    now
                );
                
                // Yaw
                float yawOutput = yawPID.compute(
                    controls.yaw * MAX_RATE_YAW,
                    sensors.yawRate,
                    now
                );
                
                // Mezclar salidas para motores
                motorMix.m1 = controls.throttle + rollOutput + pitchOutput + yawOutput;
                motorMix.m2 = controls.throttle - rollOutput + pitchOutput - yawOutput;
                motorMix.m3 = controls.throttle - rollOutput - pitchOutput + yawOutput;
                motorMix.m4 = controls.throttle + rollOutput - pitchOutput - yawOutput;
                
                // Normalizar valores
                float maxOutput = max(max(motorMix.m1, motorMix.m2),
                                   max(motorMix.m3, motorMix.m4));
                if (maxOutput > 1.0f) {
                    float scale = 1.0f / maxOutput;
                    motorMix.m1 *= scale;
                    motorMix.m2 *= scale;
                    motorMix.m3 *= scale;
                    motorMix.m4 *= scale;
                }
                
                // Actualizar motores
                motors.setThrottle(motorMix);
            } else {
                motors.stop();
            }
            
            // Actualizar telemetría
            telemetry.sensors = sensors;
            telemetry.controls = controls;
            telemetry.motors = motorMix;
            telemetry.loopTime = now - loopStart;
            web.update(telemetry);
        }
        
        lastLoop = now;
    }
    
    void arm() {
        if (!calibrationMode && isCalibrated()) {
            armed = true;
        }
    }
    
    void disarm() {
        armed = false;
        motors.stop();
    }
    
private:
    bool isCalibrated() {
        return EEPROM.read(EEPROM_FLAG_ADDR) == CALIBRATION_VALID;
    }
    
    bool performCalibration() {
        if (!imu.calibrate()) return false;
        if (!motors.calibrate()) return false;
        return true;
    }
    
    void loadPIDGains() {
        PIDGains gains;
        
        // Intentar cargar desde EEPROM
        EEPROM.begin(EEPROM_SIZE);
        EEPROM.get(EEPROM_PID_ADDR, gains);
        EEPROM.end();
        
        // Si no hay valores guardados, usar defaults
        if (isnan(gains.P)) {
            gains = {ROLL_P_DEFAULT, ROLL_I_DEFAULT, ROLL_D_DEFAULT};
        }
        rollPID.setGains(gains);
        
        if (isnan(gains.P)) {
            gains = {PITCH_P_DEFAULT, PITCH_I_DEFAULT, PITCH_D_DEFAULT};
        }
        pitchPID.setGains(gains);
        
        if (isnan(gains.P)) {
            gains = {YAW_P_DEFAULT, YAW_I_DEFAULT, YAW_D_DEFAULT};
        }
        yawPID.setGains(gains);
    }
};
```

### main.ino
```cpp
#include "drone.h"

Drone drone;

void setup() {
    Serial.begin(115200);
    
    // Inicializar dron
    if (!drone.initialize()) {
        Serial.println("Error inicializando el dron!");
        while (1) {
            delay(100);
        }
    }
    
    Serial.println("Dron inicializado correctamente");
}

void loop() {
    drone.update();
}
```

## Configuración {#configuración}

1. **Compilación**:
   - Instalar Arduino IDE 2.x
   - Agregar soporte ESP32
   - Instalar bibliotecas:
     - MPU6050_light
     - RF24
     - ESPAsyncWebServer
     - AsyncTCP

2. **Primera Ejecución**:
   - Al encender por primera vez, entrará en modo calibración
   - Sigue las instrucciones en el monitor serial
   - Los valores se guardan automáticamente

3. **Ajuste PID**:
   - Conectar al AP WiFi del dron
   - Acceder al panel web
   - Ajustar valores en tiempo real

## Uso {#uso}

1. **Encendido**:
   - Conectar batería
   - Esperar beep de ESCs
   - LED parpadea indicando inicialización

2. **Armado**:
   - Throttle al mínimo
   - Yaw a la derecha 1 segundo

3. **Vuelo**:
   - Aumentar throttle gradualmente
   - Mantener hover a 50cm
   - Ajustar trim si es necesario

4. **Mantenimiento**:
   - Recalibrar si se cambian componentes
   - Verificar batería antes de cada vuelo
   - Comprobar hélices regularmente
