---
title: "Análisis de Datos y Estadística Aplicada: De la Teoría a la Práctica"
date: 2024-05-17T00:00:00-04:00
categories: [Tutorial, Análisis de Datos]
tags: [python, estadistica, analisis-datos, preprocesamiento]
---

# Análisis de Datos y Estadística Aplicada en Python

## ¿Por qué es Fundamental el Análisis de Datos?

El análisis de datos es el proceso de inspeccionar, limpiar, transformar y modelar datos con el objetivo de descubrir información útil. Es fundamental porque:
- Nos ayuda a entender la naturaleza de nuestros datos
- Permite tomar decisiones basadas en evidencia
- Es prerequisito para cualquier proyecto de Machine Learning
- Ayuda a identificar problemas en los datos
- Facilita la comunicación de hallazgos

## Preprocesamiento de Datos

### ¿Qué es el Preprocesamiento?
El preprocesamiento es la fase más crítica y que consume más tiempo en cualquier proyecto de datos. Incluye:
- Limpieza de datos
- Transformación de variables
- Manejo de valores faltantes
- Codificación de variables categóricas
- Escalado de características

### 1. Manejo de Valores Faltantes

```python
import pandas as pd
import numpy as np
from sklearn.impute import SimpleImputer

# Crear dataset con valores faltantes
data = pd.DataFrame({
    'edad': [25, np.nan, 30, 35, np.nan],
    'salario': [30000, 45000, np.nan, 55000, 65000],
    'ciudad': ['Madrid', 'Barcelona', None, 'Sevilla', 'Valencia']
})

# 1. Identificar valores faltantes
print("Valores faltantes por columna:")
print(data.isnull().sum())

# 2. Visualizar el patrón de valores faltantes
import missingno as msno
msno.matrix(data)
plt.show()

# 3. Estrategias de imputación
# a) Numéricas - Media
imputer_num = SimpleImputer(strategy='mean')
data[['edad', 'salario']] = imputer_num.fit_transform(data[['edad', 'salario']])

# b) Categóricas - Moda
imputer_cat = SimpleImputer(strategy='most_frequent')
data[['ciudad']] = imputer_cat.fit_transform(data[['ciudad']].values)
```

### 2. Detección y Manejo de Outliers

```python
def detectar_outliers(df, columna):
    Q1 = df[columna].quantile(0.25)
    Q3 = df[columna].quantile(0.75)
    IQR = Q3 - Q1
    
    limite_inferior = Q1 - 1.5 * IQR
    limite_superior = Q3 + 1.5 * IQR
    
    outliers = df[(df[columna] < limite_inferior) | 
                  (df[columna] > limite_superior)][columna]
    
    return outliers, limite_inferior, limite_superior

# Ejemplo con datos de salarios
salarios = np.random.normal(50000, 10000, 1000)
salarios = np.append(salarios, [200000, 10000, 300000])  # Agregar outliers
df_salarios = pd.DataFrame({'salario': salarios})

outliers, li, ls = detectar_outliers(df_salarios, 'salario')
print(f"Número de outliers detectados: {len(outliers)}")
print(f"Límite inferior: {li:.2f}")
print(f"Límite superior: {ls:.2f}")

# Visualización de outliers
plt.figure(figsize=(10, 6))
sns.boxplot(x=df_salarios['salario'])
plt.title('Detección de Outliers - Box Plot')
plt.show()
```

## Estadística Descriptiva

### Medidas de Tendencia Central

```python
# Crear datos de ejemplo
notas = np.random.normal(7, 1.5, 100)
df_notas = pd.DataFrame({'calificacion': notas})

# Calcular medidas
media = df_notas['calificacion'].mean()
mediana = df_notas['calificacion'].median()
moda = df_notas['calificacion'].mode()[0]

print(f"Media: {media:.2f}")
print(f"Mediana: {mediana:.2f}")
print(f"Moda: {moda:.2f}")

# Visualización
plt.figure(figsize=(12, 6))
sns.histplot(df_notas['calificacion'], bins=30, kde=True)
plt.axvline(media, color='red', linestyle='--', label=f'Media: {media:.2f}')
plt.axvline(mediana, color='green', linestyle='--', label=f'Mediana: {mediana:.2f}')
plt.legend()
plt.title('Distribución de Calificaciones')
plt.show()
```

### Medidas de Dispersión

```python
# Calcular medidas de dispersión
varianza = df_notas['calificacion'].var()
desv_std = df_notas['calificacion'].std()
rango = df_notas['calificacion'].max() - df_notas['calificacion'].min()
cv = (desv_std / media) * 100  # Coeficiente de variación

print(f"Varianza: {varianza:.2f}")
print(f"Desviación estándar: {desv_std:.2f}")
print(f"Rango: {rango:.2f}")
print(f"Coeficiente de variación: {cv:.2f}%")
```

## Análisis Exploratorio de Datos (EDA)

### 1. Análisis Univariado

```python
def analisis_univariado(df, columna):
    """Realiza un análisis completo de una variable"""
    
    # Estadísticas básicas
    stats = df[columna].describe()
    print(f"\nEstadísticas de {columna}:")
    print(stats)
    
    # Visualizaciones
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 5))
    
    # Histograma
    sns.histplot(data=df, x=columna, kde=True, ax=ax1)
    ax1.set_title(f'Distribución de {columna}')
    
    # Box plot
    sns.boxplot(data=df, y=columna, ax=ax2)
    ax2.set_title(f'Box Plot de {columna}')
    
    plt.tight_layout()
    plt.show()
    
    # Test de normalidad
    from scipy import stats
    stat, p_value = stats.normaltest(df[columna])
    print(f"\nTest de normalidad:")
    print(f"p-value: {p_value:.4f}")
    print("¿Distribución normal?" if p_value > 0.05 else "No es distribución normal")
```

### 2. Análisis Bivariado

```python
def analisis_bivariado(df, x, y):
    """Analiza la relación entre dos variables"""
    
    # Correlación
    corr = df[x].corr(df[y])
    print(f"\nCorrelación entre {x} y {y}: {corr:.4f}")
    
    # Visualización
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 5))
    
    # Scatter plot
    sns.scatterplot(data=df, x=x, y=y, ax=ax1)
    ax1.set_title(f'Relación entre {x} y {y}')
    
    # Regresión
    sns.regplot(data=df, x=x, y=y, ax=ax2)
    ax2.set_title(f'Regresión entre {x} y {y}')
    
    plt.tight_layout()
    plt.show()
```

## Ejemplo Práctico: Análisis Completo de Dataset

Vamos a realizar un análisis completo de un conjunto de datos de empleados:

```python
# Crear dataset
np.random.seed(42)
n = 1000

data = pd.DataFrame({
    'edad': np.random.normal(35, 8, n),
    'experiencia': np.random.normal(10, 5, n),
    'salario': np.random.normal(50000, 15000, n),
    'satisfaccion': np.random.normal(7, 1.5, n),
    'departamento': np.random.choice(['IT', 'Ventas', 'Marketing', 'RRHH'], n)
})

# 1. Limpieza inicial
print("Valores faltantes:")
print(data.isnull().sum())

# 2. Análisis univariado de variables numéricas
for columna in ['edad', 'experiencia', 'salario', 'satisfaccion']:
    analisis_univariado(data, columna)

# 3. Análisis de variable categórica
plt.figure(figsize=(10, 6))
sns.countplot(data=data, x='departamento')
plt.title('Distribución por Departamento')
plt.show()

# 4. Análisis bivariado
analisis_bivariado(data, 'experiencia', 'salario')
analisis_bivariado(data, 'edad', 'satisfaccion')

# 5. Análisis por grupos
plt.figure(figsize=(12, 6))
sns.boxplot(data=data, x='departamento', y='salario')
plt.title('Salarios por Departamento')
plt.xticks(rotation=45)
plt.show()

# 6. Matriz de correlación
correlaciones = data.select_dtypes(include=[np.number]).corr()
plt.figure(figsize=(10, 8))
sns.heatmap(correlaciones, annot=True, cmap='coolwarm', center=0)
plt.title('Matriz de Correlación')
plt.show()
```

## Transformación de Variables

### 1. Escalado de Variables

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler

# StandardScaler (z-score)
scaler = StandardScaler()
data['salario_std'] = scaler.fit_transform(data[['salario']])

# MinMaxScaler (0-1)
minmax = MinMaxScaler()
data['salario_norm'] = minmax.fit_transform(data[['salario']])

# Comparación
plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)
sns.histplot(data['salario'], bins=30)
plt.title('Original')

plt.subplot(1, 3, 2)
sns.histplot(data['salario_std'], bins=30)
plt.title('Estandarizado')

plt.subplot(1, 3, 3)
sns.histplot(data['salario_norm'], bins=30)
plt.title('Normalizado')

plt.tight_layout()
plt.show()
```

### 2. Transformación de Variables Categóricas

```python
# One-Hot Encoding
dummies = pd.get_dummies(data['departamento'], prefix='dept')
data = pd.concat([data, dummies], axis=1)

# Label Encoding
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
data['departamento_encoded'] = le.fit_transform(data['departamento'])
```

## Ejercicios Prácticos

1. **Básico**: Análisis Univariado
   - Calcular estadísticas descriptivas
   - Crear visualizaciones básicas
   - Identificar outliers

2. **Intermedio**: Análisis Bivariado
   - Estudiar correlaciones
   - Crear scatter plots
   - Analizar relaciones entre variables

3. **Avanzado**: Análisis Completo
   - Realizar EDA completo
   - Aplicar transformaciones
   - Generar insights

## Recursos Adicionales

### Documentación
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [SciPy Stats](https://docs.scipy.org/doc/scipy/reference/stats.html)
- [Scikit-learn Preprocessing](https://scikit-learn.org/stable/modules/preprocessing.html)

### Tutoriales Recomendados
- [Pandas User Guide](https://pandas.pydata.org/docs/user_guide/index.html)
- [Real Python - Exploratory Data Analysis](https://realpython.com/pandas-python-explore-dataset/)
- [Towards Data Science - EDA Guide](https://towardsdatascience.com/exploratory-data-analysis-eda-python-87178e35b14)

## Próximos Pasos
1. Practica con diferentes tipos de datos
2. Aprende técnicas avanzadas de preprocesamiento
3. Desarrolla intuición estadística
4. Prepárate para Machine Learning

---
*Este post es parte del [Programa Intensivo de Especialización en IA Aplicada](/2024-05-19-syllabus-ia-aplicada). El siguiente post cubrirá los fundamentos de Machine Learning.*
