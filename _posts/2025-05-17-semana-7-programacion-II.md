---
title: "C++ POO - 7: Patrones de Diseño"
date: 2025-05-17T13:00:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Herencia
  - Polimorfismo
  - C++
  - Clases Abstractas
---

Esta semana exploraremos la herencia y las clases abstractas mediante un sistema de animales que demuestra el polimorfismo y la especialización de clases.

## Clases Abstractas y Herencia

Las clases abstractas definen una interfaz común para sus clases derivadas, estableciendo un contrato que deben cumplir. La herencia nos permite reutilizar código y crear jerarquías de objetos.

## Implementación del Sistema

```cpp
// Clase base abstracta
class Animal {
protected:
    string nombre;
    int edad;
    double peso;

public:
    Animal(string _nombre, int _edad, double _peso)
        : nombre(_nombre), edad(_edad), peso(_peso) {}
    
    // Métodos virtuales puros (hacen la clase abstracta)
    virtual void emitirSonido() const = 0;
    virtual void moverse() const = 0;
    
    // Método virtual (puede ser sobrescrito)
    virtual void comer() const {
        cout << nombre << " está comiendo..." << endl;
    }
    
    // Método final (no puede ser sobrescrito)
    virtual void mostrarInfo() const final {
        cout << "\nInformación del animal:" << endl;
        cout << "Nombre: " << nombre << endl;
        cout << "Edad: " << edad << " años" << endl;
        cout << "Peso: " << peso << " kg" << endl;
    }

    // Destructor virtual
    virtual ~Animal() = default;
};

class Perro : public Animal {
private:
    string raza;
    bool adiestrado;

public:
    Perro(string _nombre, int _edad, double _peso, string _raza, bool _adiestrado)
        : Animal(_nombre, _edad, _peso), raza(_raza), adiestrado(_adiestrado) {}

    void emitirSonido() const override {
        cout << nombre << " dice: ¡Guau guau!" << endl;
    }

    void moverse() const override {
        cout << nombre << " corre en cuatro patas" << endl;
    }

    void comer() const override {
        Animal::comer();  // Llamada al método de la clase base
        cout << "Come croquetas para perro" << endl;
    }

    // Métodos específicos de Perro
    void jugarPelota() const {
        if (adiestrado) {
            cout << nombre << " atrapa la pelota y la devuelve" << endl;
        } else {
            cout << nombre << " persigue la pelota pero no la devuelve" << endl;
        }
    }
};

class Gato : public Animal {
private:
    bool domestico;
    int vidasRestantes;

public:
    Gato(string _nombre, int _edad, double _peso, bool _domestico)
        : Animal(_nombre, _edad, _peso), 
          domestico(_domestico), vidasRestantes(9) {}

    void emitirSonido() const override {
        cout << nombre << " dice: ¡Miau!" << endl;
    }

    void moverse() const override {
        cout << nombre << " se mueve sigilosamente" << endl;
    }

    void comer() const override {
        Animal::comer();
        if (domestico) {
            cout << "Come alimento balanceado para gatos" << endl;
        } else {
            cout << "Caza su comida" << endl;
        }
    }

    // Métodos específicos de Gato
    void arañar() const {
        cout << nombre << " araña el sofá" << endl;
    }
};

class Ave : public Animal {
private:
    bool puedeVolar;
    double envergaduraAlas;

public:
    Ave(string _nombre, int _edad, double _peso, 
        bool _puedeVolar, double _envergadura)
        : Animal(_nombre, _edad, _peso),
          puedeVolar(_puedeVolar), envergaduraAlas(_envergadura) {}

    void emitirSonido() const override {
        cout << nombre << " hace: ¡Pío pío!" << endl;
    }

    void moverse() const override {
        if (puedeVolar) {
            cout << nombre << " vuela por el aire" << endl;
        } else {
            cout << nombre << " camina y salta" << endl;
        }
    }

    // Métodos específicos de Ave
    void volar() const {
        if (puedeVolar) {
            cout << nombre << " se eleva y planea con sus alas de " 
                 << envergaduraAlas << "m" << endl;
        } else {
            cout << nombre << " no puede volar" << endl;
        }
    }
};
```

## Ejemplo de Uso del Polimorfismo

```cpp
int main() {
    try {
        vector<unique_ptr<Animal>> animales;
        
        // Crear diferentes tipos de animales
        animales.push_back(make_unique<Perro>("Bobby", 3, 15.5, "Labrador", true));
        animales.push_back(make_unique<Gato>("Luna", 2, 4.2, true));
        animales.push_back(make_unique<Ave>("Piolín", 1, 0.3, true, 0.25));

        // Demostrar polimorfismo
        cout << "Demostración de polimorfismo:" << endl;
        for (const auto& animal : animales) {
            animal->mostrarInfo();
            animal->emitirSonido();
            animal->moverse();
            animal->comer();
            cout << "-------------------" << endl;
        }

        // Usar métodos específicos (necesita downcasting)
        if (auto perro = dynamic_cast<Perro*>(animales[0].get())) {
            perro->jugarPelota();
        }

    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
        return 1;
    }

    return 0;
}
```

## Conceptos Clave

### 1. Herencia
- Reutilización de código
- Jerarquía de clases
- Acceso protected

### 2. Polimorfismo
- Métodos virtuales
- Override y final
- Polimorfismo en tiempo de ejecución

### 3. Clases Abstractas
- Métodos virtuales puros
- Interfaces
- Contratos de implementación

## Ejercicios Prácticos

### 1. Básico
```cpp
// Implementar otra clase derivada
class Pez : public Animal {
protected:
    bool aguaDulce;
public:
    virtual void nadar() = 0;
};
```

### 2. Intermedio
```cpp
// Sistema de interacción entre animales
class Interaccion {
public:
    static void interactuar(const Animal& a1, const Animal& a2);
    static bool sonCompatibles(const Animal& a1, const Animal& a2);
};
```

### 3. Avanzado
```cpp
// Sistema de comportamientos compuestos
class Comportamiento {
public:
    virtual void ejecutar(Animal& animal) = 0;
};

class ComportamientoCompuesto : public Comportamiento {
private:
    vector<unique_ptr<Comportamiento>> comportamientos;
public:
    void agregarComportamiento(unique_ptr<Comportamiento> comp);
    void ejecutar(Animal& animal) override;
};
```

## Criterios de Evaluación

### Rúbrica de la Semana 7

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Herencia | Implementa herencia básica | Usa herencia múltiple | Diseño jerárquico complejo |
| Polimorfismo | Usa métodos virtuales | Implementa override | Usa polimorfismo avanzado |
| Abstracción | Define interfaz básica | Implementa clases abstractas | Diseño completo con interfaces |
| Documentación | Comentarios básicos | Documentación de jerarquía | Documentación completa con UML |

### Entregables
1. Implementación del sistema de animales
2. Diagrama de clases (UML)
3. Documentación del código
4. Pruebas de polimorfismo

## Preparación para la Semana 8

### Temas a Revisar
- Patrones de diseño
- SOLID principles
- Excepciones y manejo de errores
- Templates básicos

### Lecturas Recomendadas
1. "Design Patterns" - Gang of Four
2. "Clean Code" - Robert C. Martin (Capítulo de SOLID)
3. "Modern C++ Design" - Andrei Alexandrescu

## Recursos Adicionales
- [C++ Inheritance](https://en.cppreference.com/w/cpp/language/inheritance)
- [Virtual Functions](https://en.cppreference.com/w/cpp/language/virtual)
- [SOLID Principles in C++](https://en.wikipedia.org/wiki/SOLID)

*Los patrones de diseño y las clases abstractas son herramientas poderosas para crear código flexible y reutilizable.*

---

<div class="navigation-buttons">
  <div class="nav-previous">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-6-programacion-II.md %}" class="previous-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px; margin-right: 10px;">
      ← C++ POO - 6: Manejo de Excepciones
    </a>
  </div>
  
  <div class="nav-next">
    <a href="{{ site.baseurl }}{% link _posts/2025-05-17-semana-8-programacion-II.md %}" class="next-button" style="padding: 10px 20px; background-color: #4CAF50; color: white; text-decoration: none; border-radius: 5px;">
      C++ POO - 8: Proyecto Final →
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
