---
title: "Circuitos Digitales - 1: Sistemas Numéricos"
date: 2025-05-17T00:00:00-04:00
categories:
  - Circuitos Digitales
tags:
  - Sistemas Numéricos
  - Binario
  - Hexadecimal
  - Octal
  - BCD
  - Código Gray
---

En esta primera semana del curso de Circuitos Digitales, nos sumergiremos en los fundamentos de los sistemas numéricos y las señales digitales. Este conocimiento es esencial para comprender cómo funcionan los sistemas digitales modernos.

## Sistemas Numéricos Fundamentales

### Sistema Binario (Base 2)
El sistema binario es la base de toda la computación moderna. Utiliza únicamente dos dígitos: 0 y 1. Cada posición representa una potencia de 2:

| Posición | 2³ | 2² | 2¹ | 2⁰ |
|----------|----|----|----|----|
| Valor    | 8  | 4  | 2  | 1  |

### Sistema Decimal (Base 10)
Nuestro sistema familiar de conteo que utiliza dígitos del 0 al 9.

### Sistema Octal (Base 8)
Utiliza dígitos del 0 al 7, útil para representar números binarios de forma más compacta.

### Sistema Hexadecimal (Base 16)
Utiliza dígitos del 0 al 9 y letras de A a F. Es ampliamente usado en programación y sistemas digitales.

## Instrucciones para Convertir entre Sistemas Numéricos

### Decimal a Binario
1. Divide el número decimal entre 2.
2. Anota el residuo (0 o 1).
3. Divide el cociente nuevamente entre 2 y repite el proceso hasta que el cociente sea 0.
4. El número binario es la secuencia de residuos leída de abajo hacia arriba.

**Ejemplo:**  
Convertir 25₁₀ a binario:  
25 ÷ 2 = 12, residuo 1  
12 ÷ 2 = 6, residuo 0  
6 ÷ 2 = 3, residuo 0  
3 ÷ 2 = 1, residuo 1  
1 ÷ 2 = 0, residuo 1  
Leído de abajo hacia arriba: **11001₂**

---

### Decimal a Octal
1. Divide el número decimal entre 8.
2. Anota el residuo.
3. Divide el cociente entre 8 y repite hasta que el cociente sea 0.
4. El número octal es la secuencia de residuos de abajo hacia arriba.

**Ejemplo:**  
Convertir 65₁₀ a octal:  
65 ÷ 8 = 8, residuo 1  
8 ÷ 8 = 1, residuo 0  
1 ÷ 8 = 0, residuo 1  
Leído de abajo hacia arriba: **101₈**

---

### Decimal a Hexadecimal
1. Divide el número decimal entre 16.
2. Anota el residuo (usa letras A-F para valores 10-15).
3. Divide el cociente entre 16 y repite hasta que el cociente sea 0.
4. El número hexadecimal es la secuencia de residuos de abajo hacia arriba.

**Ejemplo:**  
Convertir 254₁₀ a hexadecimal:  
254 ÷ 16 = 15, residuo 14 (E)  
15 ÷ 16 = 0, residuo 15 (F)  
Leído de abajo hacia arriba: **FE₁₆**

---

### Binario a Decimal
1. Multiplica cada dígito binario por 2 elevado a su posición (empezando desde 0 por la derecha).
2. Suma todos los resultados.

**Ejemplo:**  
1011₂ = (1×2³) + (0×2²) + (1×2¹) + (1×2⁰) = 8 + 0 + 2 + 1 = **11₁₀**

---

### Octal a Decimal
1. Multiplica cada dígito octal por 8 elevado a su posición (empezando desde 0 por la derecha).
2. Suma todos los resultados.

**Ejemplo:**  
157₈ = (1×8²) + (5×8¹) + (7×8⁰) = 64 + 40 + 7 = **111₁₀**

---

### Hexadecimal a Decimal
1. Multiplica cada dígito hexadecimal por 16 elevado a su posición (empezando desde 0 por la derecha).
2. Suma todos los resultados.

**Ejemplo:**  
2A₁₆ = (2×16¹) + (10×16⁰) = 32 + 10 = **42₁₀**

---

### Binario a Octal
1. Agrupa los dígitos binarios en grupos de 3, comenzando desde la derecha.
2. Convierte cada grupo a su equivalente octal.

**Ejemplo:**  
110110₂ → (110)(110)  
110 = 6, 110 = 6  
Resultado: **66₈**

---

### Binario a Hexadecimal
1. Agrupa los dígitos binarios en grupos de 4, comenzando desde la derecha.
2. Convierte cada grupo a su equivalente hexadecimal.

**Ejemplo:**  
10101111₂ → (1010)(1111)  
1010 = A, 1111 = F  
Resultado: **AF₁₆**

---

*Estas instrucciones y ejemplos te ayudarán a realizar conversiones entre los sistemas numéricos más utilizados en circuitos digitales.*


## Ejemplo Práctico: Conversión del número 43

Veamos cómo convertir el número decimal 43 a diferentes bases:

1. **Decimal a Binario**: 
   - 43 = 32 + 8 + 2 + 1
   - 43₁₀ = 101011₂

2. **Decimal a Octal**:
   - 43₁₀ = 53₈

3. **Decimal a Hexadecimal**:
   - 43₁₀ = 2B₁₆

## Códigos Especiales

### Código BCD (Binary-Coded Decimal)
El BCD representa cada dígito decimal como una secuencia de 4 bits. Por ejemplo:
- 5₁₀ = 0101 BCD
- 9₁₀ = 1001 BCD

### Código Gray
Es un sistema de numeración binaria donde solo cambia un bit entre números consecutivos:
- 0: 000
- 1: 001
- 2: 011
- 3: 010
- 4: 110

## ¿Por qué usar Sistema Binario?

Los sistemas digitales utilizan base 2 por varias razones fundamentales:

1. **Simplicidad**: Solo dos estados posibles (alto/bajo, encendido/apagado)
2. **Fiabilidad**: Menor probabilidad de errores al distinguir entre solo dos estados
3. **Implementación física**: Más fácil de implementar con componentes electrónicos
4. **Procesamiento lógico**: Las operaciones booleanas trabajan naturalmente con valores binarios

## Herramientas Recomendadas

Para practicar estos conceptos, recomendamos:
- Digital Works: Excelente para simular entradas binarias simples
- Calculadoras científicas en modo programador
- Papel y lápiz para hacer conversiones manuales

## Ejercicios Sugeridos

1. Convertir 127₁₀ a binario, octal y hexadecimal
2. Convertir 1010₂ a decimal y hexadecimal
3. Escribir tu edad en BCD
4. Convertir FF₁₆ a todos los otros sistemas

*Recuerda: La práctica constante es clave para dominar las conversiones entre sistemas numéricos.*

*Los sistemas numéricos son la base fundamental para comprender el funcionamiento de los sistemas digitales.*

---

<div class="navigation-buttons">
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-2-algebra-boole.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      Circuitos Digitales - 2: Álgebra de Boole →
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
