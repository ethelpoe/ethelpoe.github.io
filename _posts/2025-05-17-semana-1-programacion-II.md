---
title: "Semana 1: Reforzamiento y Abstracción en POO"
date: 2025-05-17T10:00:00-04:00
categories:
  - Programación II
tags:
  - POO
  - Abstracción
  - C++
  - Números Primos
  - Estructuras Básicas
---

En esta primera semana del curso de Programación II, haremos una transición suave desde la programación estructurada hacia la programación orientada a objetos, utilizando como ejemplo práctico una función para detectar números primos.

## Comparación: Programación Estructurada vs POO

### Enfoque Estructurado
```cpp
// Versión estructurada tradicional
bool esPrimo(int numero) {
    if (numero <= 1) return false;
    for (int i = 2; i * i <= numero; i++) {
        if (numero % i == 0) return false;
    }
    return true;
}

void mostrarPrimos(int limite) {
    for (int i = 1; i <= limite; i++) {
        if (esPrimo(i)) {
            cout << i << " ";
        }
    }
}

int main() {
    mostrarPrimos(100);
    return 0;
}
```

### Enfoque Orientado a Objetos
```cpp
class AnalizadorNumerico {
private:
    int limite;
    vector<bool> esPrimoCache;
    
    void generarCache() {
        esPrimoCache.resize(limite + 1, true);
        esPrimoCache[0] = esPrimoCache[1] = false;
        
        for (int i = 2; i * i <= limite; i++) {
            if (esPrimoCache[i]) {
                for (int j = i * i; j <= limite; j += i) {
                    esPrimoCache[j] = false;
                }
            }
        }
    }

public:
    AnalizadorNumerico(int limiteMaximo) : limite(limiteMaximo) {
        generarCache();
    }
    
    bool esPrimo(int numero) const {
        if (numero > limite || numero < 0) {
            throw invalid_argument("Número fuera de rango");
        }
        return esPrimoCache[numero];
    }
    
    vector<int> obtenerPrimosHasta(int n) const {
        vector<int> primos;
        for (int i = 2; i <= n && i <= limite; i++) {
            if (esPrimoCache[i]) {
                primos.push_back(i);
            }
        }
        return primos;
    }
    
    int contarPrimos(int n) const {
        return count(esPrimoCache.begin(), 
                    esPrimoCache.begin() + min(n + 1, limite + 1), 
                    true);
    }
};
```

## Conceptos Clave Introducidos

### 1. Encapsulamiento
```cpp
class AnalizadorNumerico {
private:
    // Datos internos protegidos
    vector<bool> esPrimoCache;
    
public:
    // Interfaz pública clara
    bool esPrimo(int numero) const;
};
```

### 2. Responsabilidad Única
```cpp
// Cada clase tiene un propósito específico
class GeneradorReporte {
public:
    void mostrarEstadisticas(const AnalizadorNumerico& analizador, int limite) {
        cout << "Números primos encontrados: " 
             << analizador.contarPrimos(limite) << endl;
        
        vector<int> primos = analizador.obtenerPrimosHasta(limite);
        cout << "Listado de primos: ";
        for (int primo : primos) {
            cout << primo << " ";
        }
        cout << endl;
    }
};
```

### 3. Manejo de Errores
```cpp
class RangoInvalido : public exception {
public:
    const char* what() const throw() {
        return "El número está fuera del rango válido";
    }
};

// Uso en la clase principal
bool esPrimo(int numero) const {
    if (numero > limite || numero < 0) {
        throw RangoInvalido();
    }
    return esPrimoCache[numero];
}
```

## Ejemplo de Uso Completo

```cpp
int main() {
    try {
        // Crear analizador para números hasta 1000
        AnalizadorNumerico analizador(1000);
        GeneradorReporte reporte;
        
        // Realizar análisis
        cout << "¿Es 17 primo? " 
             << (analizador.esPrimo(17) ? "Sí" : "No") << endl;
        
        // Mostrar estadísticas
        reporte.mostrarEstadisticas(analizador, 100);
        
    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
        return 1;
    }
    
    return 0;
}
```

## Ejercicios Prácticos

### 1. Básico
```cpp
// Implementar método que devuelva el siguiente número primo
int siguientePrimo(int numero);
```

### 2. Intermedio
```cpp
// Implementar método para verificar si un número es primo gemelo
bool sonPrimosGemelos(int n1, int n2);
```

### 3. Avanzado
```cpp
// Implementar la Criba de Eratóstenes optimizada
void generarPrimosOptimizado();
```

## Puntos Clave de Aprendizaje

### 1. Ventajas de POO
- Código más organizado y mantenible
- Reutilización de código
- Mejor manejo de errores
- Facilita testing

### 2. Principios Introducidos
- Encapsulamiento
- Responsabilidad única
- Manejo de estados

## Criterios de Evaluación

### Rúbrica de la Semana 1

| Criterio | Básico (60-69) | Intermedio (70-89) | Avanzado (90-100) |
|----------|----------------|-------------------|-------------------|
| Implementación | Implementa detector de primos básico | Agrega manejo de errores y optimizaciones | Implementa Criba de Eratóstenes y optimizaciones avanzadas |
| POO | Define clase básica | Implementa encapsulamiento correcto | Aplica todos los principios POO vistos |
| Documentación | Comentarios básicos | Documentación de métodos clara | Documentación completa con ejemplos |
| Funcionalidad | Detecta números primos | Agrega funciones auxiliares | Implementa todas las funciones extra |

### Entregables
1. Implementación de la clase `AnalizadorNumerico`
2. Documentación del código
3. Ejemplos de uso
4. Pruebas unitarias básicas

## Preparación para la Siguiente Semana

### Temas a Revisar
- Constructores y destructores
- Sobrecarga de operadores
- Herencia básica

### Lecturas Recomendadas
1. "Clean Code" - Robert C. Martin (Capítulo sobre Clases)
2. "Effective C++" - Scott Meyers (Items 1-4)
3. Documentación de la STL sobre `vector` y `algorithm`

## Recursos Adicionales
- [Tutorial de C++ en cplusplus.com](http://www.cplusplus.com/doc/tutorial/)
- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
- [Compiler Explorer](https://godbolt.org/) para experimentar con el código

*Este primer acercamiento a la POO establece las bases para el resto del curso, enfatizando la importancia de una buena estructura y diseño en el código.*
