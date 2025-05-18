---
title: "Guía Completa: Punteros y Gestión de Memoria en C++"
date: 2025-05-17T09:00:00-04:00
categories:
  - Programación II
tags:
  - C++
  - Punteros
  - Memoria Dinámica
  - Smart Pointers
  - Referencias
---

Esta guía de referencia cubre todo lo que necesitas saber sobre punteros en C++, desde los conceptos básicos hasta las mejores prácticas modernas.

## ¿Qué son los Punteros?

Un puntero es una variable que almacena la dirección de memoria de otra variable. Piensa en ellos como "señaladores" que apuntan a donde está guardado un dato.

## Sintaxis Básica

```cpp
int numero = 42;           // Variable normal
int* puntero = &numero;    // Puntero que apunta a 'numero'

cout << numero;            // Imprime: 42
cout << &numero;           // Imprime: dirección (ej: 0x7ffd4c)
cout << puntero;          // Imprime: misma dirección
cout << *puntero;         // Imprime: 42 (desreferenciación)
```

## Punteros y Arrays

```cpp
int arr[5] = {1, 2, 3, 4, 5};
int* ptr = arr;  // Los arrays decaen en punteros

// Diferentes formas de acceder al mismo elemento
cout << arr[2] << endl;     // Usando índice
cout << *(ptr + 2) << endl; // Aritmética de punteros
cout << ptr[2] << endl;     // Sintaxis de array con puntero
```

## Memoria Dinámica

### Asignación y Liberación
```cpp
// Asignar memoria para un entero
int* p1 = new int(42);
delete p1;  // Liberar memoria

// Asignar array dinámico
int* arr = new int[100];
delete[] arr;  // Liberar array

// ¡IMPORTANTE! Siempre emparejar new con delete
```

### Errores Comunes
```cpp
// 1. Memory Leak (fuga de memoria)
int* p = new int(42);
p = new int(73);  // Se perdió la referencia al primer int

// 2. Dangling Pointer (puntero colgante)
int* p = new int(42);
delete p;
cout << *p;  // ¡ERROR! Accediendo memoria liberada

// 3. Double Delete
int* p = new int(42);
delete p;
delete p;  // ¡ERROR! Doble liberación
```

## Smart Pointers (C++ Moderno)

Los smart pointers son la forma moderna y segura de manejar memoria dinámica.

### unique_ptr
```cpp
#include <memory>

// Propiedad única - no se puede copiar
unique_ptr<int> up = make_unique<int>(42);
cout << *up << endl;  // Uso normal
// Se libera automáticamente al salir de scope
```

### shared_ptr
```cpp
// Propiedad compartida - cuenta de referencias
shared_ptr<int> sp1 = make_shared<int>(42);
shared_ptr<int> sp2 = sp1;  // Ambos apuntan al mismo int

cout << sp1.use_count() << endl;  // Imprime: 2
```

### weak_ptr
```cpp
// Referencia débil - evita referencias circulares
shared_ptr<int> sp = make_shared<int>(42);
weak_ptr<int> wp = sp;

if (auto temp = wp.lock()) {  // Verifica si el objeto existe
    cout << *temp << endl;
}
```

## Punteros vs Referencias

```cpp
void funcionPuntero(int* ptr) {
    if (ptr) {  // Necesita verificación
        *ptr = 42;
    }
}

void funcionReferencia(int& ref) {
    ref = 42;  // Más simple, siempre válida
}

int main() {
    int x = 0;
    funcionPuntero(&x);     // Necesita &
    funcionReferencia(x);   // Más natural
}
```

## Mejores Prácticas

### 1. Preferir Smart Pointers
```cpp
// En lugar de:
class MiClase {
    Recurso* recurso;
public:
    MiClase() : recurso(new Recurso()) {}
    ~MiClase() { delete recurso; }
};

// Usar:
class MiClase {
    unique_ptr<Recurso> recurso;
public:
    MiClase() : recurso(make_unique<Recurso>()) {}
    // No necesita destructor
};
```

### 2. RAII (Resource Acquisition Is Initialization)
```cpp
class AdministradorArchivo {
    unique_ptr<FILE, decltype(&fclose)> archivo;
public:
    AdministradorArchivo(const char* nombre)
        : archivo(fopen(nombre, "r"), fclose) {
        if (!archivo) throw runtime_error("No se pudo abrir");
    }
    // Se cierra automáticamente al destruirse
};
```

### 3. Evitar Arrays Crudos
```cpp
// En lugar de:
int* arr = new int[100];

// Usar:
vector<int> vec(100);  // Mejor
// o
unique_ptr<int[]> arr = make_unique<int[]>(100);
```

## Depuración de Problemas con Punteros

### Herramientas
1. Valgrind - Detector de fugas de memoria
2. AddressSanitizer - Detector de errores de memoria
3. Visual Studio Memory Profiler

### Consejos
```cpp
// 1. Inicializar punteros
int* ptr = nullptr;  // Mejor que int* ptr;

// 2. Verificar antes de usar
if (ptr != nullptr) {
    *ptr = 42;
}

// 3. Usar smart pointers cuando sea posible
unique_ptr<int> smart = make_unique<int>(42);
```

## Ejercicios Prácticos

### 1. Básico: Implementar una lista enlazada simple
```cpp
struct Nodo {
    int dato;
    unique_ptr<Nodo> siguiente;
    
    Nodo(int d) : dato(d) {}
};
```

### 2. Intermedio: Pool de objetos con shared_ptr
```cpp
class ObjetoPool {
    vector<shared_ptr<Objeto>> pool;
public:
    shared_ptr<Objeto> obtenerObjeto();
    void devolverObjeto(shared_ptr<Objeto> obj);
};
```

### 3. Avanzado: Implementar un smart pointer personalizado
```cpp
template<typename T>
class SmartPtr {
    T* ptr;
public:
    SmartPtr(T* p = nullptr) : ptr(p) {}
    ~SmartPtr() { delete ptr; }
    
    T& operator*() { return *ptr; }
    T* operator->() { return ptr; }
};
```

## Recursos Adicionales
- [CPPReference - Smart Pointers](https://en.cppreference.com/w/cpp/memory)
- [Modern C++ Guidelines - Smart Pointers](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rf-smart)
- [Effective Modern C++ - Scott Meyers](http://shop.oreilly.com/product/0636920033707.do)

## Advertencias Comunes
1. Nunca usar `delete` en un puntero que no fue creado con `new`
2. Evitar punteros crudos para propiedad de recursos
3. Cuidado con las referencias circulares usando `shared_ptr`
4. No mezclar gestión de memoria automática y manual

Este post sirve como referencia para consultar durante todo el curso cuando trabajen con punteros y gestión de memoria.
