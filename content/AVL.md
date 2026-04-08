---
title: AVL
type: zettel
created: 2026-04-03
tags: [estructura-datos,arboles,avl,balanceado]
related: [Estructuras de Datos y Análisis de Algoritmos, Arboles Binarios, BST (Binary Search Tree)]
---

# AVL

## Definición

Un **árbol AVL** es un BST auto-balanceado donde la diferencia de altura entre los subárboles izquierdo y derecho de cualquier nodo es como máximo **1**. Esta propiedad se conoce como **factor de balance**.

> [!info] Historia
> Nombrado por sus inventores: Adelson-Velsky y Landis (1962). Fue el primer árbol binario de búsqueda balanceado automáticamente.

## Conceptos Clave

### Factor de Balance (BF)

```
BF(nodo) = altura(subárbol derecho) - altura(subárbol izquierdo)

BF válido: -1, 0, 1
BF inválido: -2, 2 (requiere re-balanceo)
```

### Altura

La altura de un nodo es la longitud del camino más largo desde ese nodo hasta una hoja.

```
         30 (altura 3)
        /  \
      20   40 (altura 2)
     /     \
   10      50 (altura 1)
```

## Rotaciones

Cuando el factor de balance sale de [-1, 1], se realizan **rotaciones** para balancear el árbol.

### Rotación Simple Derecha (LL)

Usada cuando un nodo tiene hijo izquierdo pesado (BF = -2) y el hijo tiene BF = -1 o 0.

```
       30 (-2)           rotar          20
      /                            /    \
    20 (-1)         →            10     30
    /
  10
```

### Rotación Simple Izquierda (RR)

Usada cuando un nodo tiene hijo derecho pesado (BF = 2) y el hijo tiene BF = 1 o 0.

```
    30 (+2)                     40
        \                    /    \
      40 (+1)       →      30     50
          \
          50
```

### Rotación Doble Izquierda-Derecha (LR)

Usada cuando un nodo tiene hijo izquierdo pesado pero ese hijo tiene BF = 1.

```
      30 (-2)              30                20
      /                    \               /  \
    20 (+1)      →        20      →      10   30
      \                  /
      25                25
```

### Rotación Doble Derecha-Izquierda (RL)

Usada cuando un nodo tiene hijo derecho pesado pero ese hijo tiene BF = -1.

```
        30 (+2)           30              40
            \            /               /  \
          40 (-1) →    40      →       30   50
          /
        35
```

## Implementación en Go

```go
package main

import "fmt"

// Nodo AVL
type AVLNode struct {
    Val    int
    Left   *AVLNode
    Right  *AVLNode
    Height int
}

func NewAVLNode(val int) *AVLNode {
    return &AVLNode{Val: val, Height: 1}
}

// Estructura AVL
type AVL struct {
    Root *AVLNode
}

// Obtener altura
func getHeight(node *AVLNode) int {
    if node == nil {
        return 0
    }
    return node.Height
}

// Obtener factor de balance
func getBalance(node *AVLNode) int {
    if node == nil {
        return 0
    }
    return getHeight(node.Right) - getHeight(node.Left)
}

// Actualizar altura
func updateHeight(node *AVLNode) {
    if node == nil {
        return
    }
    leftH := getHeight(node.Left)
    rightH := getHeight(node.Right)
    if leftH > rightH {
        node.Height = leftH + 1
    } else {
        node.Height = rightH + 1
    }
}

// ============ ROTACIONES ============

// Rotación derecha
func rightRotate(y *AVLNode) *AVLNode {
    x := y.Left
    T2 := x.Right
    
    // Realizar rotación
    x.Right = y
    y.Left = T2
    
    // Actualizar alturas
    updateHeight(y)
    updateHeight(x)
    
    return x
}

// Rotación izquierda
func leftRotate(x *AVLNode) *AVLNode {
    y := x.Right
    T2 := y.Left
    
    // Realizar rotación
    y.Left = x
    x.Right = T2
    
    // Actualizar alturas
    updateHeight(x)
    updateHeight(y)
    
    return y
}
```

## Inserción en AVL

```go
func (a *AVL) Insert(val int) {
    a.Root = insertAVL(a.Root, val)
}

func insertAVL(node *AVLNode, val int) *AVLNode {
    // 1. Inserción normal de BST
    if node == nil {
        return NewAVLNode(val)
    }
    
    if val < node.Val {
        node.Left = insertAVL(node.Left, val)
    } else if val > node.Val {
        node.Right = insertAVL(node.Right, val)
    } else {
        // Duplicados no permitidos
        return node
    }
    
    // 2. Actualizar altura
    updateHeight(node)
    
    // 3. Obtener factor de balance
    balance := getBalance(node)
    
    // 4. Rotaciones según el caso
    // Caso LL: Rotación derecha
    if balance < -1 && val < node.Left.Val {
        fmt.Println("Rotación Derecha (LL)")
        return rightRotate(node)
    }
    
    // Caso RR: Rotación izquierda
    if balance > 1 && val > node.Right.Val {
        fmt.Println("Rotación Izquierda (RR)")
        return leftRotate(node)
    }
    
    // Caso LR: Rotación izquierda-derecha
    if balance < -1 && val > node.Left.Val {
        fmt.Println("Rotación Doble LR")
        node.Left = leftRotate(node.Left)
        return rightRotate(node)
    }
    
    // Caso RL: Rotación derecha-izquierda
    if balance > 1 && val < node.Right.Val {
        fmt.Println("Rotación Doble RL")
        node.Right = rightRotate(node.Right)
        return leftRotate(node)
    }
    
    return node
}
```

## Eliminación en AVL

```go
func (a *AVL) Delete(val int) {
    a.Root = deleteAVL(a.Root, val)
}

func deleteAVL(node *AVLNode, val int) *AVLNode {
    if node == nil {
        return nil
    }
    
    if val < node.Val {
        node.Left = deleteAVL(node.Left, val)
    } else if val > node.Val {
        node.Right = deleteAVL(node.Right, val)
    } else {
        // Nodo encontrado - eliminar
        if node.Left == nil {
            return node.Right
        }
        if node.Right == nil {
            return node.Left
        }
        
        // Nodo con dos hijos: obtener succesor in-order
        minLarger := findMinAVL(node.Right)
        node.Val = minLarger.Val
        node.Right = deleteAVL(node.Right, minLarger.Val)
    }
    
    // Actualizar altura y rebalancear
    if node == nil {
        return nil
    }
    
    updateHeight(node)
    balance := getBalance(node)
    
    // Rebalancear según caso
    // LL
    if balance < -1 && getBalance(node.Left) <= 0 {
        return rightRotate(node)
    }
    // LR
    if balance < -1 && getBalance(node.Left) > 0 {
        node.Left = leftRotate(node.Left)
        return rightRotate(node)
    }
    // RR
    if balance > 1 && getBalance(node.Right) >= 0 {
        return leftRotate(node)
    }
    // RL
    if balance > 1 && getBalance(node.Right) < 0 {
        node.Right = rightRotate(node.Right)
        return leftRotate(node)
    }
    
    return node
}

func findMinAVL(node *AVLNode) *AVLNode {
    current := node
    for current.Left != nil {
        current = current.Left
    }
    return current
}
```

## Recorridos

```go
func inOrderAVL(node *AVLNode) []int {
    var result []int
    var inOrder func(*AVLNode)
    
    inOrder = func(n *AVLNode) {
        if n == nil {
            return
        }
        inOrder(n.Left)
        result = append(result, n.Val)
        inOrder(n.Right)
    }
    
    inOrder(node)
    return result
}

func (a *AVL) InOrder() []int {
    return inOrderAVL(a.Root)
}
```

## Análisis de Complejidad

| Operación | Tiempo | Espacio |
|------------|--------|---------|
| **Búsqueda** | O(log n) | O(log n) |
| **Inserción** | O(log n) | O(log n) |
| **Eliminación** | O(log n) | O(log n) |
| **Rotación** | O(1) | O(1) |

> [!note] Garantía
> AVL garantiza O(log n) para todas las operaciones porque la altura máxima es ~1.44 * log₂(n+2)

## AVL vs Red-Black vs BST simple

| Característica | BST Simple | AVL | Red-Black |
|----------------|-----------|-----|-----------|
| Balance | No | Estricto (±1) | Relajado |
| Altura máx | n | 1.44 log n | 2 log n |
| Búsqueda | O(n) | O(log n) | O(log n) |
| Inserción | O(n) | O(log n) | O(log n) |
| Rotaciones | Ninguna | Siempre | Ocasional |
| Uso memoria | Bajo | Medio | Medio |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Arboles Binarios]]
- [[BST (Binary Search Tree)]]
