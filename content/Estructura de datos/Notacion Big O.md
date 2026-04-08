---
title: Notación Big O
type: zettel
created: 2026-04-02
tags: [algoritmos, complejidad, big-o, notacion]
related: [Analisis de Algoritmos, Estructuras de Datos y Análisis de Algoritmos]
---

# Notación Big O

## Definición

La **Notación Big O** es una herramienta matemática que describe el comportamiento límite de una función cuando el argumento tiende a infinito. En análisis de algoritmos, clasifica algoritmos según su **crecimiento asintótico**.

## Tabla de Complejidades

| Complejidad | Notación | Ejemplo | Descripción |
|-------------|----------|---------|-------------|
| **Constante** | O(1) | Acceso a array por índice | No depende de n |
| **Logarítmica** | O(log n) | Búsqueda binaria | Crecimiento muy lento |
| **Lineal** | O(n) | Búsqueda lineal | Proporcional a n |
| **Lineal-logarítmica** | O(n log n) | Merge Sort, Quick Sort | Eficiente para ordenamiento |
| **Cuadrática** | O(n²) | Bubble Sort, Selection Sort | Crecimiento rápido |
| **Cúbica** | O(n³) | Multiplicación de matrices | |
| **Exponencial** | O(2ⁿ) | Fuerza bruta | Impractical para n > 30 |
| **Factorial** | O(n!) | Permutaciones | Muy lento |

> [!warning] Crecimiento exponencial
> Un algoritmo O(2ⁿ) con n=40 puede tomar miles de años, mientras que O(n²) con n=40 toma solo 1600 operaciones.

## Reglas de Simplificación

### 1. Eliminar constantes

```
O(2n) → O(n)
O(n/2) → O(n)
O(n + 1) → O(n)
```

### 2. Solo importa el término dominante

```
O(n² + n) → O(n²)
O(n³ + n² + n) → O(n³)
```

### 3.drop lower terms

```
O(n² + log n) → O(n²)
```

## Ejemplos por Complejidad

### O(1) - Constante
```go
func primero(arr []int) int {
    return arr[0]
}
```

### O(n) - Lineal
```go
func suma(arr []int) int {
    total := 0
    for _, v := range arr {
        total += v
    }
    return total
}
```

### O(n²) - Cuadrática
```go
func bubbleSort(arr []int) {
    n := len(arr)
    for i := 0; i < n-1; i++ {
        for j := 0; j < n-i-1; j++ {
            if arr[j] > arr[j+1] {
                arr[j], arr[j+1] = arr[j+1], arr[j]
            }
        }
    }
}
```

## Comparación Visual

```
O(1)        ████████████████████████████████████
O(log n)    ██████████████████
O(n)        ████████████████████████████████████
O(n log n)  ████████████████████████████████████████
O(n²)       ██████████████████████████████████████████████████████████████
O(2ⁿ)       ███████████████████████████████████████████████████████████████████████
```

## Referencias

- [[Analisis de Algoritmos]]
- [[Estructuras de Datos y Análisis de Algoritmos]]
