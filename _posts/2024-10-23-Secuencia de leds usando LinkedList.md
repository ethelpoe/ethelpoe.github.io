---
title: Secuencia de leds usando LinkedList
author: ethelpoe
date: 2024-10-23 20:55:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - LL
  - ESP32
pin: false
---

Material necesario:

- 1x ESP32
- 5x leds
- 5x resistencias 330Kohms
- Paquete cable dupont (macho - macho,  hembra-macho, hembra - hembra)
- 1x protoboard (culquier cantidad de puntos)
- cable datos para el microcontrolador
  

Lo primero sera descargar el driver para el esp32 puedes leer el tutorial en el siguiente link [click aqui](https://randomnerdtutorials.com/install-esp32-esp8266-usb-drivers-cp210x-windows/)

Tendras ahora que descargar la version mas nueva del ide de arduino [click aqui]([https://](https://www.arduino.cc/en/software))

suponiendo que estas utilizando windows

![alt text](image.png)

y lo instalas.

Despues tendras que abrir el programa que acabas de abrir he ir a la seccion 'tools -> manageLibrary -> ' y buscamos "Arduino_ESP32_OTA by Arduino"

ahora conectamos el esp32 a nuestra computadora despues

![alt text](image-1.png)

 buscamos nuestro ESP32 y definimos el puerto

 ![alt text](image-2.png)

 Ahora si estamos listos para cargar nuestro primer script

 ![alt text](image-3.png)

 seguido de "new sketch"

 y comenzamos.

 Para nuestro proyecto tendremos varios archivos 

 - LinkedList.h
 - main.ino
 - Led.h
 - Node.h
 
dentro de nuestro archivo main.ino pondremos lo siguiente:
el archivo `.ino` es el que genera automaticamante al crear un "new sketch"

```c++
#include <Arduino.h>
#include <LinkedList.h>  // Biblioteca LinkedList para manejar la estructura

// Pines de los LEDs (ajustar según el proyecto)
int ledPins[] = {2, 4, 5, 18, 19};

// Estructura para almacenar el estado del LED y su pin asociado
struct LED {
  int pin;
  bool estado;
};

// Lista enlazada que almacena los LEDs
LinkedList<LED*> ledList = LinkedList<LED*>();

void setup() {
  Serial.begin(115200);

  // Inicializar los pines de los LEDs
  for (int i = 0; i < sizeof(ledPins) / sizeof(ledPins[0]); i++) {
    pinMode(ledPins[i], OUTPUT);
    
    // Añadir LEDs a la lista inicialmente en apagado
    LED* nuevoLED = new LED;
    nuevoLED->pin = ledPins[i];
    nuevoLED->estado = LOW;
    ledList.add(nuevoLED);
  }
}

void loop() {
  // Ejemplo: parpadeo secuencial de los LEDs
  for (int i = 0; i < ledList.size(); i++) {
    LED* led = ledList.get(i);
    digitalWrite(led->pin, HIGH); // Encender el LED
    delay(500);
    digitalWrite(led->pin, LOW);  // Apagar el LED
  }
  // Aquí comienza la lógica para modificar la LinkedList dinámicamente,
  // como añadir o eliminar LEDs desde la lista según la interacción del usuario.
}

// Función para agregar un LED a la lista
void agregarLED(int pin) {
  LED* nuevoLED = new LED;
  nuevoLED->pin = pin;
  nuevoLED->estado = LOW;
  ledList.add(nuevoLED);
  Serial.println("LED agregado a la secuencia");
}

// Función para eliminar un LED de la lista
void eliminarLED(int posicion) {
  if (posicion < ledList.size()) {
    LED* led = ledList.remove(posicion);
    delete led;
    Serial.println("LED eliminado de la secuencia");
  } else {
    Serial.println("Posición no válida");
  }
}

// Función para cambiar el orden de los LEDs (reordenar)
void moverLED(int origen, int destino) {
  if (origen < ledList.size() && destino < ledList.size()) {
    LED* led = ledList.remove(origen);
    ledList.add(destino, led);
    Serial.println("LED movido en la secuencia");
  } else {
    Serial.println("Posiciones no válidas");
  }
}
```

ahora escribiremos el contenido del archivo "linkedList.h"
```c++
#pragma once
template <typename T>
class Node{
  public:
    T data;
Node* next;

Node(T data): data(data), next(nullptr){}
};

```



