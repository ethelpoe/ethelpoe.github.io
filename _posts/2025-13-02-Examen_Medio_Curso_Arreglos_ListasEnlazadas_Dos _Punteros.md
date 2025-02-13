
**Duración:** 60 minutos

## Reglas

- Puedes usar internet e IA, pero el código debe ser tuyo.
- No se permite plagio. Si tu solución parece copiada sin comprensión, obtendrás cero puntos.

---

## Parte 1: Preguntas Conceptuales (20 puntos)

**Pregunta 1: Arreglos (5 puntos)**  
Explica la diferencia entre un **arreglo estático** y un **arreglo dinámico** en C++. Proporciona un ejemplo de cómo asignar dinámicamente un arreglo.

**Pregunta 2: Listas Enlazadas (5 puntos)**  
¿Por qué utilizarías una lista enlazada en lugar de un arreglo? Menciona dos escenarios donde las listas enlazadas son preferibles.

**Pregunta 3: Patrón de Dos Punteros (5 puntos)**  
Describe el **patrón de dos punteros** y da un ejemplo de un problema en el que este enfoque sea útil.

**Pregunta 4: Casos Límite (5 puntos)**  
¿Cuáles son algunos **casos límite** comunes al trabajar con arreglos y listas enlazadas? Menciona al menos dos ejemplos.

---

## Parte 2: Problemas de Programación (80 puntos)

**Problema 1: Invertir un Arreglo (15 puntos)**  
Escribe una función que invierta un arreglo **en su lugar** utilizando el **patrón de dos punteros**. Explica por qué es más eficiente que un enfoque ingenuo.

`void invertirArreglo(int arr[], int tam);`

**Ejemplo:**

`Entrada: arr[] = {1, 2, 3, 4, 5}   Salida: arr[] = {5, 4, 3, 2, 1}`

---

**Problema 2: Detectar un Ciclo en una Lista Enlazada (20 puntos)**  
Escribe una función para detectar si una **lista enlazada simple** contiene un ciclo. Usa el **algoritmo de detección de ciclos de Floyd (Puntero Rápido y Lento)**.

`bool tieneCiclo(ListaNodo* cabeza);`

**Ejemplo:**

```yaml

Input: 1 → 2 → 3 → 4 → 2 (cycle exists)  
Output: true  

Input: 1 → 2 → 3 → 4 → NULL (no cycle)  
Output: false
```

---

**Problema 3: Encontrar un Par con Suma Objetivo (Dos Punteros) (20 puntos)**  
Dado un **arreglo ordenado** de enteros, encuentra dos números cuya suma sea un valor dado. Usa el **patrón de dos punteros** para lograr una complejidad de **O(n)**.

`pair<int, int> encontrarParSuma(vector<int>& nums, int objetivo);`

**Ejemplo:**

`Entrada: nums = {1, 3, 5, 7, 10}, objetivo = 8   Salida: (1, 7)`

**Restricciones:**

- Devuelve cualquier par válido.
- Si no existe tal par, devuelve `(-1, -1)`.

---

**Problema 4: Lista Enlazada en Zigzag (25 puntos - Avanzado)**  
Dada una **lista enlazada simple**, reorganízala en un **patrón zigzag**, donde los elementos alternen entre orden creciente y decreciente.

`ListaNodo* listaZigzag(ListaNodo* cabeza);`

**Ejemplo:**

```yaml
Input: 1 → 4 → 3 → 2 → 5 → NULL  
Output: 1 → 4 → 2 → 5 → 3 → NULL
```

**Restricciones:**

- Debes modificar la lista **en su lugar** (sin memoria extra).
- El primer elemento debe ser **menor que** el segundo, el segundo **mayor que** el tercero, y así sucesivamente.

---
## Criterios de Evaluación

- **Corrección (40%)** – ¿La solución produce los resultados esperados?
- **Eficiencia (20%)** – ¿La solución usa una complejidad óptima?
- **Calidad del Código (20%)** – ¿El código es claro, bien comentado y legible?
- **Explicación (20%)** – ¿Explicaste tu enfoque correctamente?

---
## Desafío Extra (10 puntos adicionales)

Escribe una función que **fusione dos listas enlazadas ordenadas** en una sola lista ordenada. La solución **debe ser recursiva**.

`ListaNodo* fusionarListas(ListaNodo* l1, ListaNodo* l2);`

**Ejemplo:**

`Entrada:   l1 = 1 → 3 → 5   l2 = 2 → 4 → 6   Salida: 1 → 2 → 3 → 4 → 5 → 6`  

**Restricciones:**

- No puedes usar espacio extra (excepto la pila de recursión).

---
## Fin del Examen – ¡Buena Suerte!
