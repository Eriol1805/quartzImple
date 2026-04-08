---
title: Divide and Conquer
type: zettel
created: 2026-04-03
tags: [algoritmos,tecnicas-algoritmicas,divide-and-conquer]
related: [Estructuras de Datos y Análisis de Algoritmos, Recursion, Ordenamiento Avanzado, Greedy, Programacion Dinamica]
---

# Divide and Conquer

## Definición

**Divide and Conquer** (Dividir y Conquistar) es una técnica algorítmica que divide un problema en subproblemas más pequeños del mismo tipo, resuelve cada subproblema recursivamente, y luego combina las soluciones para obtener la solución final.

> [!tip] Tres pasos
> 1. **Dividir**: Romper el problema en subproblemas más pequeños
> 2. **Conquistar**: Resolver los subproblemas recursivamente
> 3. **Combinar**: Unir las soluciones de los subproblemas

## Estructura General

```go
func solve(problema) {
    // 1. Dividir: si el problema es pequeño, resolver directamente
    if problema.esPequeño() {
        return problema.resolverDirectamente()
    }
    
    // Dividir en subproblemas
    sub1 := problema.dividirEnDos()[0]
    sub2 := problema.dividirEnDos()[1]
    
    // 2. Conquistar: resolver cada subproblema
    resultado1 := solve(sub1)
    resultado2 := solve(sub2)
    
    // 3. Combinar: unir resultados
    return combinar(resultado1, resultado2)
}
```

## Algoritmos Clásicos de Divide and Conquer

### 1. Merge Sort

El ejemplo más característico.

```go
// Merge Sort usando divide and conquer
func mergeSort(arr []int) []int {
    n := len(arr)
    
    // Caso base: array de 1 elemento ya está ordenado
    if n <= 1 {
        return arr
    }
    
    // DIVIDIR: dividir en dos mitades
    mid := n / 2
    left := mergeSort(arr[:mid])   // Conquer
    right := mergeSort(arr[mid:])  // Conquer
    
    // COMBINAR: mezclar las mitades ordenadas
    return merge(left, right)
}

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
    
    result = append(result, left[i:]...)
    result = append(result, right[j:]...)
    
    return result
}
```

### 2. Quick Sort

```go
func quickSort(arr []int, low, high int) {
    // Caso base
    if low >= high {
        return
    }
    
    // DIVIDIR + CONQUISTAR: particionar y obtener pivote
    pivotIdx := partition(arr, low, high)
    
    // COMBINAR: ordenar ambas particiones
    quickSort(arr, low, pivotIdx-1)   // izquierda
    quickSort(arr, pivotIdx+1, high)  // derecha
}

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
```

### 3. Binary Search

```go
// Búsqueda binaria - divide and conquer
func binarySearch(arr []int, target, left, right int) int {
    // Caso base: no encontrado
    if left > right {
        return -1
    }
    
    // Dividir por la mitad
    mid := (left + right) / 2
    
    // Conquer: verificar si es el objetivo
    if arr[mid] == target {
        return mid
    }
    
    // Conquer + Combinar: buscar en la mitad correcta
    if arr[mid] < target {
        return binarySearch(arr, target, mid+1, right)
    } else {
        return binarySearch(arr, target, left, mid-1)
    }
}

// Versión iterativa
func binarySearchIterative(arr []int, target int) int {
    left, right := 0, len(arr)-1
    
    for left <= right {
        mid := (left + right) / 2
        
        if arr[mid] == target {
            return mid
        } else if arr[mid] < target {
            left = mid + 1
        } else {
            right = mid - 1
        }
    }
    
    return -1
}
```

### 4. Closest Pair of Points

Encuentra el par de puntos más cercanos en un plano.

```go
import (
    "math"
    "sort"
)

type Point struct {
    X float64
    Y float64
}

func closestPair(points []Point) (float64, Point, Point) {
    // Ordenar por X
    sort.Slice(points, func(i, j int) bool {
        return points[i].X < points[j].X
    })
    
    return closestPairHelper(points)
}

func closestPairHelper(points []Point) (float64, Point, Point) {
    n := len(points)
    
    // Caso base
    if n <= 3 {
        return bruteForceClosest(points)
    }
    
    // Dividir
    mid := n / 2
    midPoint := points[mid]
    
    // Conquer
    d1, p1a, p1b := closestPairHelper(points[:mid])
    d2, p2a, p2b := closestPairHelper(points[mid:])
    
    // Combinar
    d := math.Min(d1, d2)
    
    // Verificar puntos cerca de la línea de corte
    var bestA, bestB Point
    if d1 < d2 {
        d, bestA, bestB = d1, p1a, p1b
    } else {
        d, bestA, bestB = d2, p2a, p2b
    }
    
    // Strip: puntos dentro de d de la línea media
    strip := []Point{}
    for _, p := range points {
        if math.Abs(p.X-midPoint.X) < d {
            strip = append(strip, p)
        }
    }
    
    // Buscar el par más cercano en el strip
    dStrip, a, b := stripClosest(strip, d)
    if dStrip < d {
        return dStrip, a, b
    }
    
    return d, bestA, bestB
}

func bruteForceClosest(points []Point) (float64, Point, Point) {
    minDist := math.MaxFloat64
    var bestA, bestB Point
    
    for i := 0; i < len(points); i++ {
        for j := i + 1; j < len(points); j++ {
            dist := distance(points[i], points[j])
            if dist < minDist {
                minDist = dist
                bestA, bestB = points[i], points[j]
            }
        }
    }
    
    return minDist, bestA, bestB
}

func distance(a, b Point) float64 {
    dx := a.X - b.X
    dy := a.Y - b.Y
    return math.Sqrt(dx*dx + dy*dy)
}

func stripClosest(strip []Point, d float64) (float64, Point, Point) {
    sort.Slice(strip, func(i, j int) bool {
        return strip[i].Y < strip[j].Y
    })
    
    minDist := d
    var bestA, bestB Point
    
    for i := 0; i < len(strip); i++ {
        for j := i + 1; j < len(strip) && (strip[j].Y-strip[i].Y) < minDist; j++ {
            dist := distance(strip[i], strip[j])
            if dist < minDist {
                minDist = dist
                bestA, bestB = strip[i], strip[j]
            }
        }
    }
    
    return minDist, bestA, bestB
}
```

## Análisis de Complejidad

| Algoritmo | Dividir | Conquer | Combinar | Total |
|-----------|---------|---------|----------|-------|
| **Merge Sort** | O(1) | 2T(n/2) | O(n) | O(n log n) |
| **Quick Sort** | O(n) | 2T(n/1) | O(1) | O(n log n) avg |
| **Binary Search** | O(1) | T(n/2) | O(1) | O(log n) |
| **Closest Pair** | O(n) | 2T(n/2) | O(n) | O(n log n) |

> [!note] Teorema Maestro
> Para recurrencias del tipo T(n) = aT(n/b) + f(n):
> - Si f(n) = O(n^(log_b(a)-ε)): T(n) = Θ(n^(log_b(a)))
> - Si f(n) = Θ(n^(log_b(a))): T(n) = Θ(n^(log_b(a)) * log n)
> - Si f(n) = Ω(n^(log_b(a)+ε)) y a regularity: T(n) = Θ(f(n))

## Divide and Conquer vs Other Techniques

| Técnica | Cuando Usar | Ejemplo |
|---------|-------------|---------|
| **Divide and Conquer** | Subproblemas independientes | Merge Sort, Binary Search |
| **Greedy** | Elección local óptima funciona globalmente | Kruskal, Dijkstra |
| **Dynamic Programming** | Subproblemas se solapan | Fibonacci, Knapsack |

## Ventajas y Desventajas

| Ventajas | Desventajas |
|----------|-------------|
| Parallelizable | Overhead de recursion |
| Algoritmos elegantes y limpios | Stack overflow para problemas grandes |
| Eficiente para ciertos problemas | Puede ser difícil implementar combinación |
| Cache-friendly (ordenamiento) | |

## Ejemplo: Exponenciación Rápida

```go
// Calcular a^b usando divide and conquer
func power(a, b int) int {
    // Caso base
    if b == 0 {
        return 1
    }
    
    // Dividir
    half := power(a, b/2)
    
    // Conquer + Combinar
    if b%2 == 0 {
        return half * half // a^b = (a^(b/2))^2
    } else {
        return half * half * a // a^b = (a^(b/2))^2 * a
    }
}

// Complejidad: O(log b)
```

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Recursion]]
- [[Ordenamiento Avanzado]]
- [[Greedy]]
- [[Programacion Dinamica]]
