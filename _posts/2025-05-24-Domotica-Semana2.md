---
title: Domotica Semana 2
author: ethelpoe
date: 2025-05-24 10:00:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - Domotica
pin: false
---

# MAESTRÍA EN DOMÓTICA Y SUSTENTABILIDAD
## ASIGNATURA: DOMÓTICA (MDDO-324)

## SISTEMAS DE COMUNICACIÓN EN DOMÓTICA
Semana 2: Sistemas de Comunicación y Topología de Red

### OBJETIVOS DE APRENDIZAJE
Al finalizar esta unidad, el estudiante podrá:

- Comprender los diferentes sistemas de comunicación en instalaciones domóticas
- Identificar y analizar las distintas topologías de red
- Evaluar los tipos de arquitectura más adecuados para cada proyecto
- Seleccionar los medios de transmisión apropiados
- Entender los protocolos de comunicación más utilizados
- Calcular y optimizar velocidades de transmisión

## CARACTERÍSTICAS DE LOS EDIFICIOS INTELIGENTES

### Integración de Sistemas
- **Interoperabilidad**: Capacidad de diferentes sistemas para trabajar juntos
- **Centralización**: Control unificado de todos los subsistemas
- **Modularidad**: Facilidad para agregar o quitar componentes
- **Escalabilidad**: Posibilidad de crecimiento del sistema

### Requisitos Fundamentales
1. **Flexibilidad**
   - Adaptación a cambios futuros
   - Reconfiguración sin obras mayores
   - Actualización de software y firmware

2. **Fiabilidad**
   - Sistemas redundantes
   - Respaldo de energía
   - Monitoreo continuo

3. **Seguridad**
   - Cifrado de comunicaciones
   - Control de acceso
   - Protección contra ciberataques

## TOPOLOGÍA DE RED

### 1. Topología en Bus
**Características:**
- Cable principal al que se conectan todos los dispositivos
- Comunicación bidireccional
- Economía de cableado

**Ventajas:**
- Simple de implementar
- Bajo costo de instalación
- Fácil de ampliar

**Desventajas:**
- Vulnerabilidad a fallos en el bus principal
- Limitación en la distancia total
- Posibles colisiones de datos

### 2. Topología en Estrella
**Características:**
- Dispositivos conectados a un punto central
- Conexiones punto a punto
- Control centralizado

**Ventajas:**
- Alta fiabilidad
- Fácil detección de fallos
- Gestión simplificada

**Desventajas:**
- Mayor consumo de cable
- Dependencia del nodo central
- Costo más elevado

### 3. Topología en Anillo
**Características:**
- Conexión en serie formando un círculo
- Comunicación unidireccional
- Token passing como método de acceso

**Ventajas:**
- Rendimiento predecible
- Sin colisiones
- Gestión de prioridades

**Desventajas:**
- Vulnerabilidad a fallos en cadena
- Complejidad en la implementación
- Latencia variable

## TIPOS DE ARQUITECTURA

### 1. Arquitectura Centralizada
**Componentes:**
- Unidad central de control
- Sensores y actuadores dependientes
- Interface única de usuario

**Aplicaciones:**
- Viviendas unifamiliares
- Pequeños comercios
- Oficinas medianas

### 2. Arquitectura Descentralizada
**Componentes:**
- Múltiples controladores
- Inteligencia distribuida
- Interfaces múltiples

**Aplicaciones:**
- Grandes edificios
- Complejos comerciales
- Campus universitarios

### 3. Arquitectura Mixta/Híbrida
**Características:**
- Combinación de sistemas centralizados y distribuidos
- Flexibilidad en la implementación
- Optimización de recursos

## MEDIOS DE TRANSMISIÓN

### 1. Medios Cableados

#### Cable Par Trenzado
- **UTP**: Sin blindaje, económico
- **FTP**: Con blindaje general
- **STP**: Blindaje individual por par
- Velocidades: 100Mbps - 10Gbps
- Distancias: hasta 100m

#### Cable Coaxial
- Mayor inmunidad al ruido
- Ancho de banda elevado
- Distancias: hasta 500m
- Aplicaciones en video y datos

#### Fibra Óptica
- Inmunidad electromagnética total
- Velocidades superiores a 100Gbps
- Distancias: varios kilómetros
- Sin interferencias

### 2. Medios Inalámbricos

#### WiFi
- **IEEE 802.11ax (Wi-Fi 6)**
  - Velocidades hasta 9.6 Gbps
  - Mejor gestión de múltiples dispositivos
  - Ahorro de energía

#### Bluetooth
- **Bluetooth 5.0**
  - Alcance hasta 200m
  - Velocidad hasta 50Mbps
  - Bajo consumo energético

#### ZigBee
- Específico para domótica
- Bajo consumo
- Topología mesh
- Hasta 250 kbps

#### Z-Wave
- Protocolo propietario
- Alta compatibilidad
- Bajo consumo
- Mesh networking

## PROTOCOLOS DE COMUNICACIÓN

### 1. Protocolos Estándar

#### KNX/EIB
- Estándar internacional
- Más de 400 fabricantes
- Compatible con múltiples medios
- Certificación rigurosa

#### LonWorks
- Protocol ANSI/EIA 709.1
- Robusto y confiable
- Utilizado en industria
- Amplia base instalada

#### BACnet
- Estándar ASHRAE/ANSI/ISO
- Orientado a edificios
- Múltiples niveles de conformidad
- Interoperabilidad garantizada

### 2. Protocolos Propietarios
- Control4
- Crestron
- AMX
- Lutron

## VELOCIDAD DE TRANSMISIÓN

### Factores que Afectan la Velocidad
1. **Medio de transmisión**
   - Características físicas
   - Limitaciones técnicas
   - Distancia

2. **Protocolo utilizado**
   - Overhead de comunicación
   - Método de acceso al medio
   - Gestión de colisiones

3. **Topología de red**
   - Número de nodos
   - Distancia entre dispositivos
   - Puntos de congestión

### Cálculos Importantes
1. **Ancho de banda necesario**
   - Cantidad de dispositivos
   - Tipo de datos transmitidos
   - Frecuencia de actualización

2. **Latencia máxima permitida**
   - Aplicaciones en tiempo real
   - Sistemas de seguridad
   - Control de accesos

## ACTIVIDADES PRÁCTICAS
Para la próxima sesión:

1. **Diseño de Red**
   - Elaborar el diseño de una red domótica para una vivienda de dos plantas
   - Seleccionar protocolos y medios de transmisión
   - Justificar la topología elegida

2. **Análisis de Caso**
   - Estudiar un edificio inteligente local
   - Identificar sus sistemas de comunicación
   - Proponer mejoras viables

3. **Ejercicio de Cálculo**
   - Determinar requisitos de ancho de banda
   - Calcular latencias máximas
   - Evaluar capacidad del sistema

## RECURSOS BIBLIOGRÁFICOS

- Fernández V. Carlos; Matías Maestro, Ignacio R. (2015). Domótica e Inmótica. Alfaomega Marcombo.
- Rodríguez, Antonio; Casa Vilaseca, Miguel. (2015). Instalaciones Domóticas. Marcombo.
- Redolphi, Luciano. (2017). Domótica. E-Book, Fox Andina/Dálaga S.A.

## CONCLUSIONES

- Los sistemas de comunicación son el pilar fundamental de cualquier instalación domótica
- La elección correcta de protocolos y medios determina el éxito del proyecto
- La tendencia actual apunta hacia sistemas híbridos y flexibles
- La seguridad y la fiabilidad son aspectos críticos en el diseño

## PRÓXIMA SEMANA
Sistemas por gestionar en edificios domóticos:
- Gestión de energía
- Gestión del confort
- Gestión de seguridad
- Gestión de comunicaciones

## ¿PREGUNTAS?
¡Gracias por su atención!

## CONTACTO Y TUTORÍA
Horario de consulta: Lunes a viernes 8:00am - 6:00pm
Correo electrónico: domotica@universidad.edu
Sala de tutoría: En sala de maestros
