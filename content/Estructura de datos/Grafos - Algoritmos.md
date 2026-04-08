---
title: Grafos - Algoritmos
type: zettel
created: 2026-04-03
tags: [estructura-datos,grafos,grafos-algoritmos,dijkstra,kruskal]
related: [Estructuras de Datos y Análisis de Algoritmos, Grafos - Fundamentos, Grafos - Recorridos, Heap]
---

# Grafos - Algoritmos

## Algoritmos de Grafos Principales

| Algoritmo | Problema | Complejidad | Tipo de Grafo |
|-----------|----------|-------------|---------------|
| **Dijkstra** | Camino más corto desde una fuente | O((V+E) log V) | Ponderado, sin pesos negativos |
| **Bellman-Ford** | Camino más corto (incluye negativos) | O(VE) | Ponderado |
| **Kruskal** | Árbol de expansión mínimo | O(E log V) | Ponderado, no dirigido |
| **Prim** | Árbol de expansión mínimo | O(E log V) | Ponderado, no dirigido |
| **Topological Sort** | Orden topológico | O(V + E) | DAG |
| **Floyd-Warshall** | Caminos más cortos (todos los pares) | O(V³) | Ponderado |

## Dijkstra - Camino Más Corto

### Concepto

Encuentra el camino más corto desde un vértice fuente a todos los demás en un grafo con pesos no negativos. Usa una **Priority Queue** para siempre procesar el nodo con menor distancia conocida.

```
Grafo inicial:          Después de Dijkstra desde A:
    A---2--→B               Distancias desde A:
    ↓       ↓               A: 0, B: 2, C: 4, D: 6
    1       3               
    ↓       ↓               
    C---1--→D               A → B: 2
                            A → C: 1  
                            A → C → D: 2
```

### Implementación en Go

```go
package main

import (
    "container/heap"
    "fmt"
)

// Arista ponderada
type Edge struct {
    to     int
    weight int
}

// Dijkstra usando Priority Queue
func Dijkstra(adj map[int][]Edge, start int) map[int]int {
    // Inicializar distancias
    dist := make(map[int]int)
    for v := range adj {
        dist[v] = 1 << 31 // Infinito
    }
    dist[start] = 0
    
    // Priority Queue: (distancia, vértice)
    pq := &PriorityQueue{}
    heap.Init(pq)
    heap.Push(pq, Item{value: start, priority: 0})
    
    for pq.Len() > 0 {
        // Extraer vértice con menor distancia
        item := heap.Pop(pq).(Item)
        current := item.value
        currentDist := item.priority
        
        // Si la distancia actual es mayor que la conocida, skip
        if currentDist > dist[current] {
            continue
        }
        
        // Explorar vecinos
        for _, edge := range adj[current] {
            newDist := currentDist + edge.weight
            
            // Si encontramos un camino más corto
            if newDist < dist[edge.to] {
                dist[edge.to] = newDist
                heap.Push(pq, Item{value: edge.to, priority: newDist})
            }
        }
    }
    
    return dist
}

// Priority Queue para Dijkstra
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
```

### Dijkstra con Reconstrucción de Caminos

```go
// Dijkstra que también devuelve el camino
func DijkstraWithPath(adj map[int][]Edge, start, end int) (int, []int) {
    dist := make(map[int]int)
    parent := make(map[int]int)
    
    for v := range adj {
        dist[v] = 1 << 31
    }
    dist[start] = 0
    parent[start] = -1
    
    pq := &PriorityQueue{}
    heap.Init(pq)
    heap.Push(pq, Item{value: start, priority: 0})
    
    for pq.Len() > 0 {
        item := heap.Pop(pq).(Item)
        current := item.value
        currentDist := item.priority
        
        if currentDist > dist[current] {
            continue
        }
        
        if current == end {
            break // Encontramos el destino
        }
        
        for _, edge := range adj[current] {
            newDist := currentDist + edge.weight
            
            if newDist < dist[edge.to] {
                dist[edge.to] = newDist
                parent[edge.to] = current
                heap.Push(pq, Item{value: edge.to, priority: newDist})
            }
        }
    }
    
    // Reconstruir camino
    if dist[end] == 1<<31 {
        return -1, nil // No hay camino
    }
    
    path := []int{}
    current := end
    for current != -1 {
        path = append([]int{current}, path...)
        current = parent[current]
    }
    
    return dist[end], path
}
```

## Kruskal - Árbol de Expansión Mínimo

### Concepto

Kruskal construye un MST (Minimum Spanning Tree) ordenando todas las aristas por peso y agregando las que noformen un ciclo. Usa **Union-Find (Disjoint Set)** para detectar ciclos.

```
Grafo ponderado:              MST resultante:
    10                               10
   /    \                           /
  A------B                        A------B
  |  6   |     →    6     6      |
  |      |                /      |
  C------D                C------D
    5         5            5
  
Aristas del MST: A-B(10), A-C(6), C-D(5) = 21
```

### Implementación en Go

```go
package main

import (
    "sort"
)

// Arista para Kruskal
type KruskalEdge struct {
    u, v   int
    weight int
}

// Union-Find (Disjoint Set)
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
        uf.parent[x] = uf.Find(uf.parent[x]) // Path compression
    }
    return uf.parent[x]
}

func (uf *UnionFind) Union(x, y int) bool {
    rootX := uf.Find(x)
    rootY := uf.Find(y)
    
    if rootX == rootY {
        return false // Ya están en el mismo set (formaría ciclo)
    }
    
    // Union by rank
    if uf.rank[rootX] < uf.rank[rootY] {
        uf.parent[rootX] = rootY
    } else if uf.rank[rootX] > uf.rank[rootY] {
        uf.parent[rootY] = rootX
    } else {
        uf.parent[rootY] = rootX
        uf.rank[rootX]++
    }
    
    return true
}

// Kruskal
func Kruskal(n int, edges []KruskalEdge) ([]KruskalEdge, int) {
    // Ordenar aristas por peso
    sort.Slice(edges, func(i, j int) bool {
        return edges[i].weight < edges[j].weight
    })
    
    uf := NewUnionFind(n)
    mst := []KruskalEdge{}
    totalWeight := 0
    
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

## Topological Sort

### Concepto

Ordena los vértices de un DAG de modo que para cada arista dirigida u → v, u aparece antes que v en el orden. Útil para dependencias de tareas.

```
DAG:                  Topological Sort:
    A → B → D         
    ↓   ↓        →    [A, C, B, E, D]
    C → E → D         
                        
Orden válido: A, C, B, E, D
```

### Implementación (usando Kahn's Algorithm)

```go
package main

import "container/list"

// Topological Sort usando Kahn's Algorithm
func TopologicalSort(n int, adj map[int][]int) ([]int, bool) {
    // Calcular in-degree de cada vértice
    inDegree := make(map[int]int)
    for i := 0; i < n; i++ {
        inDegree[i] = 0
    }
    
    for _, neighbors := range adj {
        for _, neighbor := range neighbors {
            inDegree[neighbor]++
        }
    }
    
    // Agregar vértices con in-degree 0 a la cola
    queue := list.New()
    for v := 0; v < n; v++ {
        if inDegree[v] == 0 {
            queue.PushBack(v)
        }
    }
    
    result := []int{}
    
    for queue.Len() > 0 {
        v := queue.Remove(queue.Front()).(int)
        result = append(result, v)
        
        // Reducir in-degree de vecinos
        for _, neighbor := range adj[v] {
            inDegree[neighbor]--
            if inDegree[neighbor] == 0 {
                queue.PushBack(neighbor)
            }
        }
    }
    
    // Verificar si hay ciclos
    if len(result) != n {
        return nil, false // Hay ciclo (no es DAG)
    }
    
    return result, true
}
```

### Topological Sort usando DFS

```go
func TopologicalSortDFS(adj map[int][]int) ([]int, bool) {
    visited := make(map[int]int) // 0=no visit, 1=visiting, 2=visited
    result := []int{}
    hasCycle := false
    
    var dfs func(int) bool
    dfs = func(v int) bool {
        visited[v] = 1
        
        for _, neighbor := range adj[v] {
            if visited[neighbor] == 1 {
                return true // Ciclo
            }
            if visited[neighbor] == 0 {
                if dfs(neighbor) {
                    return true
                }
            }
        }
        
        visited[v] = 2
        result = append(result, v)
        return false
    }
    
    for v := range adj {
        if visited[v] == 0 {
            if dfs(v) {
                hasCycle = true
                break
            }
        }
    }
    
    if hasCycle {
        return nil, false
    }
    
    // Invertir para obtener orden topológico
    for i, j := 0, len(result)-1; i < j; i, j = i+1, j-1 {
        result[i], result[j] = result[j], result[i]
    }
    
    return result, true
}
```

## Bellman-Ford

### Concepto

Encuentra caminos más cortos soportando pesos negativos. Detecta ciclos negativos.

```go
func BellmanFord(n int, edges []struct{ u, v, w int }, start int) (map[int]int, bool) {
    dist := make(map[int]int)
    for i := 0; i < n; i++ {
        dist[i] = 1 << 31
    }
    dist[start] = 0
    
    // Relaxar todas las aristas n-1 veces
    for i := 0; i < n-1; i++ {
        for _, edge := range edges {
            if dist[edge.u]+edge.w < dist[edge.v] {
                dist[edge.v] = dist[edge.u] + edge.w
            }
        }
    }
    
    // Verificar ciclos negativos
    for _, edge := range edges {
        if dist[edge.u]+edge.w < dist[edge.v] {
            return nil, true // Ciclo negativo detectado
        }
    }
    
    return dist, false
}
```

## Ejemplo Completo

```go
func main() {
    // === DIJKSTRA ===
    adj := map[int][]Edge{
        0: {{1, 4}, {2, 1}},
        1: {{0, 4}, {2, 2}, {3, 5}},
        2: {{0, 1}, {1, 2}, {3, 3}},
        3: {{1, 5}, {2, 3}},
    }
    
    dist := Dijkstra(adj, 0)
    fmt.Println("Dijkstra desde 0:", dist)
    // map[0:0 1:3 2:1 3:4]
    
    // === KRUSKAL ===
    edges := []KruskalEdge{
        {0, 1, 10},
        {0, 2, 6},
        {0, 3, 5},
        {1, 3, 15},
        {2, 3, 4},
    }
    mst, weight := Kruskal(4, edges)
    fmt.Printf("MST: %v, Peso total: %d\n", mst, weight)
    
    // === TOPOLOGICAL SORT ===
    dag := map[int][]int{
        0: {1, 2},
        1: {3},
        2: {3},
        3: {},
    }
    order, ok := TopologicalSort(4, dag)
    fmt.Println("Topological sort:", order, "Válido:", ok)
}
```

## Análisis Comparativo

| Algoritmo | Tiempo | Espacio | Restricciones |
|-----------|---------|----------|---------------|
| **Dijkstra** | O((V+E) log V) | O(V) | Pesos ≥ 0 |
| **Bellman-Ford** | O(VE) | O(V) | Ninguna |
| **Kruskal** | O(E log V) | O(V) | No dirigido |
| **Prim** | O(E log V) | O(V) | No dirigido |
| **Topological Sort** | O(V + E) | O(V) | DAG |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Grafos - Fundamentos]]
- [[Grafos - Recorridos]]
- [[Heap]]
