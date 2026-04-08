---
title: Implementación en Go - Estructuras de Datos
type: literature
created: 2026-04-02
tags: [go, golang, implementacion, estructura-datos, programacion]
related: [Pilas (Stack), Colas (Queue), Listas Enlazadas, Deques]
---

# Implementación en Go - Estructuras de Datos

## Características de Go

Go es un lenguaje:
- **Compilado**: Convierte código a binario nativo
- **Tipado estático**: Los tipos se verifican en compilación
- **Concurrente**: Goroutines y canales integrados
- **Con garbage collector**: Gestión automática de memoria

### Paquetes y Módulos

```go
package main

import (
    "fmt"
    "errors"
)
```

> [!note] Organización
> El código se organiza en **paquetes**. El paquete especial `main` indica un ejecutable.

---

## Estructuras de Datos en Go

### Arreglos (Arrays)

```go
// Arreglo de tamaño fijo
var arr [5]int = [5]int{1, 2, 3, 4, 5}

// Inicialización corta
arr := [5]int{1, 2, 3, 4, 5}
```

| Característica | Descripción |
|----------------|-------------|
| Tamaño | Fijo en compilación |
| Memoria | Contigua |
| Acceso | O(1) por índice |

### Slices

```go
// Slice vacío
slice := []int{}

// Crear con make
slice := make([]int, 0, 10)  // len=0, cap=10

// Añadir elementos
slice = append(slice, 1, 2, 3)
```

| Característica | Descripción |
|----------------|-------------|
| Tamaño | Dinámico |
| Estructura interna | Puntero a array, len, cap |
| Crecimiento | Duplica capacidad |

```go
// Estructura interna de un slice
type sliceHeader struct {
    pointer uintptr  // Puntero al array
    len     int      // Longitud actual
    cap     int      // Capacidad total
}
```

### Maps

```go
// Crear mapa
m := make(map[string]int)

// Asignar valores
m["clave"] = 42

// Obtener valor
valor := m["clave"]

// Verificar existencia
valor, ok := m["clave"]
if !ok {
    // Clave no existe
}
```

| Característica | Descripción |
|----------------|-------------|
| Implementación | Tabla hash |
| Acceso promedio | O(1) |
| Orden | No determinista |

### Structs

```go
type Persona struct {
    Nombre string
    Edad   int
    Email  string
}

// Crear instancia
p := Persona{
    Nombre: "Juan",
    Edad:   30,
}

// Punteros a structs
p := &Persona{Nombre: "Juan"}
fmt.Println(p.Nombre)  // Acceso directo
```

---

## Punteros en Go

### Conceptos Fundamentales

| Operación | Descripción |
|-----------|-------------|
| `&x` | Obtiene la dirección de memoria de x |
| `*p` | Desreferencia el puntero p |
| `*int` | Tipo puntero a int |

### Ejemplo

```go
func main() {
    x := 10
    p := &x          // p apunta a x
    
    fmt.Println(*p)  // Imprime 10
    *p = 20          // Modifica x a través del puntero
    fmt.Println(x)   // Imprime 20
}
```

---

## Gestión de Memoria

### Stack vs Heap

```
┌─────────────────────────────────────────┐
│                  MEMORIA                │
├──────────────────────┬──────────────────┤
│        STACK         │       HEAP       │
│                      │                   │
│  - Variables locales │  - Objetos globales│
│  - Parámetros        │  - Variables que  │
│  - Valores de retorno│    escapan del    │
│  - Llamadas a func.  │    alcance        │
│                      │  - new(), make()  │
│  Acceso rápido (LIFO)│                   │
│  Gestión automática  │  GC (Garbage      │
│                      │  Collector)       │
└──────────────────────┴──────────────────┘
```

### Garbage Collector

Go usa un **GC automático** que:
- Detecta objetos sin referencias
- Libera memoria automáticamente
- Runs en background

> [!tip] Optimización
> Para evitar allocations excesivas, usar `make` con capacidad inicial cuando el tamaño sea conocido.

---

## Implementación Completa de Estructuras

### Pila (Stack)

```go
package main

import (
    "errors"
    "fmt"
)

type Stack struct {
    items []interface{}
}

func NewStack() *Stack {
    return &Stack{items: []interface{}{}}
}

func (s *Stack) Push(item interface{}) {
    s.items = append(s.items, item)
}

func (s *Stack) Pop() (interface{}, error) {
    if len(s.items) == 0 {
        return nil, errors.New("stack is empty")
    }
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item, nil
}

func (s *Stack) Top() (interface{}, error) {
    if len(s.items) == 0 {
        return nil, errors.New("stack is empty")
    }
    return s.items[len(s.items)-1], nil
}

func (s *Stack) IsEmpty() bool {
    return len(s.items) == 0
}

func (s *Stack) Size() int {
    return len(s.items)
}

func main() {
    stack := NewStack()
    
    stack.Push(1)
    stack.Push(2)
    stack.Push(3)
    
    fmt.Println("Size:", stack.Size())  // 3
    
    top, _ := stack.Top()
    fmt.Println("Top:", top)  // 3
    
    item, _ := stack.Pop()
    fmt.Println("Popped:", item)  // 3
    
    item, _ = stack.Pop()
    fmt.Println("Popped:", item)  // 2
}
```

### Cola (Queue)

```go
package main

import (
    "errors"
    "fmt"
)

type Queue struct {
    items []interface{}
}

func NewQueue() *Queue {
    return &Queue{items: []interface{}{}}
}

func (q *Queue) Enqueue(item interface{}) {
    q.items = append(q.items, item)
}

func (q *Queue) Dequeue() (interface{}, error) {
    if len(q.items) == 0 {
        return nil, errors.New("queue is empty")
    }
    item := q.items[0]
    q.items = q.items[1:]
    return item, nil
}

func (q *Queue) Front() (interface{}, error) {
    if len(q.items) == 0 {
        return nil, errors.New("queue is empty")
    }
    return q.items[0], nil
}

func (q *Queue) IsEmpty() bool {
    return len(q.items) == 0
}

func (q *Queue) Size() int {
    return len(q.items)
}

func main() {
    queue := NewQueue()
    
    queue.Enqueue("A")
    queue.Enqueue("B")
    queue.Enqueue("C")
    
    fmt.Println("Size:", queue.Size())  // 3
    
    front, _ := queue.Front()
    fmt.Println("Front:", front)  // A
    
    item, _ := queue.Dequeue()
    fmt.Println("Dequeued:", item)  // A
    
    item, _ = queue.Dequeue()
    fmt.Println("Dequeued:", item)  // B
}
```

### Lista Enlazada

```go
package main

import (
    "errors"
    "fmt"
)

type Node struct {
    Value interface{}
    Next  *Node
}

type LinkedList struct {
    head *Node
    tail *Node
    size int
}

func NewLinkedList() *LinkedList {
    return &LinkedList{}
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

func (l *LinkedList) GetAt(index int) (interface{}, error) {
    if index < 0 || index >= l.size {
        return nil, errors.New("index out of bounds")
    }
    
    current := l.head
    for i := 0; i < index; i++ {
        current = current.Next
    }
    return current.Value, nil
}

func (l *LinkedList) Size() int {
    return l.size
}

func (l *LinkedList) IsEmpty() bool {
    return l.size == 0
}

func (l *LinkedList) Print() {
    current := l.head
    fmt.Print("List: ")
    for current != nil {
        fmt.Printf("%v -> ", current.Value)
        current = current.Next
    }
    fmt.Println("nil")
}

func main() {
    list := NewLinkedList()
    
    list.InsertAtTail(1)
    list.InsertAtTail(2)
    list.InsertAtHead(0)
    list.Print()  // List: 0 -> 1 -> 2 -> nil
    
    list.Delete(1)
    list.Print()  // List: 0 -> 2 -> nil
    
    val, _ := list.GetAt(1)
    fmt.Println("Index 1:", val)  // 2
}
```

---

## Recursos

### PDFs de Referencia

- [[Attachments/02-EstructurasLineales.pdf|Estructuras Lineales]]
- [[Attachments/Programacion-estructurada-en-Go-lang.pdf|Programación en Go]]

---

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Pilas (Stack)]]
- [[Colas (Queue)]]
- [[Listas Enlazadas]]
- [[Deques]]
