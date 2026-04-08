---
title: Ordenamiento Avanzado
type: zettel
created: 2026-04-03
tags: [algoritmos,ordenamiento,sorting,merge-sort,quick-sort]
related: [Estructuras de Datos y Análisis de Algoritmos, Ordenamiento Basico, Divide and Conquer, Heap]
---

# Ordenamiento Avanzado

## Definición

Los algoritmos de ordenamiento avanzado tienen complejidad **O(n log n)** y son los preferidos para ordenar grandes volúmenes de datos. Usan estrategias como divide y conqueror o estructuras de datos especializadas.

> [!tip] Cuándo usarlos
> Para cualquier array con más de ~100 elementos. Quick Sort y Merge Sort son los más usados en la práctica.

## 1. Merge Sort

### Concepto

Divide y conquista: divide el array en mitades, ordena cada mitad recursivamente, y luego mezcla las mitades ordenadas.

```
División:  [38, 27, 43, 3, 9, 82, 10]
              /           \
           [38,27,43]    [3,9,82,10]
          /     \        /      \
       [38,27]   [43]  [3,9]    [82,10]
       /    \           /   \    /    \
     [38]  [27]        [3]  [9] [82]  [10]

Mezcla:     [27,38]   [3,9]   [10,82]
                  \     /        /
            [3,9,27,38]         [10,82]
                  \              /
              [3,9,10,27,38,82]
```

### Implementación en Go

```go
package main

import "fmt"

// Merge Sort
func mergeSort(arr []int) []int {
    n := len(arr)
    if n <= 1 {
        return arr
    }
    
    mid := n / 2
    left := mergeSort(arr[:mid])
    right := mergeSort(arr[mid:])
    
    return merge(left, right)
}

// Función auxiliar para mezclar dos arrays ordenados
func merge(left, right []int) []int {
    result := make([]int, 0, len(left)+len(right))
    i, j := 0, 0
    
    for i < len(left) && j < len(right) {
        if left[i] <= right[j] {
            result = append(result, left[i])
            i++
        } else {
            result = append(result, right[j])
            j++
        }
    }
    
    // Agregar elementos restantes
    result = append(result, left[i:]...)
    result = append(result, right[j:]...)
    
    return result
}

// Merge Sort in-place (usa menos memoria)
func mergeSortInPlace(arr []int) {
    if len(arr) <= 1 {
        return
    }
    
    mid := len(arr) / 2
    mergeSortInPlace(arr[:mid])
    mergeSortInPlace(arr[mid:])
    mergeInPlace(arr, mid)
}

func mergeInPlace(arr []int, mid int) {
    left := make([]int, mid)
    right := make([]int, len(arr)-mid)
    copy(left, arr[:mid])
    copy(right, arr[mid:])
    
    i, j, k := 0, 0, 0
    
    for i < len(left) && j < len(right) {
        if left[i] <= right[j] {
            arr[k] = left[i]
            i++
        } else {
            arr[k] = right[j]
            j++
        }
        k++
    }
    
    for i < len(left) {
        arr[k] = left[i]
        i++
        k++
    }
    for j < len(right) {
        arr[k] = right[j]
        j++
        k++
    }
}
```

## 2. Quick Sort

### Concepto

Elige un pivote y particiona el array en elementos menores y mayores que el pivote. Luego ordena las particiones recursivamente.

```
Array: [10, 7, 8, 9, 1, 5]
Elegir pivote = 5

Particionar: [1] [5] [10, 7, 8, 9]
Ordenar:     [1] [5] [7, 8, 9, 10]

Resultado: [1, 5, 7, 8, 9, 10]
```

### Implementación en Go

```go
// Quick Sort
func quickSort(arr []int) []int {
    quickSortHelper(arr, 0, len(arr)-1)
    return arr
}

func quickSortHelper(arr []int, low, high int) {
    if low < high {
        pi := partition(arr, low, high)
        quickSortHelper(arr, low, pi-1)
        quickSortHelper(arr, pi+1, high)
    }
}

// Particionar - Lomuto partition scheme
func partition(arr []int, low, high int) int {
    pivot := arr[high]
    i := low - 1
    
    for j := low; j < high; j++ {
        if arr[j] <= pivot {
            i++
            arr[i], arr[j] = arr[j], arr[i]
        }
    }
    
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i + 1
}

// Quick Sort con pivote aleatorio (evita peor caso)
func quickSortRandom(arr []int) {
    quickSortRandomHelper(arr, 0, len(arr)-1)
}

func quickSortRandomHelper(arr []int, low, high int) {
    if low < high {
        pi := partitionRandom(arr, low, high)
        quickSortRandomHelper(arr, low, pi-1)
        quickSortRandomHelper(arr, pi+1, high)
    }
}

func partitionRandom(arr []int, low, high int) int {
    // Elegir pivote aleatorio
    randIndex := low + (high-low)/2 // En Go real, usar rand.Int()
    arr[high], arr[randIndex] = arr[randIndex], arr[high]
    
    pivot := arr[high]
    i := low - 1
    
    for j := low; j < high; j++ {
        if arr[j] <= pivot {
            i++
            arr[i], arr[j] = arr[j], arr[i]
        }
    }
    
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i + 1
}

// Quick Sort iterativo (usando stack)
func quickSortIterative(arr []int) {
    n := len(arr)
    stack := make([]int, 0, n)
    
    stack = append(stack, 0, n-1)
    
    for len(stack) > 0 {
        high := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        low := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        
        pi := partition(arr, low, high)
        
        if pi-1 > low {
            stack = append(stack, low, pi-1)
        }
        if pi+1 < high {
            stack = append(stack, pi+1, high)
        }
    }
}
```

## 3. Heap Sort

### Concepto

Usa la estructura Heap para ordenar. Construye un Max-Heap y luego extrae elementos uno por uno desde la raíz.

```
Construir Max-Heap:     [9, 7, 8, 6, 5, 2, 1]
                        
Extraer 9 (máx):        [8, 7, 1, 6, 5, 2] + 9
Heapify:                [8, 7, 2, 6, 5, 1]
Extraer 8:              [7, 6, 2, 1, 5] + 8, 9
...
Resultado final:        [1, 2, 5, 6, 7, 8, 9]
```

### Implementación en Go

```go
// Heap Sort
func heapSort(arr []int) []int {
    n := len(arr)
    
    // 1. Construir Max-Heap
    for i := n/2 - 1; i >= 0; i-- {
        heapify(arr, n, i)
    }
    
    // 2. Extraer elementos uno por uno
    for i := n - 1; i > 0; i-- {
        // Mover raíz (máximo) al final
        arr[0], arr[i] = arr[i], arr[0]
        
        // Reconstruir heap sin el elemento extraído
        heapify(arr, i, 0)
    }
    
    return arr
}

// Heapify - asegurar propiedad de Max-Heap
func heapify(arr []int, n, i int) {
    largest := i
    left := 2*i + 1
    right := 2*i + 2
    
    if left < n && arr[left] > arr[largest] {
        largest = left
    }
    if right < n && arr[right] > arr[largest] {
        largest = right
    }
    
    if largest != i {
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
    }
}

// Heap Sort descendente (Min-Heap)
func heapSortDescending(arr []int) []int {
    n := len(arr)
    
    // Construir Min-Heap
    for i := n/2 - 1; i >= 0; i-- {
        minHeapify(arr, n, i)
    }
    
    // Extraer
    for i := n - 1; i > 0; i-- {
        arr[0], arr[i] = arr[i], arr[0]
        minHeapify(arr, i, 0)
    }
    
    return arr
}

func minHeapify(arr []int, n, i int) {
    smallest := i
    left := 2*i + 1
    right := 2*i + 2
    
    if left < n && arr[left] < arr[smallest] {
        smallest = left
    }
    if right < n && arr[right] < arr[smallest] {
        smallest = right
    }
    
    if smallest != i {
        arr[i], arr[smallest] = arr[smallest], arr[i]
        minHeapify(arr, n, smallest)
    }
}
```

## Análisis de Complejidad

| Algoritmo | Best Case | Average | Worst Case | Espacio | Estable |
|-----------|-----------|---------|------------|---------|---------|
| **Merge Sort** | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ Sí |
| **Quick Sort** | O(n log n) | O(n log n) | O(n²) | O(log n) | ❌ No |
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ No |

> [!warning] Peor caso de Quick Sort
> Ocurre cuando el pivote es siempre el mínimo o máximo. Para evitarlo, usar pivote aleatorio o Median of Three.

## Comparación Visual

```
Array: [12, 11, 13, 5, 6, 7]

Merge Sort:
  Dividir → [12, 11, 13] | [5, 6, 7]
  Dividir → [12, 11] [13] | [5, 6] [7]
  Dividir → [12] [11] [13] | [5] [6] [7]
  Mezclar → [11, 12, 13] | [5, 6, 7]
  Mezclar → [5, 6, 7, 11, 12, 13]

Quick Sort (pivote=7):
  Particionar → [5, 6] [7] [13, 12, 11]
  Ordenar → [5, 6] [7] [11, 12, 13]
  Resultado → [5, 6, 7, 11, 12, 13]

Heap Sort:
  Max-Heap → [13, 11, 12, 5, 6, 7]
  Extraer 13 → [12, 11, 7, 5, 6] + 13
  Extraer 12 → [11, 6, 7, 5] + 12, 13
  Resultado → [5, 6, 7, 11, 12, 13]
```

## Ejemplo Comparativo

```go
func main() {
    testCases := [][]int{
        {64, 34, 25, 12, 22, 11, 90},
        {1, 2, 3, 4, 5, 6, 7},      // Ya ordenado
        {7, 6, 5, 4, 3, 2, 1},      // Invertido
        {1},                         // Un elemento
    }
    
    for i, tc := range testCases {
        original := make([]int, len(tc))
        copy(original, tc)
        
        result := mergeSort(tc)
        fmt.Printf("Test %d: %v → Merge: %v\n", i+1, original, result)
        
        copy(tc, original)
        quickSort(tc)
        fmt.Printf("Test %d: %v → Quick: %v\n", i+1, original, tc)
        
        copy(tc, original)
        heapSort(tc)
        fmt.Printf("Test %d: %v → Heap: %v\n", i+1, original, tc)
    }
}
```

## Cuándo Usar Cada Uno

| Algoritmo | Mejor Uso | Ventaja | Desventaja |
|-----------|-----------|---------|------------|
| **Merge Sort** | Datos grandes, necesita estabilidad | Estable, siempre O(n log n) | O(n) espacio extra |
| **Quick Sort** | Arrays grandes, memoria limitada | In-place, promedio O(n log n) | Peor caso O(n²) |
| **Heap Sort** | Sistemas con memoria limitada | In-place, siempre O(n log n) | No estable, caché malo |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Ordenamiento Basico]]
- [[Divide and Conquer]]
- [[Heap]]
