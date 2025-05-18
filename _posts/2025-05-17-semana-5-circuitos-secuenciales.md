---
title: "Circuitos Digitales - 5: Circuitos Secuenciales"
date: 2025-05-17T04:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - Circuitos Secuenciales
  - Flip-Flops
  - Contadores
  - Registros
  - Diseño Digital
---

En esta quinta semana del curso de Circuitos Digitales, estudiaremos los circuitos secuenciales, que a diferencia de los combinacionales, tienen memoria y pueden almacenar estados.

## Circuitos Secuenciales vs Combinacionales

### Características Principales
1. **Memoria**: Pueden almacenar información
2. **Retroalimentación**: La salida depende del estado actual y las entradas
3. **Sincronización**: Usualmente controlados por una señal de reloj
4. **Estados**: Pueden mantener y cambiar entre diferentes estados

## Flip-Flops

Los Flip-Flops son los elementos básicos de memoria en circuitos digitales.

### Flip-Flop Tipo D

```
Entradas:
- D (Data)
- CLK (Clock)
Salidas:
- Q
- Q' (Complemento)

Funcionamiento:
- En flanco positivo del reloj:
  Q = D
- En otros momentos:
  Q mantiene su valor
```

### Flip-Flop Tipo T (Toggle)

```
Entradas:
- T (Toggle)
- CLK (Clock)
Salidas:
- Q
- Q'

Funcionamiento:
- Si T=1: Q cambia de estado
- Si T=0: Q mantiene su estado
```

### Flip-Flop Tipo JK

```
Entradas:
- J (Set)
- K (Reset)
- CLK (Clock)
Salidas:
- Q
- Q'

Tabla de Funcionamiento:
J K | Q(siguiente)
0 0 | Q(actual)
0 1 | 0
1 0 | 1
1 1 | Q'(actual)
```

## Contadores

### Contador Asíncrono de 2 bits
```
Componentes:
- 2 Flip-Flops tipo T
- Señal de reloj

Estados:
00 → 01 → 10 → 11 → 00

Salidas:
Q1Q0: Cuenta binaria
```

### Contador Síncrono de 2 bits
```
Componentes:
- 2 Flip-Flops tipo JK
- Lógica combinacional
- Señal de reloj común

Ventajas:
- Sin retardos acumulativos
- Mejor rendimiento a alta frecuencia
```

## Registros

### Registro de Desplazamiento
```
Funcionamiento:
- Desplaza bits a izquierda o derecha
- Entrada serial, salida serial/paralela

Aplicaciones:
- Conversión serie-paralelo
- Almacenamiento temporal
- Retardo digital
```

### Registro de Carga Paralela
```
Operaciones:
- LOAD: Carga paralela de datos
- SHIFT: Desplazamiento
- HOLD: Mantiene el valor

Ejemplo 4 bits:
D3 D2 D1 D0 → Entradas
Q3 Q2 Q1 Q0 → Salidas
```

## Diseño Práctico: Contador de 2 bits

### Componentes Necesarios
- 2 Flip-Flops tipo D
- Circuito de reloj
- LEDs para visualización

### Implementación
```
1. Conexiones:
   - CLK → Todos los FF
   - Q0 → LED0
   - Q1 → LED1

2. Secuencia:
   00 → 01 → 10 → 11 → 00
```

## Estados y Transiciones

### Diagrama de Estados
```
Estado Actual → Siguiente Estado
   00       →      01
   01       →      10
   10       →      11
   11       →      00
```

### Control de Transiciones
- Flanco positivo del reloj
- Reset asíncrono
- Enable (habilitación)

## Simulación en CircuitVerse

Para implementar estos circuitos, usa [CircuitVerse](https://circuitverse.org/):
- Simula el comportamiento temporal
- Visualiza cambios de estado
- Analiza timing diagrams
- Depura problemas de sincronización

## Ejercicios Prácticos

1. **Básico**:
   - Implementar un Flip-Flop tipo D
   - Crear un registro de 4 bits

2. **Intermedio**:
   - Diseñar un contador ascendente/descendente
   - Implementar un registro de desplazamiento

3. **Avanzado**:
   - Crear un contador con carga paralela
   - Diseñar un detector de secuencia

## Tips de Diseño

### Temporización
1. **Reloj**:
   - Frecuencia adecuada
   - Duty cycle 50%
   - Evitar glitches

2. **Setup & Hold**:
   - Respetar tiempos mínimos
   - Verificar márgenes de seguridad

3. **Metaestabilidad**:
   - Sincronizar entradas asíncronas
   - Usar flip-flops en cascada

## Problemas Comunes y Soluciones

### 1. Carrera (Race Conditions)
- **Problema**: Cambios simultáneos indeseados
- **Solución**: Usar diseño síncrono

### 2. Glitches
- **Problema**: Pulsos espurios
- **Solución**: Filtrado y sincronización

### 3. Rebotes (Bouncing)
- **Problema**: Entradas mecánicas inestables
- **Solución**: Debouncing por hardware o software

## Debugging de Circuitos Secuenciales

1. **Verificación Visual**:
   - LEDs para estados
   - Analizador lógico

2. **Pruebas Sistemáticas**:
   - Reset inicial
   - Secuencia de estados
   - Condiciones límite

3. **Documentación**:
   - Diagrama de estados
   - Tabla de transiciones
   - Timing diagrams

*Los circuitos secuenciales son esenciales para sistemas que requieren memoria y estados.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-4-circuitos-combinacionales.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← Circuitos Digitales - 4: Circuitos Combinacionales
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-6-maquinas-estados.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      Circuitos Digitales - 6: Máquinas de Estados →
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
