
# Sistema de Monitoreo IoT con ESP32

## 1. Introducción y Objetivos del Proyecto
Este proyecto tiene como objetivo crear un sistema de monitoreo ambiental utilizando un ESP32. 
El dispositivo recogerá datos de temperatura, humedad y presión y los enviará a una plataforma IoT para almacenamiento y visualización en tiempo real. 
La meta es diseñar un sistema modular que permita extender la funcionalidad con nuevos sensores o tipos de monitoreo.

## 2. Materiales Necesarios
- **ESP32** (modelo con conectividad WiFi)
- **Sensor de Temperatura y Humedad** (DHT22 o DHT11)
- **Sensor de Presión Atmosférica** (BMP180 o BME280)
- **Cables de conexión**
- **Protoboard** (opcional para pruebas de conexión)
- **Fuente de alimentación** (si no se alimenta a través de USB)
- **Plataforma IoT**: ThingSpeak, Blynk, u otra de tu elección (con una cuenta configurada para recibir datos)

## 3. Esquema del Circuito
- Conecta el pin de datos del sensor DHT al pin GPIO 4 del ESP32.
- Conecta los pines SDA y SCL del sensor de presión a los pines GPIO 21 y GPIO 22 del ESP32.
- Conecta los pines de alimentación (VCC y GND) de ambos sensores al ESP32.

> **Nota**: Asegúrate de utilizar resistencias de pull-up de 4.7kΩ si el sensor de presión requiere la comunicación I2C.

## 4. Configuración del Hardware y Conexiones
1. Conecta el ESP32 al protoboard (si lo usas) y asegúrate de que está bien fijado.
2. Realiza las conexiones según el esquema del circuito.
3. Verifica que todas las conexiones están correctas antes de encender el ESP32.

## 5. Desarrollo del Código en Arduino IDE

### Configuración Inicial
Asegúrate de tener las librerías necesarias en el Arduino IDE:
- **DHT**: Para los sensores DHT11 y DHT22.
- **Adafruit BMP180** o **Adafruit BME280**: Dependiendo del sensor de presión.

#### Instalación de librerías
1. En el Arduino IDE, ve a **Sketch > Include Library > Manage Libraries...**
2. Busca e instala las librerías mencionadas.

### Código

```cpp
#include <WiFi.h>
#include <DHT.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BME280.h>  // Usa esta librería para el BME280

#define DHTPIN 4
#define DHTTYPE DHT22  // Cambia a DHT11 si usas ese modelo
#define SEALEVELPRESSURE_HPA (1013.25)

DHT dht(DHTPIN, DHTTYPE);
Adafruit_BME280 bme;

const char* ssid = "TU_SSID";
const char* password = "TU_PASSWORD";
const char* server = "api.thingspeak.com";  // Cambia esto si usas otra plataforma

WiFiClient client;

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("WiFi conectado!");

  dht.begin();
  if (!bme.begin(0x76)) {
    Serial.println("Error al inicializar el BME280!");
    while (1);
  }
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();
  float pressure = bme.readPressure() / 100.0F;
  
  if (isnan(temp) || isnan(hum) || isnan(pressure)) {
    Serial.println("Error al leer los sensores!");
    return;
  }
  
  if (client.connect(server, 80)) {
    String postStr = "field1=" + String(temp) + "&field2=" + String(hum) + "&field3=" + String(pressure);
    client.print("POST /update HTTP/1.1
");
    client.print("Host: " + String(server) + "
");
    client.print("Connection: close
");
    client.print("X-THINGSPEAKAPIKEY: TU_API_KEY
");
    client.print("Content-Type: application/x-www-form-urlencoded
");
    client.print("Content-Length: ");
    client.print(postStr.length());
    client.print("

");
    client.print(postStr);
    client.stop();
  }

  delay(20000);  // 20 segundos entre envíos
}
```

## 6. Conexión a una Plataforma IoT (ThingSpeak)
1. **Crea una cuenta** en ThingSpeak (o la plataforma que prefieras).
2. **Configura un canal** y obtén la clave API.
3. **Sustituye "TU_API_KEY" en el código** con la clave API de tu canal.
4. **Ejecuta el código** y verifica que los datos se envíen correctamente.

## 7. Pruebas y Resultados
1. Abre el monitor serial en Arduino IDE para ver las lecturas de los sensores.
2. Verifica que los datos aparezcan en tu canal IoT.
3. Ajusta el intervalo de tiempo en el código si deseas una frecuencia diferente.

## 8. Posibles Extensiones y Mejoras
- **Alertas y Notificaciones**: Configura alertas en la plataforma IoT para condiciones específicas (como temperatura elevada).
- **Nuevos Sensores**: Agrega sensores adicionales como un sensor de calidad de aire.
- **Pantalla LCD**: Muestra las lecturas directamente en una pantalla conectada al ESP32.

---

