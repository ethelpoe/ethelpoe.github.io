---
title: "Circuitos Digitales - 4: Circuitos Combinacionales"
date: 2025-05-17T03:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - Circuitos Combinacionales
  - Decodificadores
  - Multiplexores
  - Sumadores
  - Diseño Digital
---

En esta cuarta semana del curso de Circuitos Digitales, nos adentraremos en los circuitos combinacionales, componentes fundamentales que realizan operaciones en tiempo real sin almacenar estados previos.

## Circuitos Combinacionales Básicos

### Características Principales
- La salida depende únicamente de las entradas actuales
- No tienen memoria ni retroalimentación
- La respuesta es inmediata (considerando retardos de propagación)
- Son la base para construir sistemas más complejos

## Decodificadores

### ¿Qué es un Decodificador?
Un decodificador convierte un código de n entradas en 2ⁿ salidas mutuamente excluyivas.

### Decodificador 2:4
```
Entradas: A1, A0
Salidas:  Y0, Y1, Y2, Y3

Tabla de Verdad:
A1 A0 | Y3 Y2 Y1 Y0
 0  0 |  0  0  0  1
 0  1 |  0  0  1  0
 1  0 |  0  1  0  0
 1  1 |  1  0  0  0
```

### Aplicaciones Comunes
- Selección de dispositivos
- Decodificación de direcciones
- Control de displays de 7 segmentos
- Conversión de códigos

## Multiplexores (MUX)

### Funcionamiento
Un multiplexor selecciona una de varias entradas de datos y la envía a una única salida.

### Multiplexor 4:1
```
Entradas de datos: D0, D1, D2, D3
Entradas de selección: S1, S0
Salida: Y

Tabla de Verdad:
S1 S0 | Y
 0  0 | D0
 0  1 | D1
 1  0 | D2
 1  1 | D3
```

### Aplicaciones
- Selección de datos
- Ruteo de señales
- Comunicación serial
- Conversión paralelo a serie

## Sumadores

### Half Adder (Medio Sumador)
```
Entradas: A, B
Salidas: Sum (S), Carry (C)

S = A ⊕ B
C = A × B

Tabla de Verdad:
A B | S C
0 0 | 0 0
0 1 | 1 0
1 0 | 1 0
1 1 | 0 1
```

### Full Adder (Sumador Completo)
```
Entradas: A, B, Cin
Salidas: Sum (S), Cout

S = A ⊕ B ⊕ Cin
Cout = AB + (A⊕B)Cin

Tabla de Verdad:
A B Cin | S Cout
0 0  0  | 0  0
0 0  1  | 1  0
0 1  0  | 1  0
0 1  1  | 0  1
1 0  0  | 1  0
1 0  1  | 0  1
1 1  0  | 0  1
1 1  1  | 1  1
```

## Diseño Práctico: Sumador de 2 Bits

### Componentes Necesarios
- 2 Full Adders
- Conexiones para Carry

### Implementación
```
Entradas: A1,A0,B1,B0
Salidas: S2,S1,S0 (Sum)

FA0: Full Adder para bits menos significativos
  - Entradas: A0, B0, 0
  - Salidas: S0, C0

FA1: Full Adder para bits más significativos
  - Entradas: A1, B1, C0
  - Salidas: S1, S2(Carry)
```

## Comparadores

### Comparador de 1 Bit
```
Entradas: A, B
Salidas: A>B, A=B, A<B

Tabla de Verdad:
A B | A>B A=B A<B
0 0 |  0   1   0
0 1 |  0   0   1
1 0 |  1   0   0
1 1 |  0   1   0
```

### Implementación
```
A>B = A·B'
A=B = A⊕B'
A<B = A'·B
```

## Ejercicios Prácticos

1. **Básico**: 
   - Implementar un decodificador 2:4
   - Diseñar un multiplexor 2:1

2. **Intermedio**:
   - Construir un sumador de 2 bits
   - Crear un comparador de magnitud

3. **Avanzado**:
   - Diseñar un decodificador BCD a display de 7 segmentos
   - Implementar un sumador/restador de 4 bits

## Simulación en CircuitVerse

Para implementar estos circuitos, recomendamos usar [CircuitVerse](https://circuitverse.org/), donde podrás:
- Diseñar y probar circuitos en tiempo real
- Verificar tablas de verdad
- Compartir tus diseños
- Colaborar con otros estudiantes

## Tips de Diseño

1. **Planificación**:
   - Comienza con la tabla de verdad
   - Identifica entradas y salidas
   - Define las ecuaciones lógicas

2. **Implementación**:
   - Usa componentes estándar
   - Minimiza el número de compuertas
   - Considera los retardos de propagación

3. **Verificación**:
   - Prueba todas las combinaciones posibles
   - Verifica los casos límite
   - Documenta el comportamiento

## Problemas Comunes y Soluciones

1. **Glitches**:
   - Causa: Diferentes retardos en las compuertas
   - Solución: Usar compuertas con retardos similares

2. **Fan-out**:
   - Problema: Sobrecarga de salidas
   - Solución: Usar buffers cuando sea necesario

3. **Timing**:
   - Issue: Retardos acumulativos
   - Solución: Minimizar niveles de lógica

*Los circuitos combinacionales son bloques fundamentales para construir sistemas digitales complejos.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-3-simplificacion-circuitos.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← Circuitos Digitales - 3: Simplificación de Circuitos
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-5-circuitos-secuenciales.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      Circuitos Digitales - 5: Circuitos Secuenciales →
    </a>
  </div>
</div>

<style>
.navigation-buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 40px;
  margin-bottom: 40px;
}
</style>
