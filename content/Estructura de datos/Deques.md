---
title: Deques
type: zettel
created: 2026-04-02
tags: [estructura-datos, deque, cola-doble, tad]
related: [Pilas (Stack), Colas (Queue), Listas Enlazadas]
---

# Deques

## Definición

Un **Deque** (Double Ended Queue) es una estructura de datos lineal que permite inserción y eliminación por ambos extremos: el frente y el final.

> [!tip] Combinación
> Un Deque es como una [[Colas (Queue)]] y una [[Pilas (Stack)]] combinadas.

## Representación Visual

```
        FRENTE                              FINAL
          ↓                                  ↓
    ┌────┬────┬────┬────┬────┬────┬────┐
    │ A  │ B  │ C  │ D  │ E  │ F  │ G  │
    └────┴────┴────┴────┴────┴────┴────┘
    
    Operaciones:
    - InsertFront / RemoveFront
    - InsertRear / RemoveRear
```

## Tipos de Deques

### Input-Restricted Deque
La inserción solo puede ser por un extremo (usualmente el rear).

### Output-Restricted Deque
La eliminación solo puede ser por un extremo.

## Operaciones Principales

| Operación | Descripción | Complejidad |
|-----------|-------------|-------------|
| **InsertFront** | Insertar al inicio | O(1) |
| **InsertRear** | Insertar al final | O(1) |
| **RemoveFront** | Eliminar del inicio | O(1) |
| **RemoveRear** | Eliminar del final | O(1) |
| **GetFront** | Ver frente sin eliminar | O(1) |
| **GetRear** | Ver final sin eliminar | O(1) |

## Aplicaciones

| Aplicación | Descripción |
|------------|-------------|
| **Sliding Window** | Problemas de ventana deslizante |
| **LRU Cache** | Cache de menor uso reciente |
| **Undo/Redo** | Historial de acciones |
| **Palindrome** | Verificar palabras palindrónicas |
| **Colas de prioridad** | Implementación eficiente |

## Implementación en Go

### Deque con Slice Circular

```go
type Deque struct {
    items    []interface{}
    front    int
    rear     int
    size     int
    capacity int
}

func NewDeque(capacity int) *Deque {
    return &Deque{
        items:    make([]interface{}, capacity),
        capacity: capacity,
    }
}

func (d *Deque) isFull() bool {
    return d.size == d.capacity
}

func (d *Deque) IsEmpty() bool {
    return d.size == 0
}

func (d *Deque) InsertFront(item interface{}) error {
    if d.isFull() {
        return errors.New("deque is full")
    }
    d.front = (d.front - 1 + d.capacity) % d.capacity
    d.items[d.front] = item
    d.size++
    return nil
}

func (d *Deque) InsertRear(item interface{}) error {
    if d.isFull() {
        return errors.New("deque is full")
    }
    d.rear = (d.rear + 1) % d.capacity
    d.items[d.rear] = item
    d.size++
    return nil
}

func (d *Deque) RemoveFront() interface{} {
    if d.IsEmpty() {
        return nil
    }
    item := d.items[d.front]
    d.front = (d.front + 1) % d.capacity
    d.size--
    return item
}

func (d *Deque) RemoveRear() interface{} {
    if d.IsEmpty() {
        return nil
    }
    item := d.items[d.rear]
    d.rear = (d.rear - 1 + d.capacity) % d.capacity
    d.size--
    return item
}

func (d *Deque) GetFront() interface{} {
    if d.IsEmpty() {
        return nil
    }
    return d.items[d.front]
}

func (d *Deque) GetRear() interface{} {
    if d.IsEmpty() {
        return nil
    }
    return d.items[d.rear]
}

func (d *Deque) Size() int {
    return d.size
}
```

## Ejemplo: Verificar Palindrome

```go
func isPalindrome(s string) bool {
    d := NewDeque(len(s))
    
    for _, char := range s {
        d.InsertRear(string(char))
    }
    
    for d.Size() > 1 {
        front := d.RemoveFront().(string)
        rear := d.RemoveRear().(string)
        if front != rear {
            return false
        }
    }
    return true
}
```

## Ejemplo: Máximo en Ventana Deslizante

```go
func maxSlidingWindow(nums []int, k int) []int {
    if len(nums) == 0 {
        return []int{}
    }
    
    result := []int{}
    d := NewDeque(k)
    
    for i, num := range nums {
        // Remover elementos fuera de la ventana
        if !d.IsEmpty() && d.GetFront().(int) <= i-k {
            d.RemoveFront()
        }
        
        // Mantener índices de números mayores
        for !d.IsEmpty() && nums[d.GetRear().(int)] <= num {
            d.RemoveRear()
        }
        
        d.InsertRear(i)
        
        // Agregar máximo cuando la ventana esté completa
        if i >= k-1 {
            result = append(result, nums[d.GetFront().(int)])
        }
    }
    
    return result
}
```

## Complejidad

| Operación | Deque |
|-----------|-------|
| InsertFront | O(1) |
| InsertRear | O(1) |
| RemoveFront | O(1) |
| RemoveRear | O(1) |
| Search | O(n) |

## Comparación con Otras Estructuras

| Estructura | Insert Front | Insert Rear | Remove Front | Remove Rear |
|------------|--------------|-------------|--------------|-------------|
| [[Pilas (Stack)]] | O(1) | - | O(1) | - |
| [[Colas (Queue)]] | - | O(1) | O(1) | - |
| **Deque** | O(1) | O(1) | O(1) | O(1) |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Tipos Abstractos de Datos (TAD)]]
- [[Colas (Queue)]]
