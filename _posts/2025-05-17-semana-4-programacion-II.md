---
title: "C++ POO - 4: Composición y Agregación"
date: 2025-05-17T11:30:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Vectores
  - STL
  - C++
  - Estructuras de Datos
---

Esta semana exploramos las estructuras de datos lineales y su implementación en POO, utilizando un sistema de registro de estudiantes como ejemplo práctico.

## Vectores vs Arrays en C++

La STL (Standard Template Library) nos proporciona `vector`, una alternativa moderna y segura a los arrays tradicionales:
- Tamaño dinámico
- Métodos integrados
- Seguridad de tipos
- Manejo automático de memoria

## Implementación del Sistema de Registro

```cpp
#include <vector>
#include <algorithm>
#include <stdexcept>

class Estudiante {
private:
    string matricula;
    string nombre;
    vector<double> calificaciones;

public:
    Estudiante(string _matricula, string _nombre) 
        : matricula(_matricula), nombre(_nombre) {}

    // Getters
    string getMatricula() const { return matricula; }
    string getNombre() const { return nombre; }
    
    // Métodos para calificaciones
    void agregarCalificacion(double calificacion) {
        if (calificacion < 0 || calificacion > 100) {
            throw invalid_argument("Calificación fuera de rango (0-100)");
        }
        calificaciones.push_back(calificacion);
    }

    double getPromedio() const {
        if (calificaciones.empty()) return 0.0;
        
        double suma = 0;
        for (double cal : calificaciones) {
            suma += cal;
        }
        return suma / calificaciones.size();
    }
};

class RegistroEstudiantes {
private:
    vector<Estudiante> estudiantes;

    // Método auxiliar para búsqueda
    int encontrarIndice(const string& matricula) const {
        for (size_t i = 0; i < estudiantes.size(); i++) {
            if (estudiantes[i].getMatricula() == matricula) {
                return i;
            }
        }
        return -1;
    }

public:
    void agregarEstudiante(const Estudiante& estudiante) {
        if (encontrarIndice(estudiante.getMatricula()) != -1) {
            throw invalid_argument("Estudiante ya existe");
        }
        estudiantes.push_back(estudiante);
    }

    void agregarCalificacion(const string& matricula, double calificacion) {
        int indice = encontrarIndice(matricula);
        if (indice == -1) {
            throw invalid_argument("Estudiante no encontrado");
        }
        estudiantes[indice].agregarCalificacion(calificacion);
    }

    vector<Estudiante> obtenerAprobados(double calificacionMinima = 70.0) const {
        vector<Estudiante> aprobados;
        copy_if(estudiantes.begin(), estudiantes.end(), 
                back_inserter(aprobados),
                [calificacionMinima](const Estudiante& e) {
                    return e.getPromedio() >= calificacionMinima;
                });
        return aprobados;
    }

    void mostrarTodos() const {
        for (const auto& estudiante : estudiantes) {
            cout << "Matrícula: " << estudiante.getMatricula() 
                 << ", Nombre: " << estudiante.getNombre()
                 << ", Promedio: " << estudiante.getPromedio() << endl;
        }
    }
};
```

## Ejemplo de Uso

```cpp
int main() {
    try {
        RegistroEstudiantes registro;

        // Agregar estudiantes
        registro.agregarEstudiante(Estudiante("A001", "Ana García"));
        registro.agregarEstudiante(Estudiante("A002", "Carlos López"));
        registro.agregarEstudiante(Estudiante("A003", "María Rodríguez"));

        // Agregar calificaciones
        registro.agregarCalificacion("A001", 85.5);
        registro.agregarCalificacion("A001", 90.0);
        registro.agregarCalificacion("A002", 65.0);
        registro.agregarCalificacion("A003", 95.0);

        // Mostrar todos los estudiantes
        cout << "Lista completa de estudiantes:" << endl;
        registro.mostrarTodos();

        // Mostrar aprobados
        cout << "\nEstudiantes aprobados:" << endl;
        auto aprobados = registro.obtenerAprobados();
        for (const auto& estudiante : aprobados) {
            cout << estudiante.getNombre() << " - " 
                 << estudiante.getPromedio() << endl;
        }

    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
    }

    return 0;
}
```

## Conceptos Clave

### 1. Métodos de Vector
- `push_back()`: Agregar elementos
- `size()`: Obtener tamaño
- `empty()`: Verificar si está vacío
- `begin()/end()`: Iteradores
- `[]`: Acceso indexado

### 2. Iteración
```cpp
// Forma tradicional
for (size_t i = 0; i < vector.size(); i++)

// Range-based for
for (const auto& elemento : vector)

// Usando iteradores
for (auto it = vector.begin(); it != vector.end(); ++it)
```

### 3. Algoritmos STL
```cpp
// Búsqueda
auto it = find(vec.begin(), vec.end(), valor);

// Ordenamiento
sort(vec.begin(), vec.end());

// Filtrado
copy_if(vec.begin(), vec.end(), back_inserter(resultado), predicado);
```

## Ejercicios Prácticos

### 1. Básico
```cpp
// Implementar búsqueda por nombre
vector<Estudiante> buscarPorNombre(const string& nombre);
```

### 2. Intermedio
```cpp
// Implementar ordenamiento por promedio
void ordenarPorPromedio();
```

### 3. Avanzado
```cpp
// Implementar sistema de grupos y estadísticas
class Grupo {
    vector<Estudiante*> estudiantes;
    string codigo;
    double promedioGrupal();
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 4

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Implementación | Uso básico de vectores | Implementa búsqueda y filtrado | Usa algoritmos STL avanzados |
| Manejo de Datos | Almacenamiento simple | Validación de datos | Optimización de operaciones |
| Funcionalidad | Operaciones básicas | Métodos de búsqueda | Sistema completo con estadísticas |
| Documentación | Comentarios básicos | Documentación de métodos | Documentación completa con ejemplos |

### Entregables
1. Implementación del sistema de registro
2. Programa de prueba con diferentes operaciones
3. Documentación del código
4. Pruebas de rendimiento (opcional)

## Preparación para la Semana 5

### Temas a Revisar
- Composición de clases
- Referencias y punteros
- Relaciones entre objetos
- Manejo de memoria

### Lecturas Recomendadas
1. "Effective STL" - Scott Meyers
2. "C++ Primer" - Capítulo sobre STL
3. Documentación de C++ sobre contenedores

## Recursos Adicionales
- [Tutorial de STL en cplusplus.com](http://www.cplusplus.com/reference/stl/)
- [C++ Reference - Vector](https://en.cppreference.com/w/cpp/container/vector)
- [Ejemplos de algoritmos STL](https://en.cppreference.com/w/cpp/algorithm)

*La comprensión de las estructuras de datos es fundamental para el desarrollo de aplicaciones eficientes.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-3-programacion-II.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← C++ POO - 3: Herencia y Polimorfismo
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-5-programacion-II.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      C++ POO - 5: Plantillas y STL →
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
