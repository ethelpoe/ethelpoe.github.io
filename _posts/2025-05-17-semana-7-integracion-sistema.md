---
title: "Circuitos Digitales - 7: Integración de Sistemas"
date: 2025-05-17T06:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - Proyecto Final
  - Integración
  - FSM
  - Circuitos Combinacionales
  - Circuitos Secuenciales
---

En esta séptima semana del curso de Circuitos Digitales, nos enfocaremos en la integración de todos los conceptos aprendidos en un proyecto final funcional.

## Componentes del Proyecto Final

### 1. Circuitos Combinacionales
- Decodificadores para entrada/salida
- Multiplexores para selección de datos
- Sumadores para procesamiento
- Comparadores para toma de decisiones

### 2. Circuitos Secuenciales
- Registros para almacenamiento
- Contadores para secuencias
- Flip-flops para estados

### 3. Máquina de Estados (FSM)
- Control del sistema
- Gestión de secuencias
- Manejo de eventos

## Ejemplo Guiado: Sistema de Control de Elevador de 2 Pisos

### Especificaciones
```
Entradas:
- Botones de llamada (2 pisos)
- Sensores de posición
- Botón de emergencia

Salidas:
- Control de motor
- Indicadores LED de piso
- Señal de alarma
```

### Componentes Necesarios
1. **Parte Combinacional**:
   - Decodificador de botones
   - Multiplexor de señales
   - Comparador de posición

2. **Parte Secuencial**:
   - Registro de estado
   - Contador de piso
   - Flip-flops para memoria

3. **FSM de Control**:
   ```
   Estados:
   - IDLE (Espera)
   - SUBIENDO
   - BAJANDO
   - DETENIDO
   - EMERGENCIA
   ```

### Diagrama de Bloques
```
[Botones/Sensores] → [Decodificador] → [FSM Control] → [Drivers]
         ↑                   ↓              ↓            ↓
    [Sensores] ← [Contadores/Registros] → [MUX] → [Indicadores]
```

## Ejemplo Alternativo: Semáforo Inteligente

### Especificaciones
```
Entradas:
- Sensor de vehículos
- Botón peatonal
- Sensor de emergencia

Salidas:
- Luces de semáforo vehicular
- Luces de semáforo peatonal
- Cuenta regresiva digital
```

### Componentes Clave
1. **Parte Combinacional**:
   - Decodificador de sensores
   - Controlador de display
   - Lógica de prioridad

2. **Parte Secuencial**:
   - Contador de tiempo
   - Registro de estados
   - Sistema de memoria

3. **FSM Principal**:
   ```
   Estados:
   - NORMAL
   - PEATONAL
   - EMERGENCIA
   - NOCHE
   ```

## Guía de Implementación

### 1. Planificación
- Definir requisitos claros
- Identificar entradas/salidas
- Diseñar diagrama de bloques
- Establecer estados del sistema

### 2. Diseño por Módulos
1. **Módulo de Entrada**:
   - Acondicionamiento de señales
   - Debouncing de botones
   - Decodificación de sensores

2. **Módulo de Control**:
   - FSM principal
   - Lógica de decisión
   - Manejo de estados

3. **Módulo de Salida**:
   - Drivers de potencia
   - Indicadores visuales
   - Interfaz de usuario

### 3. Integración
1. **Conexión de Módulos**:
   - Verificar interfaces
   - Sincronizar señales
   - Probar comunicación

2. **Pruebas Unitarias**:
   - Validar cada módulo
   - Verificar timing
   - Documentar resultados

3. **Pruebas de Sistema**:
   - Probar funcionalidad completa
   - Verificar casos límite
   - Validar especificaciones

## Tips para el Desarrollo

### 1. Metodología
- Diseñar de arriba hacia abajo
- Implementar de abajo hacia arriba
- Probar incrementalmente
- Documentar continuamente

### 2. Debugging
- Usar LEDs para debug
- Implementar puntos de prueba
- Monitorear señales críticas
- Registrar comportamientos

### 3. Optimización
- Minimizar retardos
- Reducir consumo
- Mejorar confiabilidad
- Simplificar mantenimiento

## Problemas Comunes y Soluciones

### 1. Integración
- **Problema**: Interfaces incompatibles
- **Solución**: Estandarizar niveles lógicos

### 2. Timing
- **Problema**: Retardos acumulativos
- **Solución**: Sincronización apropiada

### 3. Ruido
- **Problema**: Señales espurias
- **Solución**: Filtrado y debouncing

## Documentación del Proyecto

### 1. Documentación Técnica
- Diagramas esquemáticos
- Diagramas de estado
- Tablas de verdad
- Timing diagrams

### 2. Manual de Usuario
- Instrucciones de operación
- Procedimientos de emergencia
- Mantenimiento básico
- Solución de problemas

### 3. Reporte de Desarrollo
- Metodología utilizada
- Decisiones de diseño
- Pruebas realizadas
- Resultados obtenidos

## Preparación para la Presentación

### 1. Demostración
- Funcionalidad básica
- Casos de uso comunes
- Manejo de errores
- Características especiales

### 2. Documentación
- Presentación técnica
- Diagramas claros
- Código comentado
- Manual de usuario

### 3. Preguntas Frecuentes
- Decisiones de diseño
- Alternativas consideradas
- Mejoras futuras
- Limitaciones actuales

*La integración de sistemas es el paso final para crear proyectos digitales completos y funcionales.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-6-maquinas-estados.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← Circuitos Digitales - 6: Máquinas de Estados
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-8-pantalla-holografica.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      Circuitos Digitales - 8: Proyecto Final →
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
