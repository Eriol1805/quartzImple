---
title: Grafos - Fundamentos
type: zettel
created: 2026-04-03
tags: [estructura-datos,grafos,grafos-fundamentos]
related: [Estructuras de Datos y Análisis de Algoritmos, Grafos - Recorridos, Grafos - Algoritmos]
---

# Grafos - Fundamentos

## Definición

Un **grafo** es una estructura de datos compuesta por:
- **Vértices (V)**: Nodos o puntos
- **Aristas (E)**: Conexiones entre vértices

Se denota como G = (V, E).

> [!tip] Analogía
> Una red social: personas = vértices, amistades = aristas. Facebook es no dirigido, Twitter es dirigido.

## Componentes del Grafo

```
Grafo No Dirigido:        Grafo Dirigido (Dígrafo):
    A --- B                  A → B
   /|     |\                /|     |\
  C |     | D              C |     ↓ D
   \|     |/                \|     /
    E --- F                  E ← F

Vértices: {A, B, C, D, E, F}
Aristas (no dirigido): {A-B, A-C, A-E, B-D, B-F, C-E, D-F, E-F}
Aristas (dirigido): {A→B, A→C, A→E, B→D, B→F, C→E, D→F, F→E}
```

## Tipos de Grafos

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **No Dirigido** | Las aristas no tienen dirección | Redes sociales |
| **Dirigido (Dígrafo)** | Las aristas tienen dirección | Twitter, páginas web |
| **Ponderado** | Las aristas tienen peso/costo | Mapas, rutas |
| **No Ponderado** | Todas las aristas pesan igual | Conexiones de red |
| **Cíclico** | Contiene ciclos |-any graph with loops |
| **Acíclico (DAG)** | Sin ciclos | Dependencias de tareas |
| **Completo** | Todos los vértices conectados | Redes mesh |
| **Bipartito** | Vértices en dos conjuntos | Emparejamiento |

## Representaciones

### 1. Matriz de Adyacencia

```go
// Matriz de adyacencia para grafo con n vértices
type AdjMatrix struct {
    matrix [][]int  // 0 = no hay arista, 1 = hay arista (o peso)
    n      int
    directed bool
}

func NewAdjMatrix(n int, directed bool) *AdjMatrix {
    matrix := make([][]int, n)
    for i := range matrix {
        matrix[i] = make([]int, n)
    }
    return &AdjMatrix{matrix: matrix, n: n, directed: directed}
}

func (am *AdjMatrix) AddEdge(u, v int, weight int) {
    am.matrix[u][v] = weight
    if !am.directed {
        am.matrix[v][u] = weight
    }
}

func (am *AdjMatrix) HasEdge(u, v int) bool {
    return am.matrix[u][v] != 0
}

func (am *AdjMatrix) GetWeight(u, v int) int {
    return am.matrix[u][v]
}
```

**Complejidad:**
| Operación | Tiempo |
|-----------|--------|
| AddEdge | O(1) |
| HasEdge | O(1) |
| ListNeighbors | O(n) |
| Espacio | O(n²) |

### 2. Lista de Adyacencia

```go
package main

import "fmt"

// Lista de adyacencia
type AdjList struct {
    edges   map[int][]int  // vértice → lista de vecinos
    weights map[string]int // "u-v" → peso
    directed bool
}

func NewAdjList(directed bool) *AdjList {
    return &AdjList{
        edges:   make(map[int][]int),
        weights: make(map[string]int),
        directed: directed,
    }
}

func (al *AdjList) AddEdge(u, v int, weight int) {
    // Agregar v a la lista de u
    al.edges[u] = append(al.edges[u], v)
    
    // Si es no dirigido, agregar u a la lista de v
    if !al.directed {
        al.edges[v] = append(al.edges[v], u)
    }
    
    // Guardar peso
    al.weights[fmt.Sprintf("%d-%d", u, v)] = weight
    if !al.directed {
        al.weights[fmt.Sprintf("%d-%d", v, u)] = weight
    }
}

func (al *AdjList) GetNeighbors(v int) []int {
    return al.edges[v]
}

func (al *AdjList) HasEdge(u, v int) bool {
    neighbors := al.edges[u]
    for _, n := range neighbors {
        if n == v {
            return true
        }
    }
    return false
}

func (al *AdjList) GetWeight(u, v int) int {
    return al.weights[fmt.Sprintf("%d-%d", u, v)]
}
```

**Complejidad:**
| Operación | Tiempo |
|-----------|--------|
| AddEdge | O(1) |
| HasEdge | O(deg(v)) |
| ListNeighbors | O(deg(v)) |
| Espacio | O(V + E) |

## Comparación de Representaciones

| Criterio | Matriz de Adyacencia | Lista de Adyacencia |
|----------|---------------------|---------------------|
| **Espacio** | O(V²) | O(V + E) |
| **AddEdge** | O(1) | O(1) |
| **HasEdge** | O(1) | O(deg(v)) |
| **Iterar vecinos** | O(V) | O(deg(v)) |
| **Grafos densos** | Mejor | Peor |
| **Grafos dispersos** | Peor | Mejor |

## Grafo Ponderado

```go
// Arista con peso
type WeightedEdge struct {
    From int
    To   int
    Weight int
}

// Grafo ponderado con lista de adyacencia
type WeightedGraph struct {
    vertices int
    adj      map[int][]WeightedEdge
    directed bool
}

func NewWeightedGraph(v int, directed bool) *WeightedGraph {
    adj := make(map[int][]WeightedEdge)
    for i := 0; i < v; i++ {
        adj[i] = []WeightedEdge{}
    }
    return &WeightedGraph{vertices: v, adj: adj, directed: directed}
}

func (wg *WeightedGraph) AddEdge(u, v, weight int) {
    wg.adj[u] = append(wg.adj[u], WeightedEdge{From: u, To: v, Weight: weight})
    if !wg.directed {
        wg.adj[v] = append(wg.adj[v], WeightedEdge{From: v, To: u, Weight: weight})
    }
}

func (wg *WeightedGraph) GetEdges(u int) []WeightedEdge {
    return wg.adj[u]
}
```

## Tipos Especiales de Grafos

### DAG (Directed Acyclic Graph)

```go
func isDAG(adj map[int][]int) bool {
    visited := make(map[int]int) // 0=unvisited, 1=visiting, 2=visited
    hasCycle := false
    
    var dfs func(int)
    dfs = func(v int) {
        visited[v] = 1
        for _, neighbor := range adj[v] {
            if visited[neighbor] == 1 {
                hasCycle = true
                return
            } else if visited[neighbor] == 0 {
                dfs(neighbor)
            }
        }
        visited[v] = 2
    }
    
    for v := range adj {
        if visited[v] == 0 {
            dfs(v)
        }
    }
    return !hasCycle
}
```

### Grafo Bipartito

```go
func isBipartite(adj map[int][]int) bool {
    color := make(map[int]int) // 0=uncolored, 1=red, -1=blue
    
    for start := range adj {
        if color[start] != 0 {
            continue
        }
        
        queue := []int{start}
        color[start] = 1
        
        for len(queue) > 0 {
            v := queue[0]
            queue = queue[1:]
            
            for _, neighbor := range adj[v] {
                if color[neighbor] == 0 {
                    color[neighbor] = -color[v]
                    queue = append(queue, neighbor)
                } else if color[neighbor] == color[v] {
                    return false
                }
            }
        }
    }
    return true
}
```

## Ejemplo de Uso

```go
func main() {
    // Crear grafo no dirigido
    g := NewAdjList(false)
    
    // Agregar aristas (conexiones de red social)
    g.AddEdge(0, 1, 0)
    g.AddEdge(0, 2, 0)
    g.AddEdge(1, 2, 0)
    g.AddEdge(2, 3, 0)
    g.AddEdge(3, 4, 0)
    
    // Ver vecinos de cada vértice
    fmt.Println("Vecinos de 0:", g.GetNeighbors(0)) // [1 2]
    fmt.Println("Vecinos de 2:", g.GetNeighbors(2)) // [0 1 3]
    
    // Verificar conexión
    fmt.Println("0 → 3 conectado:", g.HasEdge(0, 3)) // false
    fmt.Println("2 → 3 conectado:", g.HasEdge(2, 3)) // true
}
```

## Aplicaciones de Grafos

| Aplicación | Tipo de Grafo | Uso |
|------------|---------------|-----|
| **Redes sociales** | No dirigido | Conexiones entre usuarios |
| **Mapas/GPS** | Ponderado dirigido | Rutas entre lugares |
| **Páginas web** | Dirigido | Links entre páginas |
| **Dependencias** | DAG | Orden de ejecución |
| **Redes de transporte** | Ponderado | Rutas óptimas |
| **Redes de computadoras** | No dirigido | Topología de red |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Grafos - Recorridos]]
- [[Grafos - Algoritmos]]
