---
title: Pilas (Stack)
type: zettel
created: 2026-04-02
tags: [estructura-datos, pila, stack, tad, lifo]
related: [Colas (Queue), Listas Enlazadas, Tipos Abstractos de Datos (TAD)]
---

# Pilas (Stack)

## Definición

Una **Pila** (Stack) es una estructura de datos lineal que sigue el principio **LIFO** (Last In, First Out): el último elemento en entrar es el primero en salir.

> [!tip] Analogía
> Imagina una pila de platos: solo puedes ver y tomar el plato de arriba.

## Operaciones Principales

| Operación | Descripción | Complejidad |
|-----------|-------------|-------------|
| **Push** (Apilar) | Insertar elemento en la cima | O(1) |
| **Pop** (Desapilar) | Eliminar elemento de la cima | O(1) |
| **Top** (Tope) | Ver elemento en la cima sin eliminar | O(1) |
| **IsEmpty** | Verificar si la pila está vacía | O(1) |
| **Size** | Obtener número de elementos | O(1) |

## Representación Visual

```
Push(A) → [A]
Push(B) → [A, B]
Push(C) → [A, B, C]
           ↑
          Top (C)

Pop() → retorna C  → [A, B]
Pop() → retorna B  → [A]
```

## Aplicaciones Reales

| Aplicación | Descripción |
|------------|-------------|
| **Deshacer/Rehacer** | Historial de acciones en editores |
| **Pila de llamadas** | Gestión de funciones en lenguajes |
| **Evaluación de expresiones** | Parsing de expresiones aritméticas |
| **Navegación web** | Botón atrás del navegador |
| **Paréntesis balanceados** | Validar expresiones con paréntesis |

## Implementación en Go

### Con Slice

```go
type Stack struct {
    items []interface{}
}

func NewStack() *Stack {
    return &Stack{items: []interface{}{}}
}

func (s *Stack) Push(item interface{}) {
    s.items = append(s.items, item)
}

func (s *Stack) Pop() interface{} {
    if len(s.items) == 0 {
        return nil
    }
    item := s.items[len(s.items)-1]
    s.items = s.items[:len(s.items)-1]
    return item
}

func (s *Stack) Top() interface{} {
    if len(s.items) == 0 {
        return nil
    }
    return s.items[len(s.items)-1]
}

func (s *Stack) IsEmpty() bool {
    return len(s.items) == 0
}

func (s *Stack) Size() int {
    return len(s.items)
}
```

### Con Lista Enlazada

```go
type Node struct {
    value interface{}
    next  *Node
}

type StackLinked struct {
    top   *Node
    count int
}

func (s *StackLinked) Push(value interface{}) {
    newNode := &Node{value: value, next: s.top}
    s.top = newNode
    s.count++
}

func (s *StackLinked) Pop() interface{} {
    if s.top == nil {
        return nil
    }
    value := s.top.value
    s.top = s.top.next
    s.count--
    return value
}
```

## Ejemplo: Validar Paréntesis

```go
func isValid(s string) bool {
    stack := NewStack()
    pairs := map[rune]rune{')': '(', ']': '[', '}': '{'}
    
    for _, char := range s {
        if closing, ok := pairs[char]; ok {
            if stack.IsEmpty() || stack.Pop() != closing {
                return false
            }
        } else {
            stack.Push(char)
        }
    }
    return stack.IsEmpty()
}
```

## Complejidad

| Operación | Array | Lista Enlazada |
|-----------|-------|----------------|
| Push | O(1) | O(1) |
| Pop | O(1) | O(1) |
| Top | O(1) | O(1) |
| Search | O(n) | O(n) |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Tipos Abstractos de Datos (TAD)]]
- [[Colas (Queue)]]
