---
title: "Semana 6: Máquinas de Estados - Moore y Mealy"
date: 2025-05-17T05:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - FSM
  - Máquinas de Estado
  - Moore
  - Mealy
  - Diseño Digital
---

En esta sexta semana del curso de Circuitos Digitales, exploraremos las Máquinas de Estados Finitos (FSM - Finite State Machines), centrándonos en los modelos Moore y Mealy.

## Máquinas de Estados Finitos (FSM)

### Características Fundamentales
1. **Estados**: Conjunto finito de situaciones posibles
2. **Entradas**: Señales que pueden modificar el estado
3. **Salidas**: Señales que dependen del estado actual
4. **Transiciones**: Reglas para cambiar de estado
5. **Estado Inicial**: Punto de partida definido

## Tipos de FSM

### Máquina Moore
```
Características:
- Salidas dependen SOLO del estado actual
- Más predecibles y fáciles de depurar
- Requieren más estados que Mealy
- Salidas síncronas con el reloj

Estructura:
Estado → Salida
```

### Máquina Mealy
```
Características:
- Salidas dependen del estado actual Y las entradas
- Más eficientes en número de estados
- Pueden responder más rápidamente
- Salidas pueden ser asíncronas

Estructura:
(Estado, Entrada) → Salida
```

## Ejemplo Práctico: Sistema de Semáforo

### Implementación Moore
```
Estados:
- ROJO (R=1, A=0, V=0)
- VERDE (R=0, A=0, V=1)
- AMARILLO (R=0, A=1, V=0)

Secuencia:
ROJO → VERDE → AMARILLO → ROJO
```

### Implementación Mealy
```
Estados:
- S0 (Rojo)
- S1 (Verde)
- S2 (Transición)

Transiciones:
S0 + Timer → S1 + Verde
S1 + Timer → S2 + Amarillo
S2 + Timer → S0 + Rojo
```

## Diseño de FSM

### 1. Diagrama de Estados
```
Componentes:
- Círculos: Estados
- Flechas: Transiciones
- Etiquetas: Entrada/Salida
```

### 2. Tabla de Transición
```
Estado Actual | Entrada | Estado Siguiente | Salida
    S0        |    0    |       S0        |   0
    S0        |    1    |       S1        |   1
    S1        |    0    |       S0        |   0
    S1        |    1    |       S1        |   1
```

## Ejemplo: Control de Secuencia LED

### Especificaciones
```
Entradas:
- START: Inicia secuencia
- RESET: Vuelve al estado inicial

Salidas:
- LED[3:0]: Patrón de luces

Secuencia:
0001 → 0010 → 0100 → 1000 → 0001
```

### Implementación
```
module LED_Sequence (
    input CLK, START, RESET,
    output reg [3:0] LED
);

// Estados
parameter S0 = 2'b00;  // LED = 0001
parameter S1 = 2'b01;  // LED = 0010
parameter S2 = 2'b10;  // LED = 0100
parameter S3 = 2'b11;  // LED = 1000
```

## Ejemplos Prácticos

### 1. Dispensador de Dulces
```
Estados:
- ESPERA
- MONEDA_INSERTADA
- ENTREGA_DULCE
- ERROR

Entradas:
- MONEDA
- SENSOR_DULCE
- ERROR_SENSOR

Salidas:
- MOTOR_ENTREGA
- LED_ERROR
- DEVOLVER_MONEDA
```

### 2. Control de Paso Peatonal
```
Estados:
- VERDE_AUTOS
- AMARILLO_AUTOS
- ROJO_AUTOS
- VERDE_PEATONES

Entradas:
- BOTON_PEATÓN
- SENSOR_TIEMPO

Salidas:
- SEMÁFORO_AUTOS[2:0]
- SEMÁFORO_PEATONES[1:0]
```

## Simulación en CircuitVerse

Para implementar FSMs en [CircuitVerse](https://circuitverse.org/):
1. Diseña el diagrama de estados
2. Implementa la lógica secuencial
3. Añade decodificadores de estado
4. Prueba todas las transiciones

## Ejercicios Prácticos

1. **Básico**:
   - Diseñar un detector de secuencia "1101"
   - Implementar un controlador de puerta automática

2. **Intermedio**:
   - Crear una máquina expendedora simple
   - Diseñar un controlador de ascensor de 2 pisos

3. **Avanzado**:
   - Implementar un sistema de semáforo inteligente
   - Diseñar un controlador de memoria cache

## Tips de Diseño

### 1. Elección del Modelo
- **Moore**: Para salidas estables y predecibles
- **Mealy**: Para respuesta rápida y menos estados

### 2. Consideraciones de Diseño
- Identificar claramente estados y transiciones
- Manejar casos especiales y errores
- Implementar reset síncrono
- Documentar el comportamiento esperado

### 3. Depuración
- Usar LEDs para visualizar estados
- Implementar modo de prueba
- Verificar todas las transiciones
- Probar condiciones límite

## Problemas Comunes y Soluciones

### 1. Estados No Alcanzables
- **Problema**: Estados que nunca se activan
- **Solución**: Verificar lógica de transición

### 2. Condiciones de Carrera
- **Problema**: Transiciones simultáneas
- **Solución**: Priorizar transiciones

### 3. Estados Indefinidos
- **Problema**: Estados no considerados
- **Solución**: Implementar estado por defecto

## Documentación

### 1. Diagramas
- Diagrama de estados
- Tabla de transiciones
- Diagrama de tiempo

### 2. Especificaciones
- Condiciones de entrada
- Comportamiento esperado
- Manejo de errores

### 3. Pruebas
- Plan de verificación
- Casos de prueba
- Resultados esperados

*Recuerda: El diseño de FSM requiere una planificación cuidadosa y una documentación clara. La elección entre Moore y Mealy dependerá de los requisitos específicos de tu aplicación.*
