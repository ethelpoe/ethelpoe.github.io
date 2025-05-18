---
title: "Semana 7: Guía del Instructor - Desarrollo del Proyecto Final"
date: 2025-05-17T06:30:00-04:00
categories:
  - Circuitos Digitales
  - Guía del Instructor
tags:
  - Proyecto Final
  - Integración
  - Tutorial
  - CircuitVerse
  - Implementación
---

Esta es una guía detallada para instructores sobre cómo desarrollar y guiar los proyectos finales de Circuitos Digitales. Nos centraremos en el desarrollo paso a paso del sistema de elevador de 2 pisos, uno de los proyectos sugeridos.

## Preparación del Proyecto

### Materiales Necesarios
1. **Hardware**:
   - Protoboard
   - LEDs (2 rojos, 2 verdes, 1 amarillo)
   - Pulsadores (5)
   - Flip-flops (4 x 74LS74)
   - Decodificadores (74LS138)
   - Compuertas lógicas básicas
   - Resistencias y cables

2. **Software**:
   - CircuitVerse para simulación
   - Documentación digital

## Desarrollo Detallado del Elevador

### 1. Módulo de Entrada

#### Decodificación de Botones
```verilog
// Implementación del debouncing
module Debouncer (
    input clk,
    input button_in,
    output reg button_out
);
    reg [3:0] counter;
    
    always @(posedge clk) begin
        if (button_in == 1'b1) begin
            if (counter < 4'b1111)
                counter <= counter + 1;
        end else begin
            if (counter > 4'b0000)
                counter <= counter - 1;
        end
        button_out <= (counter == 4'b1111);
    end
endmodule
```

### 2. Módulo de Control FSM

#### Estados y Transiciones
```verilog
// FSM Principal
module ElevatorFSM (
    input clk, reset,
    input floor1_call, floor2_call,
    input at_floor1, at_floor2,
    input emergency,
    output reg [1:0] motor_control,
    output reg [1:0] floor_indicator
);

// Definición de estados
localparam [2:0]
    IDLE      = 3'b000,
    GOING_UP  = 3'b001,
    GOING_DOWN = 3'b010,
    STOPPED   = 3'b011,
    EMERGENCY = 3'b100;

reg [2:0] current_state, next_state;
```

### 3. Control del Motor

#### Lógica de Control
```verilog
// Control de movimiento
always @(*) begin
    case (current_state)
        IDLE: 
            motor_control = 2'b00;  // Motor detenido
        GOING_UP:
            motor_control = 2'b01;  // Motor subiendo
        GOING_DOWN:
            motor_control = 2'b10;  // Motor bajando
        default:
            motor_control = 2'b00;  // Motor detenido
    endcase
end
```

## Implementación Física

### 1. Montaje en Protoboard

#### Conexiones Básicas
```
1. Alimentación:
   - VCC: Pin 14 de los ICs
   - GND: Pin 7 de los ICs

2. Botones:
   - Pull-down con R=10kΩ
   - Conectar a VCC
   - Salida al decodificador

3. LEDs:
   - R=330Ω en serie
   - Cátodo a GND
```

### 2. Diagrama de Conexiones
```
[Botón Piso 1] --→ [Debouncer] --→ [Decodificador] --→ [FSM]
[Botón Piso 2] --→ [Debouncer] --→ [Decodificador] --→ [FSM]
                                                       |
                                                       ↓
[Sensores] -----------------------------------→ [Control Motor]
```

## Guía de Depuración

### 1. Verificación de Señales
1. **Entradas**:
   - Medir voltajes en botones
   - Verificar debouncing
   - Comprobar sensores

2. **Proceso**:
   - Monitorear estados FSM
   - Verificar transiciones
   - Comprobar timeouts

3. **Salidas**:
   - Verificar control motor
   - Comprobar indicadores
   - Validar alarmas

### 2. Problemas Comunes

#### Rebotes en Botones
```
Solución Hardware:
1. Agregar C=100nF
2. R pull-down = 10kΩ
3. Verificar soldaduras
```

#### Fallos de Estado
```
Procedimiento:
1. Reset manual
2. Verificar clock
3. Comprobar flip-flops
4. Revisar conexiones
```

## Evaluación del Proyecto

### 1. Lista de Verificación
```
□ Respuesta a botones
□ Movimiento correcto
□ Indicadores funcionando
□ Parada de emergencia
□ Reset funcional
□ Sin comportamientos erráticos
```

### 2. Pruebas Específicas
1. **Funcionamiento Normal**:
   - Llamada desde piso 1
   - Llamada desde piso 2
   - Llamadas simultáneas

2. **Casos Especiales**:
   - Emergencia durante movimiento
   - Múltiples pulsaciones
   - Pérdida de energía

3. **Pruebas de Estrés**:
   - Operación continua
   - Cambios rápidos
   - Condiciones límite

## Material de Apoyo para Estudiantes

### 1. Documentación
- Diagramas esquemáticos
- Tablas de estados
- Ejemplos de código
- Guías de depuración

### 2. Recursos en Línea
- Simulaciones en CircuitVerse
- Videos explicativos
- Hojas de datos
- Foro de preguntas

### 3. Plantillas
- Reporte técnico
- Presentación
- Documentación de pruebas
- Manual de usuario

## Tips para la Enseñanza

### 1. Metodología
- Dividir en módulos pequeños
- Implementar incrementalmente
- Documentar cada paso
- Fomentar experimentación

### 2. Puntos Críticos
- Énfasis en sincronización
- Importancia del debouncing
- Manejo de estados
- Documentación clara

### 3. Evaluación
- Proceso vs resultado
- Creatividad en soluciones
- Calidad de documentación
- Trabajo en equipo

*Este proyecto integrador permite a los estudiantes aplicar todos los conceptos del curso en un sistema real y funcional. La clave está en guiarlos a través del proceso de diseño mientras se mantiene espacio para la creatividad y el aprendizaje por descubrimiento.*
