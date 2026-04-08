---
title: BST (Binary Search Tree)
type: zettel
created: 2026-04-03
tags: [estructura-datos,arboles,bst,busqueda]
related: [Estructuras de Datos y Análisis de Algoritmos, Arboles Binarios, AVL, Tablas Hash]
---

# BST (Binary Search Tree)

## Definición

Un **BST** (Binary Search Tree) es un árbol binario donde:
- El valor de cada nodo es mayor que todos los valores en su subárbol izquierdo
- El valor de cada nodo es menor que todos los valores en su subárbol derecho
- Esta propiedad se cumple recursivamente para todos los nodos

> [!tip] Búsqueda eficiente
> Permite búsqueda binaria con complejidad O(log n) promedio en árboles balanceados.

## Representación Visual

```
           50
         /    \
       30      70
      /  \    /  \
    20   40  60   80

Propiedad BST:
- 30 > 20 (menor subárbol izquierdo)
- 30 < 40 (mayor subárbol derecho)
- 70 > 60 (menor subárbol izquierdo)
- 70 < 80 (mayor subárbol derecho)
```

## Implementación en Go

```go
package main

import "fmt"

// Nodo del BST
type BSTNode struct {
    Val   int
    Left  *BSTNode
    Right *BSTNode
}

func NewBSTNode(val int) *BSTNode {
    return &BSTNode{Val: val}
}

// BST estructura completa
type BST struct {
    Root *BSTNode
    Len  int
}

func NewBST() *BST {
    return &BST{Root: nil, Len: 0}
}
```

## Operaciones Principales

### 1. Búsqueda (Search)

```go
// Búsqueda iterativa - O(log n) promedio
func (b *BST) Search_iterative(val int) bool {
    current := b.Root
    for current != nil {
        if val == current.Val {
            return true
        } else if val < current.Val {
            current = current.Left
        } else {
            current = current.Right
        }
    }
    return false
}

// Búsqueda recursiva
func (b *BST) Search(val int) bool {
    return searchHelper(b.Root, val)
}

func searchHelper(node *BSTNode, val int) bool {
    if node == nil {
        return false
    }
    if val == node.Val {
        return true
    } else if val < node.Val {
        return searchHelper(node.Left, val)
    } else {
        return searchHelper(node.Right, val)
    }
}
```

### 2. Inserción (Insert)

```go
// Insert - iterative
func (b *BST) Insert(val int) {
    newNode := NewBSTNode(val)
    
    if b.Root == nil {
        b.Root = newNode
        b.Len++
        return
    }
    
    current := b.Root
    for {
        if val < current.Val {
            if current.Left == nil {
                current.Left = newNode
                b.Len++
                return
            }
            current = current.Left
        } else if val > current.Val {
            if current.Right == nil {
                current.Right = newNode
                b.Len++
                return
            }
            current = current.Right
        } else {
            // Valor duplicado, no se inserta
            return
        }
    }
}

// Insert - recursivo
func (b *BST) InsertRecursive(val int) {
    b.Root = insertHelper(b.Root, val)
}

func insertHelper(node *BSTNode, val int) *BSTNode {
    if node == nil {
        b.Len++
        return NewBSTNode(val)
    }
    
    if val < node.Val {
        node.Left = insertHelper(node.Left, val)
    } else if val > node.Val {
        node.Right = insertHelper(node.Right, val)
    }
    return node
}
```

### 3. Eliminación (Delete)

El caso más complejo. Hay 3 escenarios:

1. **Nodo hoja**: Simple, eliminar directamente
2. **Un hijo**: Reemplazar por el hijo
3. **Dos hijos**: Reemplazar por el sucesor in-order (mínimo del subárbol derecho) o predecesor (máximo del subárbol izquierdo)

```go
func (b *BST) Delete(val int) {
    b.Root = deleteHelper(b.Root, val)
}

func deleteHelper(node *BSTNode, val int) *BSTNode {
    if node == nil {
        return nil
    }
    
    if val < node.Val {
        node.Left = deleteHelper(node.Left, val)
    } else if val > node.Val {
        node.Right = deleteHelper(node.Right, val)
    } else {
        // Caso 1: Nodo hoja
        if node.Left == nil && node.Right == nil {
            return nil
        }
        
        // Caso 2: Un hijo
        if node.Left == nil {
            return node.Right
        }
        if node.Right == nil {
            return node.Left
        }
        
        // Caso 3: Dos hijos
        // Encontrar mínimo del subárbol derecho (sucesor in-order)
        minLargerNode := findMin(node.Right)
        node.Val = minLargerNode.Val
        node.Right = deleteHelper(node.Right, minLargerNode.Val)
    }
    return node
}

func findMin(node *BSTNode) *BSTNode {
    current := node
    for current.Left != nil {
        current = current.Left
    }
    return current
}

func findMax(node *BSTNode) *BSTNode {
    current := node
    for current.Right != nil {
        current = current.Right
    }
    return current
}
```

## Recorridos Específicos del BST

```go
// In-order en BST siempre devuelve valores ordenados
func (b *BST) InOrder() []int {
    var result []int
    var inOrder func(*BSTNode)
    
    inOrder = func(node *BSTNode) {
        if node == nil {
            return
        }
        inOrder(node.Left)
        result = append(result, node.Val)
        inOrder(node.Right)
    }
    
    inOrder(b.Root)
    return result
}

// Encontrar mínimo
func (b *BST) FindMin() *BSTNode {
    if b.Root == nil {
        return nil
    }
    return findMin(b.Root)
}

// Encontrar máximo
func (b *BST) FindMax() *BSTNode {
    if b.Root == nil {
        return nil
    }
    return findMax(b.Root)
}
```

## Análisis de Complejidad

| Operación | Promedio | Peor Caso |
|-----------|----------|-----------|
| **Búsqueda** | O(log n) | O(n) |
| **Inserción** | O(log n) | O(n) |
| **Eliminación** | O(log n) | O(n) |
| **Recorrido** | O(n) | O(n) |
| **Encontrar Mín/Máx** | O(log n) | O(n) |

> [!warning] Peor caso
> El peor caso ocurre cuando el árbol se degenera en una lista enlazada (inserciones ordenadas: 1, 2, 3, 4, 5...)

## Balanceo del BST

Para garantizar O(log n), el árbol debe estar balanceado. Alternativas:

| Tipo | Balanceo | Complejidad |
|------|----------|-------------|
| **BST simple** | No guarantee | O(n) worst |
| **AVL** | Altura ±1 | O(log n) |
| **Red-Black** | Altura ≤2log(n+1) | O(log n) |
| **B-Tree** | Optimizado para disco | O(log n) |

## Ejemplo Completo

```go
func main() {
    bst := NewBST()
    
    // Insertar valores
    values := []int{50, 30, 70, 20, 40, 60, 80, 15, 25, 35, 45}
    for _, v := range values {
        bst.Insert(v)
    }
    
    // Recorrido in-order (ordenado)
    fmt.Println("In-order:", bst.InOrder())
    // Output: [15 20 25 30 35 40 45 50 60 70 80]
    
    // Búsqueda
    fmt.Println("Buscar 40:", bst.Search(40))  // true
    fmt.Println("Buscar 100:", bst.Search(100)) // false
    
    // Mín / Máx
    fmt.Println("Mínimo:", bst.FindMin().Val)  // 15
    fmt.Println("Máximo:", bst.FindMax().Val) // 80
    
    // Eliminar
    bst.Delete(20)
    fmt.Println("In-order después de eliminar 20:", bst.InOrder())
}
```

## Aplicaciones

| Aplicación | Uso |
|------------|-----|
| **Bases de datos** | Índices B-Tree y variants |
| **Compiladores** | Árboles de sintaxis |
| **Autocompletar** | Búsqueda predictiva |
| **Conjuntos ordenados** | Set en muchos lenguajes |
| **Maps** | Implementación de diccionarios |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Arboles Binarios]]
- [[AVL]]
- [[Tablas Hash]]
