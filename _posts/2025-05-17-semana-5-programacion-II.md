---
title: "Semana 5: Composición de Clases y Relaciones en C++"
date: 2025-05-17T12:00:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Composición
  - Relaciones
  - C++
  - Clases
---

Esta semana exploraremos las relaciones entre clases, enfocándonos en la composición ("tiene un") mediante un sistema que modela personas y direcciones.

## Composición vs Agregación

La composición es una relación fuerte donde una clase contiene objetos de otra clase como parte de su estado. En nuestro ejemplo, una `Persona` tiene una `Direccion`.

## Implementación del Sistema

```cpp
class Direccion {
private:
    string calle;
    string numero;
    string ciudad;
    string codigoPostal;
    string pais;

public:
    Direccion(string _calle, string _numero, string _ciudad, 
              string _codigoPostal, string _pais = "México")
        : calle(_calle), numero(_numero), ciudad(_ciudad),
          codigoPostal(_codigoPostal), pais(_pais) {}

    // Getters
    string getCalle() const { return calle; }
    string getNumero() const { return numero; }
    string getCiudad() const { return ciudad; }
    string getCodigoPostal() const { return codigoPostal; }
    string getPais() const { return pais; }

    // Setters con validación
    void setCodigoPostal(const string& nuevo) {
        if (nuevo.length() != 5) {
            throw invalid_argument("Código postal debe tener 5 dígitos");
        }
        codigoPostal = nuevo;
    }

    string getDireccionCompleta() const {
        return calle + " " + numero + ", " + ciudad + 
               "\nCP: " + codigoPostal + ", " + pais;
    }
};

class Telefono {
private:
    string codigo;
    string numero;
    bool esMovil;

public:
    Telefono(string _codigo, string _numero, bool _esMovil = true)
        : codigo(_codigo), numero(_numero), esMovil(_esMovil) {}

    string getNumeroCompleto() const {
        return "+" + codigo + " " + numero;
    }

    bool esNumeroMovil() const { return esMovil; }
};

class Persona {
private:
    string nombre;
    string apellidos;
    Direccion direccion;  // Composición
    vector<Telefono> telefonos;  // Composición con múltiples objetos
    string email;

    bool esEmailValido(const string& email) const {
        // Validación básica de email
        return email.find('@') != string::npos &&
               email.find('.') != string::npos;
    }

public:
    Persona(string _nombre, string _apellidos, 
           const Direccion& _direccion, string _email)
        : nombre(_nombre), apellidos(_apellidos),
          direccion(_direccion), email(_email) {
        if (!esEmailValido(email)) {
            throw invalid_argument("Email inválido");
        }
    }

    void agregarTelefono(const Telefono& tel) {
        telefonos.push_back(tel);
    }

    string getNombreCompleto() const {
        return nombre + " " + apellidos;
    }

    const Direccion& getDireccion() const {
        return direccion;
    }

    void setDireccion(const Direccion& nueva) {
        direccion = nueva;
    }

    vector<string> getTelefonos() const {
        vector<string> nums;
        for (const auto& tel : telefonos) {
            nums.push_back(tel.getNumeroCompleto());
        }
        return nums;
    }

    void mostrarInformacion() const {
        cout << "Nombre: " << getNombreCompleto() << endl;
        cout << "Dirección:\n" << direccion.getDireccionCompleta() << endl;
        cout << "Email: " << email << endl;
        cout << "Teléfonos:" << endl;
        for (const auto& tel : telefonos) {
            cout << "- " << tel.getNumeroCompleto() 
                 << (tel.esNumeroMovil() ? " (móvil)" : " (fijo)") << endl;
        }
    }
};
```

## Ejemplo de Uso

```cpp
int main() {
    try {
        // Crear una dirección
        Direccion dir("Av. Universidad", "3000", "Ciudad de México", "04510");

        // Crear una persona
        Persona persona("Juan", "Pérez", dir, "juan.perez@email.com");

        // Agregar teléfonos
        persona.agregarTelefono(Telefono("52", "5555123456"));
        persona.agregarTelefono(Telefono("52", "5599887766", false));

        // Mostrar información
        persona.mostrarInformacion();

        // Cambiar dirección
        Direccion nuevaDir("Reforma", "222", "Ciudad de México", "06600");
        persona.setDireccion(nuevaDir);

        cout << "\nDespués de cambiar dirección:\n" << endl;
        persona.mostrarInformacion();

    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
    }

    return 0;
}
```

## Conceptos Clave

### 1. Composición
- Relación "tiene un"
- Ciclo de vida dependiente
- Encapsulamiento de componentes

### 2. Referencias y Objetos
- Paso por referencia vs valor
- Referencias constantes
- Manejo de memoria

### 3. Validación de Datos
- Validación en constructores
- Validación en setters
- Manejo de errores

## Ejercicios Prácticos

### 1. Básico
```cpp
// Agregar historial de direcciones
class Persona {
private:
    vector<Direccion> historialDirecciones;
public:
    void agregarDireccionAHistorial();
    vector<Direccion> getHistorialDirecciones() const;
};
```

### 2. Intermedio
```cpp
// Implementar sistema de contactos favoritos
class Persona {
private:
    vector<Persona*> contactosFavoritos;
public:
    void agregarFavorito(Persona* contacto);
    void mostrarFavoritos() const;
};
```

### 3. Avanzado
```cpp
// Sistema de grupos familiares
class GrupoFamiliar {
private:
    Persona* responsable;
    vector<Persona*> miembros;
    Direccion direccionFamiliar;
public:
    void agregarMiembro(Persona* persona);
    void cambiarDireccionFamiliar(const Direccion& nueva);
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 5

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Composición | Implementa relaciones básicas | Maneja múltiples componentes | Implementa relaciones complejas |
| Validación | Validaciones básicas | Validaciones completas | Sistema robusto de validación |
| Funcionalidad | Métodos básicos funcionan | Implementa características extra | Sistema completo con historial |
| Documentación | Comentarios básicos | Documentación de relaciones | Documentación completa con UML |

### Entregables
1. Implementación de las clases con composición
2. Programa de prueba con diferentes escenarios
3. Diagrama de clases (UML)
4. Documentación del código

## Preparación para la Semana 6

### Temas a Revisar
- Manejo de eventos
- Programación basada en inputs
- Interfaces de usuario en consola
- Control de flujo avanzado

### Lecturas Recomendadas
1. "Design Patterns" - Gang of Four (Capítulo de Composite)
2. "Clean Code" - Robert C. Martin (Capítulo de Objetos y Estructuras)
3. "UML Distilled" - Martin Fowler

## Recursos Adicionales
- [Tutorial de UML](https://www.uml.org/what-is-uml.htm)
- [C++ Reference - Composición](https://en.cppreference.com/w/)
- [Patrones de Diseño en C++](https://refactoring.guru/design-patterns/cpp)
