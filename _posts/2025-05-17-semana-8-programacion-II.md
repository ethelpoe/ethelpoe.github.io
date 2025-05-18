---
title: "Semana 8: Proyecto Integrador de POO en C++"
date: 2025-05-17T13:30:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Proyecto
  - C++
  - Integración
  - Sistema Completo
---

Esta semana final desarrollaremos un proyecto integrador que combina todos los conceptos aprendidos en un sistema de gestión académica.

## Sistema de Gestión Académica

El proyecto implementará un sistema que gestiona cursos, estudiantes y profesores, aplicando los principios de POO y buenas prácticas de programación.

## Estructura del Proyecto

```cpp
// Interfaces base
class IPersona {
public:
    virtual string getNombre() const = 0;
    virtual string getID() const = 0;
    virtual void mostrarInformacion() const = 0;
    virtual ~IPersona() = default;
};

class IEvaluable {
public:
    virtual double calcularPromedio() const = 0;
    virtual void agregarCalificacion(double calificacion) = 0;
    virtual ~IEvaluable() = default;
};

// Clases base
class Persona : public IPersona {
protected:
    string nombre;
    string id;
    string email;

public:
    Persona(string _nombre, string _id, string _email)
        : nombre(_nombre), id(_id), email(_email) {}

    string getNombre() const override { return nombre; }
    string getID() const override { return id; }
    
    virtual void mostrarInformacion() const override {
        cout << "ID: " << id << endl
             << "Nombre: " << nombre << endl
             << "Email: " << email << endl;
    }
};

// Clase Estudiante
class Estudiante : public Persona, public IEvaluable {
private:
    vector<double> calificaciones;
    string carrera;
    int semestre;

public:
    Estudiante(string nombre, string id, string email,
               string _carrera, int _semestre)
        : Persona(nombre, id, email),
          carrera(_carrera), semestre(_semestre) {}

    double calcularPromedio() const override {
        if (calificaciones.empty()) return 0.0;
        return accumulate(calificaciones.begin(), calificaciones.end(), 0.0) /
               calificaciones.size();
    }

    void agregarCalificacion(double calificacion) override {
        if (calificacion < 0 || calificacion > 100) {
            throw invalid_argument("Calificación fuera de rango (0-100)");
        }
        calificaciones.push_back(calificacion);
    }

    void mostrarInformacion() const override {
        Persona::mostrarInformacion();
        cout << "Carrera: " << carrera << endl
             << "Semestre: " << semestre << endl
             << "Promedio: " << calcularPromedio() << endl;
    }
};

// Clase Profesor
class Profesor : public Persona {
private:
    vector<string> materias;
    string departamento;
    int antiguedad;

public:
    Profesor(string nombre, string id, string email,
             string _departamento, int _antiguedad)
        : Persona(nombre, id, email),
          departamento(_departamento), antiguedad(_antiguedad) {}

    void agregarMateria(const string& materia) {
        materias.push_back(materia);
    }

    void mostrarInformacion() const override {
        Persona::mostrarInformacion();
        cout << "Departamento: " << departamento << endl
             << "Antigüedad: " << antiguedad << " años" << endl
             << "Materias:" << endl;
        for (const auto& materia : materias) {
            cout << "- " << materia << endl;
        }
    }
};

// Clase Curso
class Curso {
private:
    string codigo;
    string nombre;
    Profesor* profesor;
    vector<Estudiante*> estudiantes;
    int capacidadMaxima;

public:
    Curso(string _codigo, string _nombre, int _capacidad)
        : codigo(_codigo), nombre(_nombre),
          capacidadMaxima(_capacidad), profesor(nullptr) {}

    bool agregarEstudiante(Estudiante* estudiante) {
        if (estudiantes.size() >= capacidadMaxima) {
            return false;
        }
        estudiantes.push_back(estudiante);
        return true;
    }

    void asignarProfesor(Profesor* _profesor) {
        profesor = _profesor;
        profesor->agregarMateria(nombre);
    }

    void registrarCalificacion(const string& idEstudiante, double calificacion) {
        auto it = find_if(estudiantes.begin(), estudiantes.end(),
            [&idEstudiante](const Estudiante* e) {
                return e->getID() == idEstudiante;
            });

        if (it != estudiantes.end()) {
            (*it)->agregarCalificacion(calificacion);
        } else {
            throw runtime_error("Estudiante no encontrado");
        }
    }

    void mostrarInformacion() const {
        cout << "\n=== Información del Curso ===" << endl
             << "Código: " << codigo << endl
             << "Nombre: " << nombre << endl
             << "Profesor: " << (profesor ? profesor->getNombre() : "No asignado") << endl
             << "Estudiantes inscritos: " << estudiantes.size() << "/" 
             << capacidadMaxima << endl;
    }

    void generarReporteCalificaciones() const {
        cout << "\n=== Reporte de Calificaciones ===" << endl
             << "Curso: " << nombre << endl
             << "Profesor: " << profesor->getNombre() << endl
             << "\nEstudiantes:" << endl;

        for (const auto& estudiante : estudiantes) {
            cout << estudiante->getNombre() << ": "
                 << estudiante->calcularPromedio() << endl;
        }
    }
};

// Sistema principal
class SistemaAcademico {
private:
    vector<unique_ptr<Estudiante>> estudiantes;
    vector<unique_ptr<Profesor>> profesores;
    vector<unique_ptr<Curso>> cursos;

    // Menú interactivo (implementado en la semana 6)
    void mostrarMenu() {
        cout << "\n=== Sistema Académico ===" << endl
             << "1. Agregar Estudiante" << endl
             << "2. Agregar Profesor" << endl
             << "3. Crear Curso" << endl
             << "4. Inscribir Estudiante en Curso" << endl
             << "5. Asignar Profesor a Curso" << endl
             << "6. Registrar Calificación" << endl
             << "7. Mostrar Reportes" << endl
             << "0. Salir" << endl;
    }

public:
    void ejecutar() {
        int opcion;
        do {
            mostrarMenu();
            cout << "\nElija una opción: ";
            cin >> opcion;
            
            try {
                switch (opcion) {
                    case 1:
                        agregarEstudiante();
                        break;
                    case 2:
                        agregarProfesor();
                        break;
                    case 3:
                        crearCurso();
                        break;
                    // ... más casos
                    case 0:
                        cout << "¡Hasta luego!" << endl;
                        break;
                    default:
                        cout << "Opción inválida" << endl;
                }
            } catch (const exception& e) {
                cerr << "Error: " << e.what() << endl;
            }
        } while (opcion != 0);
    }

    // Implementar métodos del menú...
};
```

## Ejemplo de Uso

```cpp
int main() {
    try {
        // Crear instancias de prueba
        SistemaAcademico sistema;
        
        // Ejecutar sistema interactivo
        sistema.ejecutar();

    } catch (const exception& e) {
        cerr << "Error crítico: " << e.what() << endl;
        return 1;
    }
    
    return 0;
}
```

## Criterios de Evaluación

### Rúbrica del Proyecto Final

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Estructura | Sistema básico funcional | Implementación completa | Diseño extensible y robusto |
| POO | Uso básico de clases | Herencia y polimorfismo | Patrones de diseño avanzados |
| Funcionalidad | Operaciones básicas | Validaciones y reportes | Sistema completo con persistencia |
| Código | Funcional pero simple | Bien organizado | Optimizado y documentado |
| Interfaz | Menú básico | Interfaz intuitiva | UI robusta con validaciones |

### Entregables (30%)
1. Código fuente completo y documentado
2. Diagrama de clases UML
3. Manual de usuario
4. Pruebas unitarias

### Evaluación Final (70%)
- 30% Estructura de clases y herencia
- 25% Encapsulamiento y polimorfismo
- 25% Funcionalidad completa
- 10% Presentación técnica
- 10% Estilo de código y documentación

## Recomendaciones Finales

### Buenas Prácticas
- Utilizar smart pointers para gestión de memoria
- Implementar manejo de excepciones
- Seguir principios SOLID
- Documentar código extensivamente

### Extensiones Sugeridas
1. Persistencia de datos en archivos
2. Interfaz gráfica básica
3. Sistema de autenticación
4. Generación de reportes en PDF

## Recursos Adicionales
- [UML Tutorial](https://www.uml.org/what-is-uml.htm)
- [Design Patterns in Modern C++](https://refactoring.guru/design-patterns/cpp)
- [C++ Best Practices](https://github.com/lefticus/cppbestpractices)
- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
