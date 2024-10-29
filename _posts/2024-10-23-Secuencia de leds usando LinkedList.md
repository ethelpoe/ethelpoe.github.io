---
title: Secuencia de leds usando LinkedList
author: ethelpoe
date: 2024-10-23 20:55:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - LinkedList
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

(![image](https://github.com/user-attachments/assets/0d75992b-34f7-437e-83b5-0191fd12c35b)

y lo instalas.

Despues tendras que abrir el programa que acabas de abrir he ir a la seccion 'tools -> manageLibrary -> ' y buscamos "Arduino_ESP32_OTA by Arduino"

![image 200x200](https://github.com/user-attachments/assets/807afd77-7f9b-496b-8923-690c56b56feb)

ahora conectamos el esp32 a nuestra computadora despues buscamos nuestro ESP32 y definimos el puerto

 ![alt text](image-2.png)

 Ahora si estamos listos para cargar nuestro primer script

 ![alt text](image-3.png)

 seguido de "new sketch"

 y comenzamos.

 Para nuestro proyecto tendremos varios archivos 

 - LinkedList.h
 - main.ino
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
  for (int i = 0; i < lengthof(ledPins) / lengthof(ledPins[0]); i++) {
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
  for (int i = 0; i < ledList.length(); i++) {
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
  if (posicion < ledList.length()) {
    LED* led = ledList.remove(posicion);
    delete led;
    Serial.println("LED eliminado de la secuencia");
  } else {
    Serial.println("Posición no válida");
  }
}

// Función para cambiar el orden de los LEDs (reordenar)
void moverLED(int origen, int destino) {
  if (origen < ledList.length() && destino < ledList.length()) {
    LED* led = ledList.remove(origen);
    ledList.add(destino, led);
    Serial.println("LED movido en la secuencia");
  } else {
    Serial.println("Posiciones no válidas");
  }
}
```

ahora escribiremos el contenido del archivo "Node.h"
```c++
//Node.h
#pragma once
template <typename T>
class Node{
  public:
    T value;
    Node* next;

Node(T value): value(value), next(nullptr){}
};

```

Ahora escribiremos el codigo de nuestro archivo "LinkedList.h"

```c++
// LinkedList.h
#pragma once
#include "Node.h"
#include <Arduino.h>

template <typename T>
class LinkedList {
private:
    Node<T>* head;
    Node<T> *tail;
    int length;

public:
    LinkedList() : head(nullptr),tail(nullptr) length(0) {}

    ~LinkedList() {
        clear();
    }

    void append(T value) {
        Node<T>* newNode = new Node<T>(value);
        if (length == 0) {
            head = newNode;
            tail = newNode;
        } else {
            tail->next = newNode;
            tail = newNode;
        }
        length++;
    }


    void deleteFirst(){
        if (length == 0) return;
        Node<T> *temp = head;
        if (length == 1)
        {
            head == nullptr;
            tail == nullptr;
        }
        else
        {
            head = head->next;
        }
        delete temp;
        length--;
    }

    Node<T> *get(int index){
        if (index < 0 || index >= length)
        {
            return nullptr;
        }
        Node<T> *temp = head;
        for (int i = 0; i < index; i++)
        {
            temp = temp->next;
        }
        return temp;
    }


    void deleteLast(){
        if (length == 0)
            return;
        Node<T> *temp = head;
        if (length == 1)
        {
            head = nullptr;
            tail = nullptr;
        }
        else
        {
            Node<T> *pre = head;
            while (temp->next)
            {
                pre = temp;
                temp = temp->next;
            }
            tail = pre;
            tail->next = nullptr;
        }
        delete temp;
        length--;
    }

    void deleteNode(int index)
    {
        if (index < 0 || index >= length)
        {
            return;
        }
        if (index == 0)
        {
            return deleteFirst();
        }
        if (index == length - 1)
        {
            return deleteLast();
        }
        Node<T> *prev = get(index - 1);
        Node<T> *temp = prev->next;
        prev->next = temp->next;
        delete temp;
        length--;
    }

    void print() const {
        Node<T>* temp = head;
        while (temp != nullptr) {
            Serial.print(temp->value);
            Serial.print(" -> ");
            temp = temp->next;
        }
        Serial.println("null");
    }

    void clear() {
        while (head) {
            Node<T>* temp = head;
            head = head->next;
            delete temp;
        }
        length = 0;
    }

    int getlength() const {
        return length;
    }
};

```

Ahora solo faltaria el


