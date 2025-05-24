---
title: Protocolos de Comunicación en Domótica
author: ethelpoe
date: 2025-05-24 11:00:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - Domotica
  - Protocolos
  - IoT
pin: false
---

# PROTOCOLOS DE COMUNICACIÓN EN DOMÓTICA
## Implementaciones Prácticas y Ejemplos

## 1. PROTOCOLOS BASADOS EN HTTP/HTTPS

### Ejemplo básico con ESP32 y API REST
```cpp
#include <WiFi.h>
#include <HTTPClient.h>
#include <ArduinoJson.h>

const char* ssid = "TuRedWiFi";
const char* password = "TuPassword";

void setup() {
    Serial.begin(115200);
    WiFi.begin(ssid, password);
    
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    Serial.println("Conectado al WiFi");
}

void loop() {
    if(WiFi.status() == WL_CONNECTED) {
        HTTPClient http;
        
        // Ejemplo de GET
        http.begin("https://api.domotica.com/sensores");
        int httpCode = http.GET();
        
        if(httpCode > 0) {
            String payload = http.getString();
            Serial.println(payload);
        }
        
        http.end();
    }
    delay(5000);
}
```

### Ejemplo de API REST para Control de Luces
```python
from flask import Flask, jsonify, request
app = Flask(__name__)

# Simulación de estado de luces
luces = {
    "sala": False,
    "cocina": False,
    "dormitorio": False
}

@app.route('/luces', methods=['GET'])
def obtener_estado():
    return jsonify(luces)

@app.route('/luces/<habitacion>', methods=['POST'])
def cambiar_estado(habitacion):
    if habitacion in luces:
        luces[habitacion] = not luces[habitacion]
        return jsonify({"status": "success", "estado": luces[habitacion]})
    return jsonify({"error": "Habitación no encontrada"}), 404

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

## 2. IMPLEMENTACIÓN CON WEBSOCKETS

### Servidor WebSocket para Tiempo Real
```javascript
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', function connection(ws) {
    console.log('Nueva conexión establecida');
    
    ws.on('message', function incoming(message) {
        console.log('received: %s', message);
        
        // Broadcast a todos los clientes
        wss.clients.forEach(function each(client) {
            if (client.readyState === WebSocket.OPEN) {
                client.send(message);
            }
        });
    });
});
```

### Cliente WebSocket en ESP32
```cpp
#include <WiFi.h>
#include <WebSocketsClient.h>

WebSocketsClient webSocket;

void webSocketEvent(WStype_t type, uint8_t * payload, size_t length) {
    switch(type) {
        case WStype_DISCONNECTED:
            Serial.println("Desconectado!");
            break;
        case WStype_CONNECTED:
            Serial.println("Conectado!");
            break;
        case WStype_TEXT:
            Serial.printf("Mensaje recibido: %s\n", payload);
            break;
    }
}

void setup() {
    Serial.begin(115200);
    WiFi.begin("TuRedWiFi", "TuPassword");
    
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    
    webSocket.begin("192.168.1.100", 8080, "/");
    webSocket.onEvent(webSocketEvent);
    webSocket.setReconnectInterval(5000);
}

void loop() {
    webSocket.loop();
}
```

## 3. MQTT EN DOMÓTICA

### Ejemplo de Publicador MQTT
```cpp
#include <WiFi.h>
#include <PubSubClient.h>

WiFiClient espClient;
PubSubClient client(espClient);

void setup() {
    Serial.begin(115200);
    WiFi.begin("TuRedWiFi", "TuPassword");
    
    client.setServer("broker.mqtt.com", 1883);
    client.setCallback(callback);
}

void callback(char* topic, byte* payload, unsigned int length) {
    String mensaje = "";
    for (int i = 0; i < length; i++) {
        mensaje += (char)payload[i];
    }
    Serial.println("Mensaje recibido: " + mensaje);
}

void loop() {
    if (!client.connected()) {
        reconnect();
    }
    client.loop();
    
    // Publicar temperatura cada 30 segundos
    static unsigned long ultimaPublicacion = 0;
    if (millis() - ultimaPublicacion > 30000) {
        float temperatura = 25.5; // Ejemplo
        client.publish("casa/temperatura", String(temperatura).c_str());
        ultimaPublicacion = millis();
    }
}
```

## COMPARATIVA DE PROTOCOLOS

### 1. Protocolos Inalámbricos
| Protocolo | Ventajas | Desventajas | Uso Recomendado |
|-----------|----------|-------------|-----------------|
| Wi-Fi     | Alta velocidad, Compatible | Alto consumo | Streaming, Control |
| Zigbee    | Bajo consumo, Mesh | Alcance limitado | Sensores |
| Z-Wave    | Buena interoperabilidad | Propietario | Control de hogar |
| Bluetooth | Simple, ubicuo | Alcance corto | Dispositivos cercanos |

### 2. Protocolos IP
| Protocolo | Ventajas | Desventajas | Uso Recomendado |
|-----------|----------|-------------|-----------------|
| HTTP      | Universal, Simple | Overhead alto | APIs, Config |
| MQTT      | Ligero, Eficiente | Requiere broker | IoT, Sensores |
| WebSocket | Tiempo real | Más complejo | Updates en vivo |
| CoAP      | Ligero, UDP | Menos soportado | IoT restringido |

## EJEMPLOS DE CASOS DE USO

### 1. Sistema de Monitoreo de Temperatura
- **Protocolo**: MQTT
- **Razón**: Eficiente para datos periódicos
- **Implementación**: Sensores publican en tópicos como "casa/habitacion1/temperatura"

### 2. Control de Iluminación
- **Protocolo**: HTTP/REST
- **Razón**: Simple para comandos ocasionales
- **Implementación**: API endpoints para encender/apagar luces

### 3. Cámaras de Seguridad
- **Protocolo**: WebSocket
- **Razón**: Streaming en tiempo real
- **Implementación**: Video y alertas en tiempo real

## CONSIDERACIONES DE SEGURIDAD

1. **Para HTTP/HTTPS**
   - Usar siempre HTTPS
   - Implementar autenticación JWT
   - Validar inputs
   - Usar certificados válidos

2. **Para WebSocket**
   - Usar WSS (WebSocket Secure)
   - Implementar handshake seguro
   - Validar origen de mensajes

3. **Para MQTT**
   - Usar TLS
   - Autenticación por usuario/contraseña
   - ACLs para control de acceso

## ACTIVIDADES PRÁCTICAS

1. **Ejercicio HTTP**
   - Crear una API REST simple para control de dispositivos
   - Implementar autenticación básica
   - Probar con Postman o similar

2. **Ejercicio WebSocket**
   - Crear chat simple entre dispositivos
   - Implementar broadcast de estados
   - Manejar reconexiones

3. **Ejercicio MQTT**
   - Configurar broker local (Mosquitto)
   - Crear publicador y suscriptor
   - Implementar QoS diferentes

## RECURSOS Y HERRAMIENTAS

### Herramientas de Desarrollo
- Postman (pruebas HTTP)
- MQTT Explorer (debug MQTT)
- WebSocket King (pruebas WS)

### Bibliotecas Recomendadas
- ESP8266/ESP32: ESPAsyncWebServer
- Arduino: PubSubClient
- Python: Flask, Paho-MQTT

## CONCLUSIONES

- HTTP es ideal para operaciones ocasionales y configuración
- WebSocket es mejor para actualizaciones en tiempo real
- MQTT es perfecto para datos de sensores y comandos ligeros
- La elección del protocolo depende del caso de uso específico

## REFERENCIAS

- [ESP32 Documentation](https://docs.espressif.com/)
- [MQTT Specification](http://mqtt.org/)
- [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
- [HTTP/2 RFC](https://tools.ietf.org/html/rfc7540)
