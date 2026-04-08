---
title: Grafos - Recorridos
type: zettel
created: 2026-04-03
tags: [estructura-datos,grafos,grafos-recorridos,bfs,dfs]
related: [Estructuras de Datos y Análisis de Algoritmos, Grafos - Fundamentos, Grafos - Algoritmos]
---

# Grafos - Recorridos

## Definición

Los **recorridos de grafos** (traversals) son algoritmos que permiten visitar todos los vértices de un grafo de manera sistemática. Los dos métodos fundamentales son:

- **BFS** (Breadth-First Search): Recorrido por niveles
- **DFS** (Depth-First Search): Recorrido en profundidad

## BFS (Breadth-First Search)

### Concepto

BFS explora el grafo nivel por nivel usando una **cola** (queue). Es analogous a propagate una onda desde el vértice inicial.

```
Nivel 0:    A          (inicio)
Nivel 1:  B   C
Nivel 2: D   E   F

Orden de visita: A → B → C → D → E → F
```

### Implementación en Go

```go
package main

import (
    "container/list"
    "fmt"
)

// BFS genérico
func BFS(adj map[int][]int, start int) []int {
    visited := make(map[int]bool)
    queue := list.New()
    result := []int{}
    
    // Iniciar desde el vértice start
    visited[start] = true
    queue.PushBack(start)
    
    for queue.Len() > 0 {
        // Obtener siguiente vértice de la cola
        vertex := queue.Remove(queue.Front()).(int)
        result = append(result, vertex)
        
        // Visitar todos los vecinos no visitados
        for _, neighbor := range adj[vertex] {
            if !visited[neighbor] {
                visited[neighbor] = true
                queue.PushBack(neighbor)
            }
        }
    }
    
    return result
}

// BFS con distancia desde el origen
func BFSWithDistance(adj map[int][]int, start int) (map[int]int, map[int]int) {
    visited := make(map[int]bool)
    distance := make(map[int]int)
    parent := make(map[int]int)
    
    queue := list.New()
    
    visited[start] = true
    distance[start] = 0
    parent[start] = -1
    queue.PushBack(start)
    
    for queue.Len() > 0 {
        vertex := queue.Remove(queue.Front()).(int)
        
        for _, neighbor := range adj[vertex] {
            if !visited[neighbor] {
                visited[neighbor] = true
                distance[neighbor] = distance[vertex] + 1
                parent[neighbor] = vertex
                queue.PushBack(neighbor)
            }
        }
    }
    
    return distance, parent
}

// Reconstruir camino desde el origen hasta destino
func ReconstructPath(parent map[int]int, start, end int) []int {
    path := []int{}
    current := end
    
    for current != -1 {
        path = append([]int{current}, path...)
        if current == start {
            break
        }
        current = parent[current]
    }
    
    return path
}
```

### BFS para Grafo Ponderado

```go
// BFS para grafo con pesos en aristas
type WeightedEdge struct {
    To     int
    Weight int
}

func BFSWeighted(adj map[int][]WeightedEdge, start int) map[int]int {
    visited := make(map[int]bool)
    distance := make(map[int]int)
    queue := list.New()
    
    visited[start] = true
    distance[start] = 0
    queue.PushBack(start)
    
    for queue.Len() > 0 {
        vertex := queue.Remove(queue.Front()).(int)
        
        for _, edge := range adj[vertex] {
            if !visited[edge.To] {
                visited[edge.To] = true
                distance[edge.To] = distance[vertex] + edge.Weight
                queue.PushBack(edge.To)
            }
        }
    }
    
    return distance
}
```

## DFS (Depth-First Search)

### Concepto

DFS explora el grafo en profundidad primero, usando una **pila** (stack) o recursión. Es analogous a explorar un laberinto siguiendo siempre el camino más largo.

```
Exploración: A → B → D → E → C → F

Donde:
- Desde A vamos a B
- Desde B vamos a D
- Desde D no podemos ir más → backtrack a B
- Desde B vamos a E
- Desde E no podemos ir más → backtrack a B → A
- Desde A vamos a C
- Etc.
```

### Implementación en Go

```go
package main

import "fmt"

// DFS recursivo
func DFSRecursive(adj map[int][]int, start int) []int {
    visited := make(map[int]bool)
    result := []int{}
    
    var dfsHelper func(int)
    dfsHelper = func(v int) {
        visited[v] = true
        result = append(result, v)
        
        // Explorar vecinos en orden
        for _, neighbor := range adj[v] {
            if !visited[neighbor] {
                dfsHelper(neighbor)
            }
        }
    }
    
    dfsHelper(start)
    return result
}

// DFS iterativo (usa pila explícita)
func DFSIterative(adj map[int][]int, start int) []int {
    visited := make(map[int]bool)
    stack := []int{start}
    result := []int{}
    
    for len(stack) > 0 {
        // Pop de la pila
        v := stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        
        if visited[v] {
            continue
        }
        
        visited[v] = true
        result = append(result, v)
        
        // Agregar vecinos a la pila (en orden inverso para mantener orden)
        neighbors := adj[v]
        for i := len(neighbors) - 1; i >= 0; i-- {
            if !visited[neighbors[i]] {
                stack = append(stack, neighbors[i])
            }
        }
    }
    
    return result
}

// DFS para encontrar todos los componentes conectados
func FindAllComponents(adj map[int][]int) [][]int {
    visited := make(map[int]bool)
    components := [][]int{}
    
    var dfs func(int, *[]int)
    dfs = func(v int, component *[]int) {
        visited[v] = true
        *component = append(*component, v)
        
        for _, neighbor := range adj[v] {
            if !visited[neighbor] {
                dfs(neighbor, component)
            }
        }
    }
    
    for v := range adj {
        if !visited[v] {
            component := []int{}
            dfs(v, &component)
            components = append(components, component)
        }
    }
    
    return components
}
```

## Aplicaciones de Recorridos

### BFS - Usos Típicos

| Aplicación | Por qué BFS |
|------------|-------------|
| **Camino más corto (sin peso)** | Encuentra camino con menos aristas |
| **Niveles de red social** | Distancia desde un usuario |
| **Ciclo más corto en grafo no ponderado** | Primer ciclo encontrado es el más corto |
| **Componente conectado** | Explora todo el componente |

### DFS - Usos Típicos

| Aplicación | Por qué DFS |
|------------|-------------|
| **Detectar ciclos** | Backtracking natural |
| **Topological Sort** | Orden lineal de DAG |
| **Componentes fuertemente conectados** | Algoritmo de Kosaraju |
| **Resolución de laberintos** | Explora profundamente |
| **Backtracking** | Generar combinaciones/permutaciones |

## Detección de Ciclos

```go
// Detectar ciclos usando DFS
func HasCycle(adj map[int][]int, directed bool) bool {
    visited := make(map[int]int) // 0=no visit, 1=visiting, 2=visited
    hasCycle := false
    
    var dfs func(int) bool
    dfs = func(v int) bool {
        visited[v] = 1
        
        for _, neighbor := range adj[v] {
            if visited[neighbor] == 1 {
                return true // Ciclo encontrado
            }
            if visited[neighbor] == 0 {
                if dfs(neighbor) {
                    return true
                }
            }
        }
        
        visited[v] = 2
        return false
    }
    
    for v := range adj {
        if visited[v] == 0 {
            if dfs(v) {
                return true
            }
        }
    }
    return false
}

// Detectar ciclos en grafo no dirigido
func HasCycleUndirected(adj map[int][]int) bool {
    visited := make(map[int]bool)
    
    var dfs func(int, int) bool
    dfs = func(v, parent int) bool {
        visited[v] = true
        
        for _, neighbor := range adj[v] {
            if !visited[neighbor] {
                if dfs(neighbor, v) {
                    return true
                }
            } else if neighbor != parent {
                return true // Ciclo encontrado
            }
        }
        return false
    }
    
    for v := range adj {
        if !visited[v] {
            if dfs(v, -1) {
                return true
            }
        }
    }
    return false
}
```

## Topological Sort (usando DFS)

```go
func TopologicalSortDFS(adj map[int][]int) ([]int, bool) {
    visited := make(map[int]int)
    result := []int{}
    hasCycle := false
    
    var dfs func(int)
    dfs = func(v int) {
        visited[v] = 1
        
        for _, neighbor := range adj[v] {
            if visited[neighbor] == 1 {
                hasCycle = true
                return
            }
            if visited[neighbor] == 0 {
                dfs(neighbor)
            }
        }
        
        visited[v] = 2
        result = append(result, v) // Agregar después de visitar todos los vecinos
    }
    
    for v := range adj {
        if visited[v] == 0 {
            dfs(v)
        }
    }
    
    // Invertir resultado para obtener orden topológico
    for i, j := 0, len(result)-1; i < j; i, j = i+1, j-1 {
        result[i], result[j] = result[j], result[i]
    }
    
    return result, !hasCycle
}
```

## Ejemplo Completo

```go
func main() {
    // Grafo no dirigido
    adj := map[int][]int{
        0: {1, 2},
        1: {0, 2, 3},
        2: {0, 1, 4},
        3: {1, 4},
        4: {2, 3},
    }
    
    fmt.Println("BFS desde 0:", BFS(adj, 0))
    // Output: [0 1 2 3 4]
    
    fmt.Println("DFS recursivo desde 0:", DFSRecursive(adj, 0))
    // Output: [0 1 2 4 3] (puede variar)
    
    // BFS con distancia
    dist, parent := BFSWithDistance(adj, 0)
    fmt.Println("Distancias desde 0:", dist)
    // map[0:0 1:1 2:1 3:2 4:2]
    
    // Reconstruir camino
    path := ReconstructPath(parent, 0, 4)
    fmt.Println("Camino 0 → 4:", path)
    // [0 2 4]
    
    // Encontrar componentes
    components := FindAllComponents(adj)
    fmt.Println("Componentes:", components)
}
```

## Análisis de Complejidad

| Operación | Tiempo | Espacio |
|------------|--------|---------|
| **BFS** | O(V + E) | O(V) |
| **DFS** | O(V + E) | O(V) |
| **Detectar ciclo** | O(V + E) | O(V) |
| **Topological sort** | O(V + E) | O(V) |

> [!note] Notación
> V = número de vértices, E = número de aristas

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Grafos - Fundamentos]]
- [[Grafos - Algoritmos]]
