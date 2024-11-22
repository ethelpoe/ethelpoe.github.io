---
layout: post
title: "Mini Rechargeable Power Supply"
author: "Francisco Macias"
date: 2024-11-03 20:55:00 +0800
subtitle: ""
categories: 
   - Proyectos, 
   - IoT,
   - C++
tags:
   - ESP32
   - LinkedList
   - IoT
   - Tutorial
description: ""
---

## 1. Descripción General
Este proyecto tiene como objetivo crear un sistema de monitoreo ambiental utilizando un ESP32. 
El dispositivo recogerá datos de temperatura, humedad y presión y los enviará a una plataforma IoT para almacenamiento y visualización en tiempo real. 
La meta es diseñar un sistema modular que permita extender la funcionalidad con nuevos sensores o tipos de monitoreo.

Para la elaboracion de este proyecto, necesario que el lector tenga conocimientos basicos con esp32 o el ide de arduino.

## 2. Materiales Necesarios
- **ESP32** (modelo con conectividad WiFi)
- **Sensor de Temperatura y Humedad** (DHT22 o DHT11)
- **Sensor de Presión Atmosférica** (BMP180 o BME280)
- **Cables de conexión**
- **Protoboard** (opcional para pruebas de conexión)
- **Fuente de alimentación** (si no se alimenta a través de USB)
- **Plataforma IoT**: ThingSpeak, Blynk, u otra de tu elección (con una cuenta configurada para recibir datos)



---

