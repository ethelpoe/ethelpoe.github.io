---
title: "Semana 2: Álgebra de Boole y Compuertas Lógicas"
date: 2025-05-17T01:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - Álgebra de Boole
  - Compuertas Lógicas
  - Tablas de Verdad
  - CircuitVerse
  - Circuitos Digitales
---

En esta segunda semana del curso de Circuitos Digitales, exploraremos los fundamentos del álgebra de Boole y las compuertas lógicas, elementos esenciales para el diseño de circuitos digitales.

## Compuertas Lógicas Básicas

### Compuerta AND

![alt text](/assets/images/image.png)

- Símbolo: &
- Operación: Salida = 1 solo si todas las entradas son 1
- Tabla de verdad (2 entradas):

| A | B | A AND B |
|---|---|---------|
| 0 | 0 |    0    |
| 0 | 1 |    0    |
| 1 | 0 |    0    |
| 1 | 1 |    1    |

### Compuerta OR
- Símbolo: +
- Operación: Salida = 1 si al menos una entrada es 1
- Tabla de verdad (2 entradas):

| A | B | A OR B |
|---|---|--------|
| 0 | 0 |   0   |
| 0 | 1 |   1   |
| 1 | 0 |   1   |
| 1 | 1 |   1   |

### Compuerta NOT
- Símbolo: ¬ o '
- Operación: Invierte la entrada
- Tabla de verdad:

| A | NOT A |
|---|-------|
| 0 |   1   |
| 1 |   0   |

### Compuerta XOR (OR Exclusiva)
- Símbolo: ⊕
- Operación: Salida = 1 si las entradas son diferentes
- Tabla de verdad:

| A | B | A XOR B |
|---|---|---------|
| 0 | 0 |    0    |
| 0 | 1 |    1    |
| 1 | 0 |    1    |
| 1 | 1 |    0    |

## Símbolos Estándar de Compuertas

### Norma IEC (Internacional)
```
AND: Forma rectangular con & 
OR: Forma rectangular con ≥1
NOT: Triángulo con círculo
```

### Norma ANSI/IEEE (Americana)
```
AND: Forma de D
OR: Forma como escudo
NOT: Triángulo con círculo
```

## Leyes del Álgebra de Boole

### Leyes Básicas
1. **Conmutativa**:
   - A + B = B + A
   - A × B = B × A

2. **Asociativa**:
   - (A + B) + C = A + (B + C)
   - (A × B) × C = A × (B × C)

3. **Distributiva**:
   - A × (B + C) = (A × B) + (A × C)
   - A + (B × C) = (A + B) × (A + C)

### Identidades Importantes
- A + 0 = A
- A × 1 = A
- A + 1 = 1
- A × 0 = 0
- A + A = A
- A × A = A
- A + A' = 1
- A × A' = 0

## De Tabla de Verdad a Expresión Booleana

### Proceso:
1. Identificar las filas donde la salida es 1
2. Para cada fila:
   - Escribir el producto de variables
   - Si la variable es 0, usar su complemento
3. Sumar todos los productos

**Ejemplo:**

| A | B | Salida |
|---|---|--------|
| 0 | 0 |   1    |
| 0 | 1 |   0    |
| 1 | 0 |   1    |
| 1 | 1 |   0    |

Expresión: A'B' + AB'

## Diseño Práctico con CircuitVerse

Puedes practicar el diseño de circuitos digitales en [CircuitVerse](https://circuitverse.org/), una plataforma online gratuita para crear, simular y compartir circuitos lógicos de manera interactiva.

### Ejemplo Práctico: Sumador de 1 bit
```
Entradas: A, B
Salidas: Sum, Carry
Sum = A XOR B
Carry = A AND B
```

## Ejercicios Sugeridos

1. **Básico**: Implementar las siguientes funciones:
   - F = AB + C
   - G = (A + B)(C + D)

2. **Intermedio**: Diseñar un circuito que:
   - Se active cuando al menos dos entradas están en 1
   - Detecte si un número binario de 2 bits es par

3. **Avanzado**: 
   - Crear un comparador de 2 bits
   - Implementar un multiplexor 2:1

## Herramientas de Práctica

1. **CircuitVerse**
   - Simulador de lógica basado en navegador
   - Interfaz intuitiva
   - Simulación en tiempo real

2. **Digital Works**
   - Bueno para principiantes
   - Incluye ejemplos básicos
   - Validación de circuitos

## Consejos para el Diseño de Circuitos

1. Siempre empieza con la tabla de verdad
2. Simplifica las expresiones antes de implementar
3. Utiliza el menor número posible de compuertas
4. Verifica el funcionamiento con todos los casos posibles
5. Documenta el diseño y su funcionamiento

*Recuerda: La práctica con herramientas de simulación es fundamental para dominar el diseño de circuitos digitales.*
