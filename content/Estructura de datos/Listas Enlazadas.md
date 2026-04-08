---
title: Listas Enlazadas
type: zettel
created: 2026-04-02
tags: [estructura-datos, lista-enlazada, linked-list, tad]
related: [Pilas (Stack), Colas (Queue), Tipos Abstractos de Datos (TAD)]
---

# Listas Enlazadas

## Definición

Una **Lista Enlazada** (Linked List) es una estructura de datos lineal compuesta por **nodos**, donde cada nodo contiene:
- El **dato** (información a almacenar)
- Un **puntero** al siguiente nodo

> [!tip] Diferencia con arrays
> A diferencia de los arrays, los nodos pueden estar en posiciones no contiguas de memoria.

## Tipos de Listas Enlazadas

### Lista Simplemente Enlazada

Cada nodo tiene un puntero al siguiente nodo.

```
┌────┬───┐    ┌────┬───┐    ┌────┬───┐    ┌────┬────┐
│ A  │ ──┼──→ │ B  │ ──┼──→ │ C  │ ──┼──→ │ D  │ nil│
└────┴───┘    └────┴───┘    └────┴───┘    └────┴────┘
  head                                           tail
```

### Lista Doblemente Enlazada

Cada nodo tiene punteros al nodo anterior y siguiente.

```
┌────┬───┬───┐    ┌────┬───┬───┐    ┌────┬───┬───┐
│ nil│ A │ ──┼──→ │ ◄──│ B │ ──┼──→ │ ◄──│ C │nil│
└────┴───┴───┘    └────┴───┴───┘    └────┴───┴───┘
  head                                           tail
```

### Lista Circular

El último nodo apunta de vuelta al primero.

```
┌────┬───┐    ┌────┬───┐    ┌────┬───┐
│ A  │ ──┼──→ │ B  │ ──┼──→ │ C  │ ──┼──┐
└────┴───┘    └────┴───┘    └────┴───┘  │
    ↑                                  │
    └──────────────────────────────────┘
```

## Operaciones

| Operación | Descripción | Complejidad |
|-----------|-------------|-------------|
| **InsertAtHead** | Insertar al inicio | O(1) |
| **InsertAtTail** | Insertar al final | O(1)* |
| **Delete** | Eliminar nodo | O(n) |
| **Search** | Buscar elemento | O(n) |
| **GetAt** | Obtener por índice | O(n) |

*Con puntero a tail; sin él, O(n)

## Implementación en Go

### Nodo

```go
type Node struct {
    Value interface{}
    Next  *Node
}
```

### Lista Simply Enlazada

```go
type LinkedList struct {
    head *Node
    tail *Node
    size int
}

func (l *LinkedList) InsertAtHead(value interface{}) {
    newNode := &Node{Value: value, Next: l.head}
    l.head = newNode
    if l.tail == nil {
        l.tail = newNode
    }
    l.size++
}

func (l *LinkedList) InsertAtTail(value interface{}) {
    newNode := &Node{Value: value}
    if l.tail == nil {
        l.head = newNode
        l.tail = newNode
    } else {
        l.tail.Next = newNode
        l.tail = newNode
    }
    l.size++
}

func (l *LinkedList) Delete(value interface{}) bool {
    if l.head == nil {
        return false
    }
    
    if l.head.Value == value {
        l.head = l.head.Next
        if l.head == nil {
            l.tail = nil
        }
        l.size--
        return true
    }
    
    current := l.head
    for current.Next != nil {
        if current.Next.Value == value {
            current.Next = current.Next.Next
            if current.Next == nil {
                l.tail = current
            }
            l.size--
            return true
        }
        current = current.Next
    }
    return false
}

func (l *LinkedList) Search(value interface{}) *Node {
    current := l.head
    for current != nil {
        if current.Value == value {
            return current
        }
        current = current.Next
    }
    return nil
}

func (l *LinkedList) Size() int {
    return l.size
}

func (l *LinkedList) IsEmpty() bool {
    return l.size == 0
}
```

### Lista Doblemente Enlazada

```go
type DNode struct {
    Value interface{}
    Prev  *DNode
    Next  *DNode
}

type DoublyLinkedList struct {
    head *DNode
    tail *DNode
    size int
}

func (l *DoublyLinkedList) InsertAtHead(value interface{}) {
    newNode := &DNode{Value: value, Next: l.head}
    if l.head != nil {
        l.head.Prev = newNode
    }
    l.head = newNode
    if l.tail == nil {
        l.tail = newNode
    }
    l.size++
}

func (l *DoublyLinkedList) InsertAtTail(value interface{}) {
    newNode := &DNode{Value: value, Prev: l.tail}
    if l.tail != nil {
        l.tail.Next = newNode
    }
    l.tail = newNode
    if l.head == nil {
        l.head = newNode
    }
    l.size++
}
```

## Ventajas y Desventajas

| Aspecto | Lista Enlazada | Array |
|---------|----------------|-------|
| **Inserción al inicio** | O(1) | O(n) |
| **Acceso por índice** | O(n) | O(1) |
| **Memoria** | Variable (solo nodos) | Fija |
| **Localidad de caché** | Mala | Buena |

## Aplicaciones

- Implementación de [[Pilas (Stack)]] y [[Colas (Queue)]]
- LRU Cache
- Representación de polinomios
- Grafos (lista de adyacencia)

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Tipos Abstractos de Datos (TAD)]]
- [[Pilas (Stack)]]
