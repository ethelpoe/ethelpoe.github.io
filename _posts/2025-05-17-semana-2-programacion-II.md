---
title: "Semana 2: Estructura de Clases en C++"
date: 2025-05-17T10:30:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Clases
  - C++
  - Objetos
  - Métodos
---

Esta semana nos adentramos en el concepto fundamental de la programación orientada a objetos: las clases. Usaremos como ejemplo práctico la implementación de una clase `Automovil`.

## Clases y Objetos: La Analogía del Molde

Imagina una clase como un molde para hacer galletas. El molde (clase) define la forma, pero no es la galleta en sí. Las galletas que haces con el molde son los objetos o instancias.

## Implementación Base

```cpp
class Automovil {
private:
    // Atributos
    string marca;
    string modelo;
    int anio;
    bool encendido;

public:
    // Constructor
    Automovil(string _marca, string _modelo, int _anio) 
        : marca(_marca), modelo(_modelo), anio(_anio), encendido(false) {}

    // Métodos
    void encender() {
        if (!encendido) {
            encendido = true;
            cout << "El " << marca << " " << modelo << " está encendido" << endl;
        } else {
            cout << "El auto ya está encendido" << endl;
        }
    }

    void apagar() {
        if (encendido) {
            encendido = false;
            cout << "El auto se ha apagado" << endl;
        } else {
            cout << "El auto ya está apagado" << endl;
        }
    }

    void mostrarInformacion() const {
        cout << "Automóvil: " << marca << " " << modelo << " " << anio << endl;
        cout << "Estado: " << (encendido ? "Encendido" : "Apagado") << endl;
    }
};
```

## Ejemplo de Uso

```cpp
int main() {
    // Crear instancias de Automovil
    Automovil miAuto("Toyota", "Corolla", 2024);
    Automovil otroAuto("Honda", "Civic", 2023);

    // Usar los métodos
    miAuto.mostrarInformacion();
    miAuto.encender();
    miAuto.encender();  // Intentar encender de nuevo
    miAuto.apagar();

    otroAuto.mostrarInformacion();
    return 0;
}
```

## Conceptos Clave

### 1. Atributos
- Variables miembro que describen el estado del objeto
- Diferentes niveles de visibilidad (private, public, protected)

### 2. Métodos
- Funciones miembro que definen el comportamiento
- Pueden modificar o consultar el estado del objeto

### 3. Constructor
- Método especial que inicializa el objeto
- Se llama automáticamente al crear una instancia

## Ejercicios Prácticos

### 1. Básico
```cpp
// Agregar método para cambiar el modelo y año
void actualizarModelo(string nuevoModelo, int nuevoAnio);
```

### 2. Intermedio
```cpp
// Implementar sistema de combustible
class Automovil {
private:
    double nivelCombustible;
    const double capacidadMaxima;
public:
    void cargarCombustible(double litros);
    bool tieneSuficienteCombustible() const;
};
```

### 3. Avanzado
```cpp
// Agregar sistema de velocidad y marchas
class Automovil {
private:
    int velocidadActual;
    int marchaActual;
public:
    void acelerar(int incremento);
    void frenar(int decremento);
    void cambiarMarcha(int nuevaMarcha);
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 2

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Estructura | Define clase básica | Implementa atributos y métodos adecuados | Diseño completo con validaciones |
| Encapsulamiento | Usa public/private | Protege datos sensibles | Implementa getters/setters necesarios |
| Funcionalidad | Métodos básicos funcionan | Agrega validaciones | Manejo completo de estados |
| Documentación | Comentarios básicos | Documentación de métodos | Documentación completa con ejemplos |

### Entregables
1. Implementación de la clase `Automovil`
2. Programa de prueba con múltiples instancias
3. Documentación del código
4. Demostración de uso de métodos

## Preparación para la Semana 3

### Temas a Revisar
- Getters y setters
- Constructores y destructores
- Control de acceso
- Palabra clave `this`

### Lecturas Recomendadas
1. "C++ Primer" - Stanley Lippman (Capítulo sobre Clases)
2. "Effective C++" - Scott Meyers (Items sobre construcción/destrucción)
3. Documentación de C++ sobre clases y objetos

## Recursos Adicionales
- [Tutorial de C++ en cplusplus.com](http://www.cplusplus.com/doc/tutorial/classes)
- [C++ Reference](https://en.cppreference.com/w/cpp/language/classes)
- [Ejemplos interactivos en Compiler Explorer](https://godbolt.org/)
