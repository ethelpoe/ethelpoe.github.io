---
title: "Semana 3: Encapsulamiento y Buenas Prácticas en C++"
date: 2025-05-17T11:00:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Encapsulamiento
  - Getters
  - Setters
  - C++
---

Esta semana profundizaremos en el concepto de encapsulamiento y las buenas prácticas en POO, utilizando como ejemplo práctico una clase `Libro`.

## Encapsulamiento y Control de Acceso

El encapsulamiento es uno de los pilares fundamentales de la POO, que nos permite:
- Ocultar la implementación interna
- Proteger la integridad de los datos
- Proporcionar una interfaz clara y controlada

## Implementación Completa

```cpp
class Libro {
private:
    string isbn;
    string titulo;
    string autor;
    int anioPublicacion;
    double precio;
    int copias;

    // Método privado para validación
    bool esISBNValido(const string& isbn) const {
        if (isbn.length() != 13) return false;
        for (char c : isbn) {
            if (!isdigit(c)) return false;
        }
        return true;
    }

public:
    // Constructor
    Libro(string _isbn, string _titulo, string _autor, int _anio, double _precio, int _copias = 0) 
        : titulo(_titulo), autor(_autor), anioPublicacion(_anio), precio(_precio), copias(_copias) {
        if (!esISBNValido(_isbn)) {
            throw invalid_argument("ISBN inválido");
        }
        isbn = _isbn;
    }

    // Getters
    string getISBN() const { return isbn; }
    string getTitulo() const { return titulo; }
    string getAutor() const { return autor; }
    int getAnioPublicacion() const { return anioPublicacion; }
    double getPrecio() const { return precio; }
    int getCopias() const { return copias; }

    // Setters con validación
    void setPrecio(double nuevoPrecio) {
        if (nuevoPrecio < 0) {
            throw invalid_argument("El precio no puede ser negativo");
        }
        precio = nuevoPrecio;
    }

    void agregarCopias(int cantidad) {
        if (cantidad < 0) {
            throw invalid_argument("La cantidad no puede ser negativa");
        }
        copias += cantidad;
    }

    bool venderCopia() {
        if (copias > 0) {
            copias--;
            return true;
        }
        return false;
    }

    // Método para mostrar información
    void mostrarDatos() const {
        cout << "ISBN: " << isbn << endl
             << "Título: " << titulo << endl
             << "Autor: " << autor << endl
             << "Año: " << anioPublicacion << endl
             << "Precio: $" << fixed << setprecision(2) << precio << endl
             << "Copias disponibles: " << copias << endl;
    }
};
```

## Uso de `this`

El puntero `this` se refiere al objeto actual. Es útil cuando:
1. Hay ambigüedad entre parámetros y atributos
2. Necesitamos devolver una referencia al objeto actual
3. Queremos enfatizar que estamos accediendo a miembros de la clase

```cpp
class Libro {
public:
    // Método encadenado usando this
    Libro& actualizarPrecio(double precio) {
        this->precio = precio;
        return *this;
    }

    Libro& actualizarCopias(int copias) {
        this->copias = copias;
        return *this;
    }
};

// Uso del encadenamiento
libro.actualizarPrecio(29.99)
     .actualizarCopias(10);
```

## Ejemplo de Uso Completo

```cpp
int main() {
    try {
        // Crear un libro
        Libro libro("9780123456789", "El Quijote", "Cervantes", 1605, 29.99, 5);
        
        // Mostrar datos iniciales
        libro.mostrarDatos();
        
        // Actualizar precio
        libro.setPrecio(34.99);
        
        // Vender algunas copias
        if (libro.venderCopia()) {
            cout << "Venta realizada" << endl;
        }
        
        // Agregar más copias
        libro.agregarCopias(3);
        
        // Mostrar datos actualizados
        libro.mostrarDatos();
        
    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
    }
    
    return 0;
}
```

## Ejercicios Prácticos

### 1. Básico
```cpp
// Implementar método para calcular descuento
double calcularPrecioConDescuento(double porcentaje) const;
```

### 2. Intermedio
```cpp
// Implementar sistema de préstamos
class Libro {
private:
    vector<string> prestamos;
public:
    bool prestar(string usuario);
    bool devolver(string usuario);
    vector<string> getPrestamosActivos() const;
};
```

### 3. Avanzado
```cpp
// Sistema de reseñas y calificaciones
class Libro {
private:
    struct Resena {
        string usuario;
        int calificacion;
        string comentario;
    };
    vector<Resena> resenas;
public:
    void agregarResena(string usuario, int calificacion, string comentario);
    double getCalificacionPromedio() const;
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 3

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Encapsulamiento | Implementa getters/setters básicos | Agrega validación de datos | Implementación robusta con manejo de errores |
| Uso de this | Uso básico en constructores | Uso en métodos encadenados | Implementación avanzada con retorno de referencias |
| Validación | Validaciones básicas | Manejo de casos edge | Sistema completo de validación |
| Documentación | Comentarios básicos | Documentación detallada | Documentación completa con ejemplos |

### Entregables
1. Implementación de la clase `Libro`
2. Programa de prueba con manejo de errores
3. Documentación del código
4. Casos de prueba para validaciones

## Preparación para la Semana 4

### Temas a Revisar
- Vectores y arreglos
- Iteración sobre colecciones
- Manejo de memoria dinámica
- STL básico

### Lecturas Recomendadas
1. "Effective Modern C++" - Scott Meyers
2. "C++ Standard Library Tutorial" - Nicolai Josuttis
3. Documentación de STL sobre contenedores

## Recursos Adicionales
- [C++ Core Guidelines sobre clases](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c-classes-and-class-hierarchies)
- [Tutorial sobre encapsulamiento](https://www.learncpp.com/cpp-tutorial/81-welcome-to-object-oriented-programming/)
- [Ejemplos de buenas prácticas en C++](https://github.com/isocpp/CppCoreGuidelines)
