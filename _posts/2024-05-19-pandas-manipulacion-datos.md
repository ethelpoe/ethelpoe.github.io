---
layout: post
title: "Pandas: La Herramienta Definitiva para Manipulación y Análisis de Datos"
date: 2024-05-19
categories: [Tutorial, Python]
tags: [pandas, data-analysis, data-science, python]
toc: true
---

# Pandas: Dominando el Análisis de Datos en Python

## ¿Qué es Pandas y Por Qué es Esencial?

Pandas es la biblioteca más importante para el análisis y manipulación de datos en Python. Su nombre deriva de "Panel Data" (datos en panel), y se ha convertido en una herramienta indispensable en el ecosistema de data science.

### ¿Por qué Pandas?
- Maneja datos estructurados y no estructurados
- Trabaja eficientemente con grandes conjuntos de datos
- Integra perfectamente funcionalidades de SQL
- Proporciona herramientas poderosas para limpieza de datos
- Facilita la visualización y el análisis estadístico

### Diferencias con NumPy
```python
import numpy as np
import pandas as pd

# NumPy - Solo datos numéricos del mismo tipo
array_numpy = np.array([1, 2, 3, 4, 5])

# Pandas - Múltiples tipos de datos
serie_pandas = pd.Series([1, "texto", 3.14, True, None])
```

## Estructuras de Datos Fundamentales

### Series
Una Series es como un array unidimensional etiquetado. Piensa en ella como una columna de Excel con nombres de fila.

```python
# Crear una Series básica
s = pd.Series([10, 20, 30, 40, 50], 
              index=['a', 'b', 'c', 'd', 'e'])

print("Valores:", s.values)  # Los datos
print("Índice:", s.index)    # Las etiquetas
print("Tipo:", s.dtype)      # Tipo de datos

# Acceso a datos
print(s['a'])        # Por etiqueta
print(s[0])         # Por posición
print(s[['a', 'c']]) # Múltiples elementos
```

### DataFrame
Un DataFrame es una tabla bidimensional, similar a una hoja de Excel:

```python
# Crear un DataFrame desde un diccionario
data = {
    'nombre': ['Ana', 'Juan', 'María', 'Pedro'],
    'edad': [25, 30, 35, 28],
    'ciudad': ['Madrid', 'Barcelona', 'Sevilla', 'Valencia'],
    'salario': [30000, 45000, 50000, 35000]
}

df = pd.DataFrame(data)

# Información básica
print("\nInformación del DataFrame:")
print(df.info())

print("\nPrimeras filas:")
print(df.head())

print("\nEstadísticas básicas:")
print(df.describe())
```

## Manipulación de Datos

### Lectura y Escritura de Datos
Pandas puede trabajar con múltiples formatos:

```python
# CSV
df_csv = pd.read_csv('datos.csv')

# Excel
df_excel = pd.read_excel('datos.xlsx')

# JSON
df_json = pd.read_json('datos.json')

# SQL
from sqlalchemy import create_engine
engine = create_engine('sqlite:///database.db')
df_sql = pd.read_sql('SELECT * FROM tabla', engine)

# Guardar datos
df.to_csv('nuevo.csv', index=False)
df.to_excel('nuevo.xlsx')
```

### Limpieza de Datos

#### Manejo de Valores Faltantes
```python
# Crear datos con valores faltantes
df_missing = pd.DataFrame({
    'A': [1, np.nan, 3, None, 5],
    'B': [None, 2, 3, np.nan, 5],
    'C': ['a', 'b', None, 'd', 'e']
})

# Detectar valores faltantes
print("Valores faltantes por columna:")
print(df_missing.isnull().sum())

# Diferentes estrategias de manejo
df_drop = df_missing.dropna()           # Eliminar filas
df_fill = df_missing.fillna(0)          # Rellenar con 0
df_fill_mean = df_missing.fillna(df_missing.mean())  # Rellenar con media
```

### Transformación de Datos

#### Filtrado y Selección
```python
# Filtrado por condición
jovenes = df[df['edad'] < 30]
madrilenos = df[df['ciudad'] == 'Madrid']

# Filtros múltiples
filtro_complejo = df[(df['edad'] < 30) & 
                     (df['salario'] > 35000)]

# Selección de columnas
datos_personales = df[['nombre', 'edad', 'ciudad']]
```

#### Agregación y Agrupamiento
```python
# Agrupar por ciudad
por_ciudad = df.groupby('ciudad').agg({
    'salario': ['mean', 'min', 'max', 'count'],
    'edad': ['mean', 'min', 'max']
}).round(2)

print("\nEstadísticas por ciudad:")
print(por_ciudad)
```

## Operaciones Avanzadas

### Merge y Join
Similar a SQL, Pandas permite combinar DataFrames:

```python
# Datos de empleados
empleados = pd.DataFrame({
    'id': [1, 2, 3, 4],
    'nombre': ['Ana', 'Juan', 'María', 'Pedro'],
    'dept_id': [1, 2, 1, 3]
})

# Datos de departamentos
departamentos = pd.DataFrame({
    'dept_id': [1, 2, 3],
    'departamento': ['IT', 'RRHH', 'Ventas']
})

# Combinar datos
resultado = pd.merge(empleados, departamentos, 
                    on='dept_id', 
                    how='left')
```

### Pivot Tables
Crear tablas dinámicas como en Excel:

```python
# Datos de ventas
ventas = pd.DataFrame({
    'fecha': pd.date_range('2024-01-01', periods=100),
    'producto': np.random.choice(['A', 'B', 'C'], 100),
    'vendedor': np.random.choice(['Juan', 'Ana', 'Pedro'], 100),
    'unidades': np.random.randint(1, 50, 100),
    'precio': np.random.uniform(10, 100, 100)
})

# Calcular total
ventas['total'] = ventas['unidades'] * ventas['precio']

# Crear tabla dinámica
tabla_pivot = pd.pivot_table(
    ventas,
    values='total',
    index='vendedor',
    columns='producto',
    aggfunc='sum'
).round(2)

print("\nVentas por vendedor y producto:")
print(tabla_pivot)
```

## Ejemplo Práctico: Análisis de Ventas

Vamos a crear un análisis completo combinando varias funcionalidades:

```python
# Generar datos de ventas más detallados
np.random.seed(42)
n_registros = 1000

datos_ventas = pd.DataFrame({
    'fecha': pd.date_range('2024-01-01', periods=n_registros),
    'producto': np.random.choice(['Laptop', 'Smartphone', 'Tablet', 'Smartwatch'], n_registros),
    'categoria': np.random.choice(['Electrónica', 'Accesorios'], n_registros),
    'region': np.random.choice(['Norte', 'Sur', 'Este', 'Oeste'], n_registros),
    'unidades': np.random.randint(1, 10, n_registros),
    'precio_unitario': np.random.uniform(300, 1200, n_registros)
})

# 1. Preparación de datos
datos_ventas['total_venta'] = datos_ventas['unidades'] * datos_ventas['precio_unitario']
datos_ventas['mes'] = datos_ventas['fecha'].dt.month
datos_ventas['dia_semana'] = datos_ventas['fecha'].dt.day_name()

# 2. Análisis por producto
analisis_producto = datos_ventas.groupby('producto').agg({
    'unidades': 'sum',
    'total_venta': ['sum', 'mean'],
    'fecha': 'count'
}).round(2)

print("\nAnálisis por Producto:")
print(analisis_producto)

# 3. Tendencias mensuales
tendencia_mensual = datos_ventas.groupby(['mes', 'producto'])['total_venta'].sum().unstack()
print("\nTendencia Mensual por Producto:")
print(tendencia_mensual)

# 4. Análisis regional
analisis_regional = pd.pivot_table(
    datos_ventas,
    values=['total_venta', 'unidades'],
    index=['region'],
    columns=['producto'],
    aggfunc='sum'
).round(2)

print("\nAnálisis Regional:")
print(analisis_regional)

# 5. Métricas clave
print("\nMétricas Clave:")
print(f"Venta Total: ${datos_ventas['total_venta'].sum():,.2f}")
print(f"Ticket Promedio: ${datos_ventas['total_venta'].mean():,.2f}")
print(f"Productos más vendidos:")
print(datos_ventas.groupby('producto')['unidades'].sum().sort_values(ascending=False))
```

## Ejercicios Prácticos

1. **Básico**: Análisis de Datos CSV
   - Cargar un archivo CSV
   - Limpiar valores faltantes
   - Calcular estadísticas básicas
   - Exportar resultados

2. **Intermedio**: Análisis de Ventas
   - Crear un DataFrame de ventas
   - Calcular métricas mensuales
   - Crear visualizaciones
   - Identificar tendencias

3. **Avanzado**: Dashboard de Datos
   - Combinar múltiples fuentes de datos
   - Crear tablas dinámicas complejas
   - Generar reportes automatizados
   - Exportar a múltiples formatos

## Recursos Adicionales

### Documentación Oficial
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Pandas User Guide](https://pandas.pydata.org/docs/user_guide/index.html)
- [Pandas API Reference](https://pandas.pydata.org/docs/reference/index.html)

### Tutoriales Recomendados
- [10 Minutes to Pandas](https://pandas.pydata.org/docs/user_guide/10min.html)
- [Real Python - Pandas Tutorial](https://realpython.com/pandas-python-explore-dataset/)
- [Python Data Science Handbook - Pandas](https://jakevdp.github.io/PythonDataScienceHandbook/03.00-introduction-to-pandas.html)

## Próximos Pasos
1. Practica con los ejercicios propuestos
2. Explora funcionalidades avanzadas
3. Comienza a trabajar con visualización de datos

---
*Este post es parte del [Programa Intensivo de Especialización en IA Aplicada](/2024-05-19-syllabus-ia-aplicada). El siguiente post cubrirá visualización de datos con Matplotlib y Seaborn.*
