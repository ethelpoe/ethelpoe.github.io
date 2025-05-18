---
title: "C++ POO - 6: Manejo de Excepciones"
date: 2025-05-17T12:30:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Eventos
  - Input/Output
  - C++
  - Menús Interactivos
---

Esta semana exploraremos la programación basada en eventos mediante la implementación de un sistema de menú interactivo que gestiona una biblioteca.

## Manejo de Eventos y Estados

En programación orientada a objetos, los eventos son acciones que desencadenan cambios en el estado de los objetos. Implementaremos un sistema que reacciona a las decisiones del usuario.

## Implementación del Sistema

```cpp
class Menu;  // Forward declaration

// Interfaz para observadores de eventos
class MenuObserver {
public:
    virtual void onOpcionSeleccionada(int opcion) = 0;
    virtual ~MenuObserver() = default;
};

class Menu {
private:
    string titulo;
    vector<string> opciones;
    vector<MenuObserver*> observers;
    bool activo;

public:
    Menu(string _titulo) : titulo(_titulo), activo(true) {}

    void agregarOpcion(const string& opcion) {
        opciones.push_back(opcion);
    }

    void agregarObservador(MenuObserver* observer) {
        observers.push_back(observer);
    }

    void mostrar() const {
        system("cls");  // Limpiar pantalla en Windows
        cout << "\n=== " << titulo << " ===" << endl;
        for (size_t i = 0; i < opciones.size(); i++) {
            cout << i + 1 << ". " << opciones[i] << endl;
        }
        cout << "0. Salir" << endl;
        cout << "\nSeleccione una opción: ";
    }

    void ejecutar() {
        while (activo) {
            mostrar();
            int opcion = leerOpcion();
            if (opcion == 0) {
                activo = false;
            } else {
                notificarObservadores(opcion - 1);
            }
        }
    }

private:
    int leerOpcion() {
        int opcion;
        while (true) {
            if (cin >> opcion) {
                if (opcion >= 0 && opcion <= static_cast<int>(opciones.size())) {
                    return opcion;
                }
            }
            cout << "Opción inválida. Intente de nuevo: ";
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
        }
    }

    void notificarObservadores(int opcion) {
        for (auto observer : observers) {
            observer->onOpcionSeleccionada(opcion);
        }
    }
};

class SistemaBiblioteca : public MenuObserver {
private:
    vector<Libro> libros;
    Menu menuPrincipal;
    Menu menuLibros;

    void inicializarMenus() {
        // Menú principal
        menuPrincipal.agregarOpcion("Gestionar Libros");
        menuPrincipal.agregarOpcion("Buscar Libro");
        menuPrincipal.agregarOpcion("Mostrar Todos");
        menuPrincipal.agregarObservador(this);

        // Menú de libros
        menuLibros.agregarOpcion("Agregar Libro");
        menuLibros.agregarOpcion("Eliminar Libro");
        menuLibros.agregarOpcion("Modificar Libro");
        menuLibros.agregarOpcion("Volver");
        menuLibros.agregarObservador(this);
    }

public:
    SistemaBiblioteca() 
        : menuPrincipal("Sistema de Biblioteca"),
          menuLibros("Gestión de Libros") {
        inicializarMenus();
    }

    void onOpcionSeleccionada(int opcion) override {
        switch (opcion) {
            case 0:  // Gestionar Libros
                menuLibros.ejecutar();
                break;
            case 1:  // Buscar Libro
                buscarLibro();
                break;
            case 2:  // Mostrar Todos
                mostrarLibros();
                break;
            // Más casos según el menú activo
        }
        pausar();
    }

    void ejecutar() {
        menuPrincipal.ejecutar();
    }

private:
    void buscarLibro() {
        cout << "\nIngrese título a buscar: ";
        string titulo;
        cin.ignore();
        getline(cin, titulo);

        bool encontrado = false;
        for (const auto& libro : libros) {
            if (libro.getTitulo().find(titulo) != string::npos) {
                libro.mostrarDatos();
                encontrado = true;
            }
        }

        if (!encontrado) {
            cout << "No se encontraron libros con ese título." << endl;
        }
    }

    void mostrarLibros() {
        if (libros.empty()) {
            cout << "\nNo hay libros registrados." << endl;
            return;
        }

        cout << "\nListado de Libros:" << endl;
        cout << "=================" << endl;
        for (const auto& libro : libros) {
            libro.mostrarDatos();
            cout << "-----------------" << endl;
        }
    }

    void pausar() {
        cout << "\nPresione Enter para continuar...";
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
        cin.get();
    }
};
```

## Ejemplo de Uso

```cpp
int main() {
    try {
        SistemaBiblioteca sistema;
        sistema.ejecutar();
    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
        return 1;
    }
    return 0;
}
```

## Conceptos Clave

### 1. Manejo de Eventos
- Patrón Observador
- Callbacks y notificaciones
- Estados y transiciones

### 2. Entrada/Salida
- Validación de inputs
- Limpieza de buffer
- Formateo de salida

### 3. Interfaz de Usuario
- Menús interactivos
- Navegación jerárquica
- Feedback al usuario

## Ejercicios Prácticos

### 1. Básico
```cpp
// Implementar historial de comandos
class Menu {
private:
    vector<string> historialComandos;
public:
    void guardarComando(const string& comando);
    void mostrarHistorial() const;
};
```

### 2. Intermedio
```cpp
// Sistema de permisos de usuario
class Usuario {
private:
    string nombre;
    vector<string> permisos;
public:
    bool tienePermiso(const string& permiso) const;
    void agregarPermiso(const string& permiso);
};
```

### 3. Avanzado
```cpp
// Implementar sistema de deshacer/rehacer
class ComandoBase {
public:
    virtual void ejecutar() = 0;
    virtual void deshacer() = 0;
    virtual ~ComandoBase() = default;
};

class SistemaComandos {
private:
    stack<unique_ptr<ComandoBase>> historial;
    stack<unique_ptr<ComandoBase>> deshecho;
public:
    void ejecutarComando(unique_ptr<ComandoBase> cmd);
    void deshacer();
    void rehacer();
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 6

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Eventos | Manejo básico de inputs | Implementa observadores | Sistema completo de eventos |
| Interfaz | Menú simple funcional | Múltiples niveles de menú | UI intuitiva y robusta |
| Validación | Validación básica | Manejo de errores | Sistema completo de validación |
| Documentación | Comentarios básicos | Documentación de flujos | Documentación completa con diagramas |

### Entregables
1. Implementación del sistema de menús
2. Programa completo con manejo de eventos
3. Documentación del código
4. Diagrama de flujo de la aplicación

## Preparación para la Semana 7

### Temas a Revisar
- Herencia y polimorfismo
- Clases abstractas
- Interfaces
- Métodos virtuales

### Lecturas Recomendadas
1. "Design Patterns" - Gang of Four (Observer Pattern)
2. "C++ GUI Programming with Qt" - Jasmin Blanchette
3. "Clean Code" - Robert C. Martin (User Interfaces)

## Recursos Adicionales
- [C++ Input/Output library](http://www.cplusplus.com/reference/iostream/)
- [Command Pattern en C++](https://refactoring.guru/design-patterns/command/cpp/example)
- [Event-Driven Programming](https://en.wikipedia.org/wiki/Event-driven_programming)

*El manejo efectivo de eventos y estados es crucial para crear aplicaciones interactivas robustas.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-5-programacion-II.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← C++ POO - 5: Plantillas y STL
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-7-programacion-II.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      C++ POO - 7: Patrones de Diseño →
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
