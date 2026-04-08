---
title: Colas (Queue)
type: zettel
created: 2026-04-02
tags: [estructura-datos, cola, queue, tad, fifo]
related: [Pilas (Stack), Listas Enlazadas, Tipos Abstractos de Datos (TAD)]
---

# Colas (Queue)

## Definición

Una **Cola** (Queue) es una estructura de datos lineal que sigue el principio **FIFO** (First In, First Out): el primer elemento en entrar es el primero en salir.

> [!tip] Analogía
> Imagina una cola del banco: la primera persona en llegar es la primera en ser atendida.

## Operaciones Principales

| Operación | Descripción | Complejidad |
|-----------|-------------|-------------|
| **Enqueue** (Encolar) | Insertar elemento al final | O(1) |
| **Dequeue** (Desencolar) | Eliminar elemento del frente | O(1) |
| **Front** | Ver elemento del frente sin eliminar | O(1) |
| **Rear** | Ver elemento del final sin eliminar | O(1) |
| **IsEmpty** | Verificar si la cola está vacía | O(1) |
| **Size** | Obtener número de elementos | O(1) |

## Representación Visual

```
Enqueue(A) → [A] ← front/rear
Enqueue(B) → [A, B]
              ↑front
Enqueue(C) → [A, B, C]
                    ↑rear

Dequeue() → retorna A  → [B, C]
Dequeue() → retorna B  → [C]
```

## Tipos de Colas

### Cola Simple
Estructura básica con un frente y un final.

### Cola Circular
Optimización donde el final se conecta al frente, aprovechando mejor la memoria.

```
┌─────────────────────────────┐
│  [A] [B] [C] [D] [ ] [ ]   │
│    ↑                  ↑    │
│  front               rear  │
└─────────────────────────────┘
```

### Cola de Prioridad
Elementos con prioridades diferentes; los de mayor prioridad se procesan primero.

## Aplicaciones Reales

| Aplicación | Descripción |
|------------|-------------|
| **Gestión de turnos** | Atención a clientes |
| **Colas de impresión** | Trabajos pendientes |
| **Procesamiento de tareas** | Job queues en servidores |
| **Breadth-First Search** | Recorrido en árboles/grafos |
| **Buffer de datos** | Streaming de video/audio |

## Implementación en Go

### Cola Simple con Slice

```go
type Queue struct {
    items []interface{}
}

func NewQueue() *Queue {
    return &Queue{items: []interface{}{}}
}

func (q *Queue) Enqueue(item interface{}) {
    q.items = append(q.items, item)
}

func (q *Queue) Dequeue() interface{} {
    if len(q.items) == 0 {
        return nil
    }
    item := q.items[0]
    q.items = q.items[1:]
    return item
}

func (q *Queue) Front() interface{} {
    if len(q.items) == 0 {
        return nil
    }
    return q.items[0]
}

func (q *Queue) IsEmpty() bool {
    return len(q.items) == 0
}

func (q *Queue) Size() int {
    return len(q.items)
}
```

### Cola Circular

```go
type CircularQueue struct {
    items  []interface{}
    front  int
    rear   int
    size   int
    cap    int
}

func NewCircularQueue(cap int) *CircularQueue {
    return &CircularQueue{
        items: make([]interface{}, cap),
        cap:   cap,
    }
}

func (q *CircularQueue) Enqueue(item interface{}) error {
    if q.size == q.cap {
        return errors.New("queue is full")
    }
    q.rear = (q.rear + 1) % q.cap
    q.items[q.rear] = item
    q.size++
    return nil
}

func (q *CircularQueue) Dequeue() interface{} {
    if q.size == 0 {
        return nil
    }
    q.front = (q.front + 1) % q.cap
    item := q.items[q.front]
    q.size--
    return item
}
```

## Ejemplo: BFS en Go

```go
func bfs(graph map[string][]string, start string) []string {
    visited := make(map[string]bool)
    queue := NewQueue()
    result := []string{}
    
    queue.Enqueue(start)
    visited[start] = true
    
    for !queue.IsEmpty() {
        node := queue.Dequeue().(string)
        result = append(result, node)
        
        for _, neighbor := range graph[node] {
            if !visited[neighbor] {
                visited[neighbor] = true
                queue.Enqueue(neighbor)
            }
        }
    }
    return result
}
```

## Complejidad

| Operación | Array | Lista Enlazada |
|-----------|-------|----------------|
| Enqueue | O(1)* | O(1) |
| Dequeue | O(n) | O(1) |
| Front | O(1) | O(1) |
| Search | O(n) | O(n) |

*O(n) si hay redimensionamiento

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Tipos Abstractos de Datos (TAD)]]
- [[Pilas (Stack)]]
