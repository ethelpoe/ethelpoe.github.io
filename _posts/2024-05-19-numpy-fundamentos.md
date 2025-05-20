---
title: "NumPy: El Pilar Fundamental del Análisis Numérico en Python"
date: 2024-05-17T00:00:00-04:00
categories: [Tutorial, Python]
tags: [numpy, arrays, computacion-cientifica, data-science]

---

# NumPy: La Base de la Computación Científica en Python

## ¿Qué es NumPy y por qué es importante?

NumPy (Numerical Python) es la biblioteca fundamental para computación científica en Python. Si Python es el lenguaje base para data science, NumPy es su motor matemático. 

### ¿Por qué necesitamos NumPy?
- Las listas de Python son lentas para cálculos numéricos
- NumPy utiliza código C optimizado para operaciones numéricas
- Proporciona soporte para arrays multidimensionales
- Incluye funciones matemáticas de alto nivel
- Es la base de casi todas las bibliotecas de data science

### Ventajas sobre Python puro
```python
# Python puro - Lista
lista_python = [1, 2, 3, 4, 5]
duplicar = [x * 2 for x in lista_python]  # Lento para grandes cantidades

# NumPy - Array
import numpy as np
array_numpy = np.array([1, 2, 3, 4, 5])
duplicar = array_numpy * 2  # Muchísimo más rápido
```

## Arrays en NumPy

### ¿Qué es un Array?
Un array es una estructura de datos que contiene elementos del mismo tipo. A diferencia de las listas de Python:
- Todos los elementos deben ser del mismo tipo
- El tamaño es fijo
- Son más eficientes en memoria
- Permiten operaciones vectorizadas

### Tipos de Arrays

1. **Arrays Unidimensionales (Vectores)**:
```python
# Diferentes formas de crear arrays 1D
array_simple = np.array([1, 2, 3, 4, 5])
array_zeros = np.zeros(5)  # [0. 0. 0. 0. 0.]
array_ones = np.ones(5)    # [1. 1. 1. 1. 1.]
array_range = np.arange(5) # [0 1 2 3 4]

print(f"Tipo de datos: {array_simple.dtype}")
print(f"Dimensión: {array_simple.ndim}")
print(f"Forma: {array_simple.shape}")
```

2. **Arrays Bidimensionales (Matrices)**:
```python
# Crear una matriz 2x3
matriz = np.array([[1, 2, 3],
                   [4, 5, 6]])

print(f"Forma: {matriz.shape}")  # (2, 3)
print(f"Dimensión: {matriz.ndim}")  # 2
```

3. **Arrays Multidimensionales**:
```python
# Crear un array 3D (2x3x4)
array_3d = np.zeros((2, 3, 4))
print(f"Forma: {array_3d.shape}")  # (2, 3, 4)
print(f"Dimensión: {array_3d.ndim}")  # 3
```

## Operaciones con Arrays

### Operaciones Vectorizadas
Las operaciones vectorizadas son una de las características más poderosas de NumPy. Permiten realizar operaciones en todos los elementos de un array sin necesidad de bucles explícitos.

```python
# Operaciones elemento a elemento
arr1 = np.array([1, 2, 3])
arr2 = np.array([4, 5, 6])

suma = arr1 + arr2          # [5 7 9]
multiplicacion = arr1 * arr2 # [4 10 18]
cuadrado = arr1 ** 2        # [1 4 9]
```

### Broadcasting
Broadcasting es la capacidad de NumPy para operar en arrays de diferentes formas:

```python
# Array 3x3
matriz = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])

# Vector 1x3
vector = np.array([1, 0, -1])

# Broadcasting automático
resultado = matriz + vector
print("Resultado del broadcasting:")
print(resultado)
"""
[[2  2  2]
 [5  5  5]
 [8  8  8]]
"""
```

## Indexación y Slicing Avanzado

### Indexación Básica
```python
arr = np.array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9]])

print(arr[0, 0])    # Primer elemento (1)
print(arr[1, :])    # Segunda fila [4 5 6]
print(arr[:, 1])    # Segunda columna [2 5 8]
```

### Indexación Booleana
Una de las características más poderosas de NumPy:

```python
arr = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])

# Crear una máscara booleana
mascara = arr > 5
print(mascara)  # [False False False False False True True True True]

# Usar la máscara para filtrar
numeros_grandes = arr[mascara]
print(numeros_grandes)  # [6 7 8 9]

# Combinación de condiciones
mascara_compleja = (arr > 3) & (arr < 8)
print(arr[mascara_compleja])  # [4 5 6 7]
```

## Funciones Matemáticas y Estadísticas

### Funciones Básicas
```python
arr = np.array([1, 2, 3, 4, 5])

print(f"Suma: {np.sum(arr)}")
print(f"Media: {np.mean(arr)}")
print(f"Desviación estándar: {np.std(arr)}")
print(f"Mínimo: {np.min(arr)}")
print(f"Máximo: {np.max(arr)}")
```

### Funciones Trigonométricas
```python
# Ángulos en radianes
angulos = np.array([0, np.pi/2, np.pi])

print("Seno:", np.sin(angulos))
print("Coseno:", np.cos(angulos))
print("Tangente:", np.tan(angulos))
```

## Ejemplo Práctico: Análisis de Temperaturas

Vamos a crear un ejemplo más complejo que combine varios conceptos:

```python
# Crear datos de temperatura simulados para una semana
# Forma: (7 días, 24 horas)
temperaturas = np.random.normal(25, 5, (7, 24))

# Análisis estadístico por día
promedio_diario = np.mean(temperaturas, axis=1)
max_diario = np.max(temperaturas, axis=1)
min_diario = np.min(temperaturas, axis=1)

print("\nEstadísticas diarias:")
for dia in range(7):
    print(f"\nDía {dia + 1}:")
    print(f"  Promedio: {promedio_diario[dia]:.2f}°C")
    print(f"  Máxima: {max_diario[dia]:.2f}°C")
    print(f"  Mínima: {min_diario[dia]:.2f}°C")

# Encontrar las horas más calurosas
horas_calurosas = temperaturas > 30
print(f"\nHoras por encima de 30°C: {np.sum(horas_calurosas)}")

# Calcular la variación de temperatura
variacion_diaria = np.max(temperaturas, axis=1) - np.min(temperaturas, axis=1)
dia_mas_variable = np.argmax(variacion_diaria)
print(f"\nDía con mayor variación de temperatura: {dia_mas_variable + 1}")
print(f"Variación: {variacion_diaria[dia_mas_variable]:.2f}°C")
```

## Ejercicios Prácticos

1. **Básico**: Crear una matriz 4x4 de números aleatorios y calcular:
   - La suma de cada fila
   - El promedio de cada columna
   - El valor máximo y mínimo total

2. **Intermedio**: Simular el lanzamiento de 1000 dados:
   - Crear un array de números aleatorios entre 1 y 6
   - Calcular la frecuencia de cada número
   - Visualizar la distribución

3. **Avanzado**: Análisis de datos de ventas:
   - Crear una matriz de ventas mensuales por producto
   - Calcular tendencias
   - Identificar los mejores y peores productos

## Recursos Adicionales

### Documentación Oficial
- [NumPy Documentation](https://numpy.org/doc/)
- [NumPy User Guide](https://numpy.org/doc/stable/user/index.html)
- [NumPy Reference](https://numpy.org/doc/stable/reference/index.html)

### Tutoriales Recomendados
- [Real Python - NumPy Tutorial](https://realpython.com/numpy-tutorial/)
- [SciPy Lectures - NumPy](https://scipy-lectures.org/intro/numpy/index.html)
- [Python Data Science Handbook - NumPy](https://jakevdp.github.io/PythonDataScienceHandbook/02.00-introduction-to-numpy.html)

## Próximos Pasos
1. Practica con los ejercicios propuestos
2. Explora las funciones avanzadas de NumPy
3. Comienza a trabajar con Pandas, que se construye sobre NumPy

---
*Este post es parte del [Programa Intensivo de Especialización en IA Aplicada](/2024-05-19-syllabus-ia-aplicada). El siguiente post cubrirá Pandas y manipulación de datos.*
