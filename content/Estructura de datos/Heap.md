---
title: Heap
type: zettel
created: 2026-04-03
tags: [estructura-datos,heap,arbol,priority-queue]
related: [Estructuras de Datos y Análisis de Algoritmos, Arboles Binarios, Ordenamiento Avanzado]
---

# Heap

## Definición

Un **Heap** (montículo) es un árbol binario completo que satisface la **propiedad heap**:

- **Max-Heap**: El valor de cada nodo es **mayor o igual** que los valores de sus hijos
- **Min-Heap**: El valor de cada nodo es **menor o igual** que los valores de sus hijos

> [!tip] Analogía
> Imagina una pirámide de personas donde cada persona es más pesada que las dos que la sostienen. El más pesado está arriba.

## Representación Visual

```
Max-Heap:                          Min-Heap:
        50                              10
       /  \                            /  \
     40    35                        20    30
    /  \   /  \                     /  \   /  \
   30  25 20  10                   40  50  60  70

Propiedad Max-Heap: 50≥40, 50≥35, 40≥30, 40≥25...
Propiedad Min-Heap: 10≤20, 10≤30, 20≤40, 20≤50...
```

## Representación como Array

El heap se representa eficientemente en un array (sin punteros):

```
Índice:    0   1   2   3   4   5   6   7
Array:    [50, 40, 35, 30, 25, 20, 10, ...]

Relaciones:
- Padre de i:    (i-1) / 2
- Hijo izq de i: 2*i + 1
- Hijo der de i: 2*i + 2
```

## Implementación en Go

```go
package main

import "fmt"

// Heap genérico con interfaz
type Heap[T any] struct {
    data     []T
    less     func(a, b T) bool  // less(a,b) = true si a < b (para Min-Heap)
    more     func(a, b T) bool  // more(a,b) = true si a > b (para Max-Heap)
}

func NewMinHeap[T any](less func(a, b T) bool) *Heap[T] {
    return &Heap[T]{
        data: make([]T, 0),
        less: less,
        more: func(a, b T) bool { return less(b, a) },
    }
}

func NewMaxHeap[T any](more func(a, b T) bool) *Heap[T] {
    return &Heap[T]{
        data: make([]T, 0),
        less: func(a, b T) bool { return more(b, a) },
        more: more,
    }
}
```

## Operaciones Fundamentales

### Heapify (Heapify-Up / Bubble Up)

Sube un elemento desde las hojas hacia la raíz para mantener la propiedad heap.

```go
// Heapify-Up: Sube el elemento en el índice i
func (h *Heap[T]) heapifyUp(i int) {
    for i > 0 {
        parent := (i - 1) / 2
        // Para Min-Heap: si el hijo es menor que el padre, intercambiar
        // Para Max-Heap: si el hijo es mayor que el padre, intercambiar
        if h.less(h.data[i], h.data[parent]) {
            h.data[i], h.data[parent] = h.data[parent], h.data[i]
            i = parent
        } else {
            break
        }
    }
}

// Heapify-Down: Baja el elemento desde la raíz hacia las hojas
func (h *Heap[T]) heapifyDown(i int) {
    n := len(h.data)
    for {
        smallest := i
        left := 2*i + 1
        right := 2*i + 2
        
        if left < n && h.less(h.data[left], h.data[smallest]) {
            smallest = left
        }
        if right < n && h.less(h.data[right], h.data[smallest]) {
            smallest = right
        }
        
        if smallest != i {
            h.data[i], h.data[smallest] = h.data[smallest], h.data[i]
            i = smallest
        } else {
            break
        }
    }
}
```

### Insert (Enqueue)

```go
func (h *Heap[T]) Insert(val T) {
    h.data = append(h.data, val)
    h.heapifyUp(len(h.data) - 1)
}
```

### Extract (Dequeue) - Extraer raíz

```go
func (h *Heap[T]) Extract() (T, bool) {
    if len(h.data) == 0 {
        var zero T
        return zero, false
    }
    
    // Guardar raíz (máximo o mínimo según el tipo)
    root := h.data[0]
    
    // Mover último elemento a la raíz
    last := len(h.data) - 1
    h.data[0] = h.data[last]
    h.data = h.data[:last]
    
    // Reconstruir heap
    if len(h.data) > 0 {
        h.heapifyDown(0)
    }
    
    return root, true
}

// Peek: ver elemento raíz sin extraer
func (h *Heap[T]) Peek() (T, bool) {
    if len(h.data) == 0 {
        var zero T
        return zero, false
    }
    return h.data[0], true
}
```

## Heap Sort

Ordenamiento basado en heap con complejidad O(n log n).

```go
func heapSort(arr []int) []int {
    n := len(arr)
    
    // 1. Construir Max-Heap
    for i := n/2 - 1; i >= 0; i-- {
        heapifyDownArray(arr, n, i)
    }
    
    // 2. Extraer elementos uno por uno
    for i := n - 1; i > 0; i-- {
        arr[0], arr[i] = arr[i], arr[0]
        heapifyDownArray(arr, i, 0)
    }
    
    return arr
}

// Heapify para array in-place
func heapifyDownArray(arr []int, n, i int) {
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
        heapifyDownArray(arr, n, largest)
    }
}
```

## Priority Queue con Heap

```go
// Priority Queue basada en Min-Heap
type PriorityQueue[T any] struct {
    heap *Heap[T]
}

func NewPriorityQueue[T any](less func(a, b T) bool) *PriorityQueue[T] {
    return &PriorityQueue[T]{
        heap: NewMinHeap(less),
    }
}

func (pq *PriorityQueue[T]) Enqueue(val T) {
    pq.heap.Insert(val)
}

func (pq *PriorityQueue[T]) Dequeue() (T, bool) {
    return pq.heap.Extract()
}

func (pq *PriorityQueue[T]) Peek() (T, bool) {
    return pq.heap.Peek()
}

func (pq *PriorityQueue[T]) Len() int {
    return len(pq.heap.data)
}

func (pq *PriorityQueue[T]) IsEmpty() bool {
    return len(pq.heap.data) == 0
}
```

## Ejemplo Completo

```go
func main() {
    // Ejemplo con números
    pq := NewPriorityQueue[int](func(a, b int) bool {
        return a < b  // Menor prioridad tiene mayor precedencia
    })
    
    pq.Enqueue(5)
    pq.Enqueue(2)
    pq.Enqueue(8)
    pq.Enqueue(1)
    
    fmt.Println("Priority Queue (Min-Heap):")
    for !pq.IsEmpty() {
        val, _ := pq.Dequeue()
        fmt.Printf("%d ", val)
    }
    // Output: 1 2 5 8
    
    // Ejemplo con Max-Heap
    maxHeap := NewMaxHeap[int](func(a, b int) bool {
        return a > b
    })
    
    values := []int{3, 1, 4, 1, 5, 9, 2, 6}
    for _, v := range values {
        maxHeap.Insert(v)
    }
    
    fmt.Println("\nMax-Heap extract:")
    for i := 0; i < len(values); i++ {
        val, _ := maxHeap.Extract()
        fmt.Printf("%d ", val)
    }
    // Output: 9 6 5 4 3 2 1 1
}
```

## Análisis de Complejidad

| Operación | Tiempo | Espacio |
|------------|--------|---------|
| **Insert** | O(log n) | O(1) |
| **Extract** | O(log n) | O(1) |
| **Peek** | O(1) | O(1) |
| **Build Heap** | O(n) | O(1) |
| **Heap Sort** | O(n log n) | O(1) |

## Comparación Min-Heap vs Max-Heap

| Operación | Min-Heap | Max-Heap |
|------------|----------|----------|
| **Raíz** | Mínimo | Máximo |
| **Extract** | Extrae mínimo | Extrae máximo |
| **Use case** | Priority Queue | Priority Queue, scheduling |
| **Construcción** | O(n) | O(n) |

## Aplicaciones

| Aplicación | Uso del Heap |
|------------|--------------|
| **Priority Queue** | Cola con prioridades |
| **Heap Sort** | Ordenamiento O(n log n) |
| **Top K elementos** | Encontrar K mayores/menores |
| **Mediana en flujo** | Mantener mediana online |
| **Scheduling** | OS job scheduling |
| **Dijkstra** | Algoritmo de ruta más corta |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Arboles Binarios]]
- [[Ordenamiento Avanzado]]
- [[Grafos - Algoritmos]] (para Dijkstra)
