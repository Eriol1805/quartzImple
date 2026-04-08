---
title: Ordenamiento Basico
type: zettel
created: 2026-04-03
tags: [algoritmos,ordenamiento,sorting,bubble-sort]
related: [Estructuras de Datos y Análisis de Algoritmos, Ordenamiento Avanzado, Recursion]
---

# Ordenamiento Basico

## Definición

Los algoritmos de ordenamiento básico son aquellos con complejidad **O(n²)** que funcionan comparando elementos adyacentes. Son fáciles de entender e implementar, pero no son eficientes para grandes volúmenes de datos.

> [!tip] Cuándo usarlos
> Solo para arrays pequeños (< 100 elementos) o cuando la simplicidad de código es prioritaria sobre el rendimiento.

## 1. Bubble Sort

### Concepto

Compara elementos adyacentes y los intercambia si están en orden incorrecto. "Burbujea" el elemento más grande hacia el final en cada pasada.

```
Pasada 1: [5, 3, 8, 1] → [3, 5, 1, 8] → [3, 1, 5, 8] → [1, 3, 5, 8]
Pasada 2: [1, 3, 5, 8] → [1, 3, 5, 8] (sin cambios)
```

### Implementación en Go

```go
package main

import "fmt"

// Bubble Sort básico
func bubbleSort(arr []int) []int {
    n := len(arr)
    
    for i := 0; i < n-1; i++ {
        swapped := false
        
        for j := 0; j < n-i-1; j++ {
            if arr[j] > arr[j+1] {
                arr[j], arr[j+1] = arr[j+1], arr[j]
                swapped = true
            }
        }
        
        // Optimización: si no hubo cambios, el array ya está ordenado
        if !swapped {
            break
        }
    }
    
    return arr
}

// Bubble Sort mejorado - optimizado para ambos extremos
func bubbleSortOptimized(arr []int) []int {
    n := len(arr)
    left, right := 0, n-1
    
    for left < right {
        // Burbuja hacia la derecha (mayor al final)
        for i := left; i < right; i++ {
            if arr[i] > arr[i+1] {
                arr[i], arr[i+1] = arr[i+1], arr[i]
            }
        }
        right--
        
        // Burbuja hacia la izquierda (menor al inicio)
        for i := right; i > left; i-- {
            if arr[i] < arr[i-1] {
                arr[i], arr[i-1] = arr[i-1], arr[i]
            }
        }
        left++
    }
    
    return arr
}
```

## 2. Selection Sort

### Concepto

Encuentra el elemento mínimo y lo coloca al inicio. Repite para el resto del array.

```
Array: [64, 25, 12, 22, 11]
Paso 1: mínimo = 11 → [11, 25, 12, 22, 64]
Paso 2: mínimo = 12 → [11, 12, 25, 22, 64]
Paso 3: mínimo = 22 → [11, 12, 22, 25, 64]
Paso 4: mínimo = 25 → [11, 12, 22, 25, 64]
```

### Implementación en Go

```go
// Selection Sort
func selectionSort(arr []int) []int {
    n := len(arr)
    
    for i := 0; i < n-1; i++ {
        // Encontrar el índice del mínimo
        minIdx := i
        for j := i + 1; j < n; j++ {
            if arr[j] < arr[minIdx] {
                minIdx = j
            }
        }
        
        // Intercambiar
        if minIdx != i {
            arr[i], arr[minIdx] = arr[minIdx], arr[i]
        }
    }
    
    return arr
}

// Selection Sort con自作
func selectionSortDescending(arr []int) []int {
    n := len(arr)
    
    for i := 0; i < n-1; i++ {
        maxIdx := i
        for j := i + 1; j < n; j++ {
            if arr[j] > arr[maxIdx] {
                maxIdx = j
            }
        }
        arr[i], arr[maxIdx] = arr[maxIdx], arr[i]
    }
    
    return arr
}
```

## 3. Insertion Sort

### Concepto

Construye el array ordenado inserting cada elemento en su posición correcta en la parte ya ordenada. Como ordenar cartas en la mano.

```
Array: [12, 11, 13, 5, 6]

Iter 1: 11 < 12 → insertar antes → [11, 12, 13, 5, 6]
Iter 2: 13 ok → [11, 12, 13, 5, 6]
Iter 3: 5 < todos → [5, 11, 12, 13, 6]
Iter 4: 6 < 13, 12, 11 → [5, 6, 11, 12, 13]
```

### Implementación en Go

```go
// Insertion Sort
func insertionSort(arr []int) []int {
    n := len(arr)
    
    for i := 1; i < n; i++ {
        key := arr[i]
        j := i - 1
        
        // Mover elementos mayores que key una posición adelante
        for j >= 0 && arr[j] > key {
            arr[j+1] = arr[j]
            j--
        }
        
        arr[j+1] = key
    }
    
    return arr
}

// Insertion Sort para slices grandes (usa copy y append)
func insertionSortOptimized(arr []int) []int {
    n := len(arr)
    
    for i := 1; i < n; i++ {
        key := arr[i]
        
        // Encontrar posición usando binary search
        pos := binarySearchPos(arr, 0, i-1, key)
        
        // Insertar en posición
        arr = append(arr[:pos], append([]int{key}, arr[pos:i]...)...)
    }
    
    return arr
}

// Binary search para encontrar posición
func binarySearchPos(arr []int, left, right, key int) int {
    for left < right {
        mid := (left + right) / 2
        if arr[mid] <= key {
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
}

// Insertion Sort recursivo
func insertionSortRecursive(arr []int, n int) []int {
    if n <= 1 {
        return arr
    }
    
    insertionSortRecursive(arr, n-1)
    
    last := arr[n-1]
    j := n - 2
    
    for j >= 0 && arr[j] > last {
        arr[j+1] = arr[j]
        j--
    }
    arr[j+1] = last
    
    return arr
}
```

## Análisis de Complejidad

| Algoritmo | Best Case | Average | Worst Case | Espacio |
|-----------|-----------|---------|------------|---------|
| **Bubble Sort** | O(n) | O(n²) | O(n²) | O(1) |
| **Selection Sort** | O(n²) | O(n²) | O(n²) | O(1) |
| **Insertion Sort** | O(n) | O(n²) | O(n²) | O(1) |

> [!note] Análisis detallado
> - **Bubble Sort**: Mejor caso O(n) cuando ya está ordenado (gracias a optimización)
> - **Selection Sort**: Siempre O(n²), no se beneficia de datos parcialmente ordenados
> - **Insertion Sort**: O(n) cuando el array ya está casi ordenado (ideal para streams)

## Comparación Visual

```
Array inicial: [5, 3, 8, 1, 2]

Bubble Sort:
  Pasada 1: [3, 5, 1, 2, 8]
  Pasada 2: [3, 1, 2, 5, 8]
  Pasada 3: [1, 3, 2, 5, 8]
  Pasada 4: [1, 2, 3, 5, 8]
  Total: 4n-1 comparaciones

Selection Sort:
  Encontrar 1 → [1, 3, 8, 5, 2]
  Encontrar 2 → [1, 2, 8, 5, 3]
  Encontrar 3 → [1, 2, 3, 5, 8]
  Encontrar 5 → [1, 2, 3, 5, 8]
  Total: n(n-1)/2 comparaciones (fijo)

Insertion Sort:
  3 < 5 → [3, 5, 8, 1, 2]
  8 ok → [3, 5, 8, 1, 2]
  1 < todos → [1, 3, 5, 8, 2]
  2 < 8,5,3 → [1, 2, 3, 5, 8]
```

## Ejemplo Comparativo

```go
func main() {
    testCases := [][]int{
        {64, 34, 25, 12, 22, 11, 90},
        {1, 2, 3, 4, 5, 6, 7},        // Ya ordenado
        {7, 6, 5, 4, 3, 2, 1},        // Invertido
        {1},                           // Un elemento
        {},                            // Vacío
    }
    
    for i, tc := range testCases {
        original := make([]int, len(tc))
        copy(original, tc)
        
        bubbleSort(tc)
        fmt.Printf("Test %d: %v → Bubble: %v\n", i+1, original, tc)
        
        copy(tc, original)
        selectionSort(tc)
        fmt.Printf("Test %d: %v → Selection: %v\n", i+1, original, tc)
        
        copy(tc, original)
        insertionSort(tc)
        fmt.Printf("Test %d: %v → Insertion: %v\n", i+1, original, tc)
    }
}
```

## Cuándo Usar Cada Uno

| Algoritmo | Mejor Uso | Ventaja | Desventaja |
|-----------|-----------|---------|------------|
| **Bubble Sort** | Arrays muy pequeños, educacional | Easy de entender | Muy lento |
| **Selection Sort** | Memoria limitada | Writes mínimos | Siempre O(n²) |
| **Insertion Sort** | Datos casi ordenados | O(n) best case, estable | Peor caso O(n²) |

## Estabilidad

| Algoritmo | Estable? |
|-----------|----------|
| **Bubble Sort** | ✅ Sí |
| **Selection Sort** | ❌ No |
| **Insertion Sort** | ✅ Sí |

> [!info] Ordenamiento estable
> Un algoritmo estable mantiene el orden relativo de elementos iguales.

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Ordenamiento Avanzado]]
- [[Recursion]]
