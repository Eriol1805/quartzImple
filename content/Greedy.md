---
title: Greedy
type: zettel
created: 2026-04-03
tags: [algoritmos,tecnicas-algoritmicas,greedy,algoritmo-greedy]
related: [Estructuras de Datos y Análisis de Algoritmos, Divide and Conquer, Programacion Dinamica, Grafos - Algoritmos]
---

# Greedy

## Definición

Los **algoritmos Greedy** (avarios/ambiciosos) toman decisiones locales óptimas en cada paso, con la esperanza de que llevando a cabo una secuencia de decisiones locales óptimas se llegue a una solución global óptima.

> [!tip] Analogía
> Elegir siempre el camino que parece mejor en el momento, como tomar el tren con más probabilidad de conexión rápida, sin considerar todo el mapa de rutas.

## Características

| Característica | Descripción |
|----------------|-------------|
| **Selección local** | Elige la mejor opción en el momento |
| **Irrevocable** | Una vez tomada la decisión, no se revierte |
| **Esperanza de optimalidad** | Confía en que las decisiones locales llevan a la solución global |
| **No siempre óptimo** | Puede no encontrar la solución óptima (debe probarse) |

## Estructura General

```go
func greedyAlgorithm(problema) Solucion {
    solucion := nuevaSolucion()
    
    mientras problema.noEstaResuelto() {
        // 1. Seleccionar la mejor opción local
        opcion := seleccionarMejorOpcion(problema)
        
        // 2. Verificar si es viable
        if esViable(opcion, problema) {
            // 3. Agregar a la solución
            solucion.agregar(opcion)
            problema.actualizar(opcion)
        }
    }
    
    return solucion
}
```

## Algoritmos Greedy Clásicos

### 1. Dijkstra (Camino Más Corto)

```go
package main

import (
    "container/heap"
    "fmt"
)

type Edge struct {
    to     int
    weight int
}

type Item struct {
    value    int
    priority int
}

type PriorityQueue []Item

func (pq PriorityQueue) Len() int { return len(pq) }
func (pq PriorityQueue) Less(i, j int) bool { return pq[i].priority < pq[j].priority }
func (pq PriorityQueue) Swap(i, j int) { pq[i], pq[j] = pq[j], pq[i] }
func (pq *PriorityQueue) Push(x interface{}) {
    *pq = append(*pq, x.(Item))
}
func (pq *PriorityQueue) Pop() interface{} {
    old := *pq
    n := len(old)
    item := old[n-1]
    *pq = old[0 : n-1]
    return item
}

// Dijkstra - Greedy: siempre procesar el nodo con menor distancia conocida
func dijkstra(adj map[int][]Edge, start int) map[int]int {
    dist := make(map[int]int)
    visited := make(map[int]bool)
    
    for v := range adj {
        dist[v] = 1 << 31 // Infinito
    }
    dist[start] = 0
    
    pq := &PriorityQueue{}
    heap.Init(pq)
    heap.Push(pq, Item{value: start, priority: 0})
    
    for pq.Len() > 0 {
        // GREEDY: siempre extraer el nodo con menor distancia
        item := heap.Pop(pq).(Item)
        current := item.value
        
        if visited[current] {
            continue
        }
        visited[current] = true
        
        // Explorar vecinos
        for _, edge := range adj[current] {
            newDist := dist[current] + edge.weight
            if newDist < dist[edge.to] {
                dist[edge.to] = newDist
                heap.Push(pq, Item{value: edge.to, priority: newDist})
            }
        }
    }
    
    return dist
}
```

### 2. Kruskal (Árbol de Expansión Mínimo)

```go
package main

import (
    "sort"
)

type KruskalEdge struct {
    u, v   int
    weight int
}

// Union-Find para Kruskal
type UnionFind struct {
    parent []int
    rank   []int
}

func NewUnionFind(n int) *UnionFind {
    parent := make([]int, n)
    rank := make([]int, n)
    for i := 0; i < n; i++ {
        parent[i] = i
    }
    return &UnionFind{parent: parent, rank: rank}
}

func (uf *UnionFind) Find(x int) int {
    if uf.parent[x] != x {
        uf.parent[x] = uf.Find(uf.parent[x])
    }
    return uf.parent[x]
}

func (uf *UnionFind) Union(x, y int) bool {
    rx, ry := uf.Find(x), uf.Find(y)
    if rx == ry {
        return false // Ya conectado
    }
    
    // GREEDY: unir por rango
    if uf.rank[rx] < uf.rank[ry] {
        uf.parent[rx] = ry
    } else if uf.rank[rx] > uf.rank[ry] {
        uf.parent[ry] = rx
    } else {
        uf.parent[ry] = rx
        uf.rank[rx]++
    }
    return true
}

// Kruskal - Greedy: siempre seleccionar la arista de menor peso que no forme ciclo
func kruskal(n int, edges []KruskalEdge) ([]KruskalEdge, int) {
    // GREEDY 1: ordenar aristas por peso
    sort.Slice(edges, func(i, j int) bool {
        return edges[i].weight < edges[j].weight
    })
    
    uf := NewUnionFind(n)
    mst := []KruskalEdge{}
    totalWeight := 0
    
    // GREEDY 2: siempre agregar la arista de menor peso que no forme ciclo
    for _, edge := range edges {
        if uf.Union(edge.u, edge.v) {
            mst = append(mst, edge)
            totalWeight += edge.weight
            
            if len(mst) == n-1 {
                break // MST completo
            }
        }
    }
    
    return mst, totalWeight
}
```

### 3. Activity Selection

Seleccionar el máximo número de actividades que no se superponen.

```go
import (
    "sort"
)

type Activity struct {
    start int
    end   int
}

// Greedy: siempre seleccionar la actividad que termina más pronto
func activitySelection(activities []Activity) []Activity {
    // GREEDY: ordenar por tiempo de fin
    sort.Slice(activities, func(i, j int) bool {
        return activities[i].end < activities[j].end
    })
    
    selected := []Activity{}
    lastEnd := 0
    
    for _, a := range activities {
        // GREEDY: seleccionar si no hay conflicto
        if a.start >= lastEnd {
            selected = append(selected, a)
            lastEnd = a.end
        }
    }
    
    return selected
}
```

### 4. Fractional Knapsack

```go
import "sort"

type Item struct {
    weight int
    value  int
}

// Greedy: siempre seleccionar el item con mejor ratio valor/peso
func fractionalKnapsack(capacity int, items []Item) float64 {
    // GREEDY: ordenar por ratio valor/peso descendente
    sort.Slice(items, func(i, j int) bool {
        r1 := float64(items[i].value) / float64(items[i].weight)
        r2 := float64(items[j].value) / float64(items[j].weight)
        return r1 > r2
    })
    
    totalValue := 0.0
    remaining := capacity
    
    for _, item := range items {
        if remaining <= 0 {
            break
        }
        
        // GREEDY: tomar lo que se pueda
        if item.weight <= remaining {
            totalValue += float64(item.value)
            remaining -= item.weight
        } else {
            ratio := float64(remaining) / float64(item.weight)
            totalValue += float64(item.value) * ratio
            remaining = 0
        }
    }
    
    return totalValue
}
```

### 5. Huffman Coding

Compresión usando códigos prefijos óptimos.

```go
import (
    "container/heap"
    "fmt"
)

type HuffmanNode struct {
    char   rune
    freq   int
    left   *HuffmanNode
    right  *HuffmanNode
}

type HuffmanHeap []*HuffmanNode

func (h HuffmanHeap) Len() int { return len(h) }
func (h HuffmanHeap) Less(i, j int) bool { return h[i].freq < h[j].freq }
func (h HuffmanHeap) Swap(i, j int) { h[i], h[j] = h[j], h[i] }
func (h *HuffmanHeap) Push(x interface{}) {
    *h = append(*h, x.(*HuffmanNode))
}
func (h *HuffmanHeap) Pop() interface{} {
    old := *h
    n := len(old)
    item := old[n-1]
    *h = old[0 : n-1]
    return item
}

// Huffman - Greedy: siempre combinar los dos nodos de menor frecuencia
func huffman(freqs map[rune]int) *HuffmanNode {
    pq := &HuffmanHeap{}
    heap.Init(pq)
    
    for char, freq := range freqs {
        heap.Push(pq, &HuffmanNode{char: char, freq: freq})
    }
    
    for pq.Len() > 1 {
        // GREEDY: extraer dos nodos de menor frecuencia
        left := heap.Pop(pq).(*HuffmanNode)
        right := heap.Pop(pq).(*HuffmanNode)
        
        // Crear nodo interno
        merged := &HuffmanNode{
            freq:  left.freq + right.freq,
            left:  left,
            right: right,
        }
        
        heap.Push(pq, merged)
    }
    
    return heap.Pop(pq).(*HuffmanNode)
}
```

## Análisis de Complejidad

| Algoritmo | Complejidad | Por qué |
|-----------|-------------|---------|
| **Dijkstra** | O((V+E) log V) | Priority Queue |
| **Kruskal** | O(E log V) | Ordenar aristas |
| **Activity Selection** | O(n log n) | Ordenar |
| **Fractional Knapsack** | O(n log n) | Ordenar |
| **Huffman** | O(n log n) | Priority Queue |

## Propiedades de los Algoritmos Greedy

### Propiedad de Elección Greedy

"La elección local óptima puede lead a una solución global óptima."

> [!note] Verificación
> Esta propiedad debe probarse formalmente para cada problema. No todos los problemas la satisfy.

### Subestructura Óptima

"Si una solución óptima contiene una subproblema, entonces la sub-solución dentro de esa solución debe ser óptima para ese subproblema."

## Greedy vs Divide and Conquer vs DP

| Técnica | Approach | Optimalidad | Complejidad |
|---------|-----------|-------------|-------------|
| **Greedy** | Toma la mejor decisión local | No garantiza | Generalmentemenos |
| **Divide and Conquer** | Divide en subproblemas independientes | Sí | Variable |
| **DP** | Almacena soluciones de subproblemas | Sí | Mayor |

## Ejemplo: Cambios de Monedas

```go
// Greedy NO funciona para todas las monedas
func greedyCoinChange(coins []int, amount int) int {
    sort.Slice(coins, func(i, j int) bool { return coins[i] > coins[j] })
    
    count := 0
    remaining := amount
    
    for _, coin := range coins {
        count += remaining / coin
        remaining %= coin
    }
    
    if remaining != 0 {
        return -1 // No se puede dar el cambio exacto
    }
    
    return count
}

// Ejemplo donde Greedy falla: coins = [1, 3, 4], amount = 6
// Greedy: 4 + 1 + 1 = 3 monedas (NO óptimo)
// Óptimo: 3 + 3 = 2 monedas

// DP SÍ funciona para el caso general
func dpCoinChange(coins []int, amount int) int {
    dp := make([]int, amount+1)
    for i := range dp {
        dp[i] = amount + 1 // Infinito
    }
    dp[0] = 0
    
    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i {
                dp[i] = min(dp[i], dp[i-coin]+1)
            }
        }
    }
    
    if dp[amount] > amount {
        return -1
    }
    return dp[amount]
}
```

## Cuándo Usar Greedy

| Condición | Ejemplo |
|-----------|---------|
| ✅ Propiedad de elección greedy probada | Dijkstra, Kruskal, Prim |
| ✅ Problemas de optimización simple | Activity selection, fractional knapsack |
| ❌ Solución global requiere revisar opciones | 0/1 Knapsack |
| ❌ Subproblemas se solapan | Fibonacci (usa DP) |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Divide and Conquer]]
- [[Programacion Dinamica]]
- [[Grafos - Algoritmos]]
