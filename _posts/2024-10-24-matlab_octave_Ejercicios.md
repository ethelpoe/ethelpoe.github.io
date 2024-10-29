---
title: Ejercicios del Curso Básico de MATLAB/Octave
author: ethelpoe
date: 2024-10-23 20:55:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - matlap
  - octave
pin: false
---

### Ejercicio 1: Simulación del Movimiento de un Proyectil

### **Descripción**
Este ejercicio simula el movimiento parabólico de un proyectil. Los estudiantes ingresan un ángulo de lanzamiento y una velocidad inicial para calcular la trayectoria y visualizarla con un gráfico en 2D.

### **Paso a Paso**
1. Solicitar datos al usuario: ángulo en grados y velocidad inicial.
2. Convertir el ángulo a radianes.
3. Calcular el tiempo de vuelo total del proyectil.
4. Calcular las posiciones del proyectil en cada instante.
5. Graficar la trayectoria.

### **Código**
```matlab
% Solicitar datos al usuario
angulo = input('Ingresa el ángulo de lanzamiento (en grados): ');
velocidad_inicial = input('Ingresa la velocidad inicial (en m/s): ');

% Convertir el ángulo a radianes
angulo_rad = deg2rad(angulo);

% Parámetros del movimiento
g = 9.81; % Aceleración gravitacional (m/s²)
t_final = (2 * velocidad_inicial * sin(angulo_rad)) / g;
t = linspace(0, t_final, 100);

% Cálculo de la trayectoria
x = velocidad_inicial * cos(angulo_rad) .* t;
y = velocidad_inicial * sin(angulo_rad) .* t - 0.5 * g * t.^2;

% Graficar la trayectoria
figure;
plot(x, y, 'b', 'LineWidth', 2);
xlabel('Distancia (m)');
ylabel('Altura (m)');
title('Trayectoria del Proyectil');
grid on;
```

---

## Ejercicio 2: Generador de Fractales Simples – Conjunto de Mandelbrot

### **Descripción**
Este ejercicio genera una visualización del conjunto de Mandelbrot. Cada punto del plano complejo se itera y colorea según cuántas iteraciones toma para divergir.

### **Paso a Paso**
1. Definir un plano complejo entre -2 y 1 en el eje real, y -1.5 a 1.5 en el imaginario.
2. Iterar cada punto y verificar si pertenece al conjunto.
3. Colorear los puntos según las iteraciones necesarias para salir del círculo de radio 2.
4. Mostrar el fractal generado.

### **Código**
```matlab
% Parámetros de la visualización
limite = 2; 
max_iter = 50; 
resolucion = 500; 

% Crear el plano complejo
x = linspace(-2, 1, resolucion);
y = linspace(-1.5, 1.5, resolucion);
[X, Y] = meshgrid(x, y);
Z = X + 1i * Y;
C = Z;

% Inicializar el contador de iteraciones
M = zeros(size(Z));

% Iterar para cada punto
for k = 1:max_iter
    Z = Z.^2 + C;
    M(abs(Z) > limite & M == 0) = k;
end

% Visualizar el fractal
figure;
imagesc(M);
colormap(jet);
axis off;
title('Conjunto de Mandelbrot');
```

---

## Ejercicio 3: Procesamiento de Imágenes Básicas – Aplicación de Filtros

### **Descripción**
Este ejercicio introduce el procesamiento básico de imágenes al aplicar filtros de media y Sobel a una imagen en escala de grises.

### **Paso a Paso**
1. Cargar una imagen en escala de grises usando `imread`.
2. Aplicar un filtro de media para suavizar la imagen.
3. Aplicar el filtro Sobel para detectar bordes.
4. Mostrar las imágenes usando subplots.

### **Código**
```matlab
% Cargar la imagen
imagen = imread('imagen_grises.jpg'); % Asegúrate de tener una imagen en escala de grises

% Filtro de media
filtro_media = fspecial('average', [3 3]);
imagen_media = imfilter(imagen, filtro_media);

% Filtro Sobel para detección de bordes
filtro_sobel = fspecial('sobel');
imagen_bordes = imfilter(imagen, filtro_sobel);

% Mostrar las imágenes
figure;
subplot(1, 3, 1); imshow(imagen); title('Imagen Original');
subplot(1, 3, 2); imshow(imagen_media); title('Filtro de Media');
subplot(1, 3, 3); imshow(imagen_bordes); title('Filtro Sobel');
```


---

## Ejercicio 1: Simulación del Movimiento de un Proyectil

### **Explicación**
En este ejercicio, el movimiento del proyectil sigue la ecuación:
- **Posición en X**: \(x(t) = v_0 \cdot \cos(\theta) \cdot t\)
- **Posición en Y**: \(y(t) = v_0 \cdot \sin(\theta) \cdot t - \frac{1}{2} g t^2\)

Donde:
- \(v_0\) es la velocidad inicial.
- \(\theta\) es el ángulo de lanzamiento (convertido a radianes).
- \(g = 9.81 m/s^2\) es la gravedad.

El proyectil describe una trayectoria parabólica y termina cuando toca el suelo (cuando \(y = 0\)).

### **Actividad sugerida 1: Modificar la gravedad**
Permitir que el usuario seleccione un valor de gravedad diferente para simular el comportamiento en otros planetas.

**Código modificado:**
```matlab
% Solicitar datos
angulo = input('Ingresa el ángulo de lanzamiento (en grados): ');
velocidad_inicial = input('Ingresa la velocidad inicial (en m/s): ');
gravedad = input('Ingresa el valor de la gravedad (en m/s²): ');

% Convertir el ángulo a radianes
angulo_rad = deg2rad(angulo);

% Calcular la trayectoria
t_final = (2 * velocidad_inicial * sin(angulo_rad)) / gravedad;
t = linspace(0, t_final, 100);
x = velocidad_inicial * cos(angulo_rad) .* t;
y = velocidad_inicial * sin(angulo_rad) .* t - 0.5 * gravedad * t.^2;

% Graficar la trayectoria
figure;
plot(x, y, 'b', 'LineWidth', 2);
xlabel('Distancia (m)');
ylabel('Altura (m)');
title('Trayectoria del Proyectil en Diferentes Gravedades');
grid on;
```

**Explicación adicional:**  
Este cambio permitirá que los alumnos experimenten con la gravedad en otros planetas como Marte (3.71 m/s²) o la Luna (1.62 m/s²).

---

## Ejercicio 2: Generador de Fractales – Conjunto de Mandelbrot

### **Explicación**
El conjunto de Mandelbrot está formado por números complejos que no divergen cuando se aplican iterativamente la fórmula:
- \(Z_{n+1} = Z_n^2 + C\), donde \(Z\) y \(C\) son números complejos.

Los puntos que permanecen dentro de un círculo de radio 2 después de un número de iteraciones se consideran parte del conjunto.

### **Actividad sugerida 1: Permitir el número de iteraciones**
Agregar la opción de que el usuario modifique el número de iteraciones para observar los cambios en la calidad del fractal.

**Código modificado:**
```matlab
% Solicitar el número de iteraciones
max_iter = input('Ingresa el número máximo de iteraciones: ');

% Crear el plano complejo
resolucion = 500;
x = linspace(-2, 1, resolucion);
y = linspace(-1.5, 1.5, resolucion);
[X, Y] = meshgrid(x, y);
Z = X + 1i * Y;
C = Z;
M = zeros(size(Z));

% Iterar para cada punto
for k = 1:max_iter
    Z = Z.^2 + C;
    M(abs(Z) > 2 & M == 0) = k;
end

% Visualizar el fractal
figure;
imagesc(M);
colormap(jet);
axis off;
title('Conjunto de Mandelbrot con Iteraciones Personalizadas');
```

**Explicación adicional:**  
Los estudiantes podrán ver cómo un mayor número de iteraciones genera más detalles, pero también aumenta el tiempo de procesamiento.

---

## Ejercicio 3: Procesamiento de Imágenes Básicas – Aplicación de Filtros

### **Explicación**
Este ejercicio introduce la idea de filtros aplicados a imágenes.  
- **Filtro de Media**: Suaviza la imagen reduciendo el ruido.
- **Filtro Sobel**: Detecta bordes en la imagen resaltando los cambios de intensidad.

### **Actividad sugerida 1: Cambiar el tamaño del filtro de media**
Permitir que el usuario defina el tamaño del filtro de media y observar cómo esto afecta la suavidad de la imagen.

**Código modificado:**
```matlab
% Cargar la imagen
imagen = imread('imagen_grises.jpg');

% Solicitar el tamaño del filtro al usuario
tamano_filtro = input('Ingresa el tamaño del filtro (por ejemplo, 3 para 3x3): ');

% Aplicar el filtro de media
filtro_media = fspecial('average', [tamano_filtro tamano_filtro]);
imagen_media = imfilter(imagen, filtro_media);

% Aplicar el filtro Sobel
filtro_sobel = fspecial('sobel');
imagen_bordes = imfilter(imagen, filtro_sobel);

% Mostrar las imágenes
figure;
subplot(1, 3, 1); imshow(imagen); title('Imagen Original');
subplot(1, 3, 2); imshow(imagen_media); title('Filtro de Media');
subplot(1, 3, 3); imshow(imagen_bordes); title('Filtro Sobel');
```

**Explicación adicional:**  
Un filtro de media más grande suaviza más la imagen pero puede perder detalles importantes. Los estudiantes aprenderán cómo ajustar el tamaño del filtro según el resultado deseado.

---
