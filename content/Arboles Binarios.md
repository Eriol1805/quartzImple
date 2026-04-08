---
title: Árboles Binarios
type: zettel
created: 2026-04-03
tags: [estructura-datos,-arboles,arbol-binario]
related: [Estructuras de Datos y Análisis de Algoritmos, BST (Binary Search Tree), AVL, Heap]
---

# Árboles Binarios

## Definición

Un **árbol binario** es una estructura de datos jerárquica donde cada nodo tiene **como máximo dos hijos**, denominados hijo izquierdo e hijo derecho.

> [!tip] Analogía
> Un árbol genealógico donde cada persona tiene máximo dos hijos: ramas izquierda y derecha del árbol familiar.

## Componentes del Árbol

```
         [Raíz]
        /     \
    [HI]       [HD]
    /  \       /  \
[Ho] [Ho]   [Ho] [Ho]

- Raíz: Primer nodo del árbol
- Hijos: Nodos derivados de un padre
- Hojas: Nodos sin hijos
- Altura: Nivel máximo del árbol
- Grado: Número de hijos (máximo 2)
```

## Tipos de Árboles Binarios

| Tipo | Descripción |
|------|-------------|
| **Completo** | Todos los niveles tienen máximo de nodos excepto el último |
| **Perfecto** | Todos los nodos tienen dos hijos y todas las hojas están al mismo nivel |
| **Balanceado** | La diferencia de altura entre subárboles es como máximo 1 |
| **Degenerado** | Cada nodo tiene solo un hijo (como una lista enlazada) |

## Recorridos (Traversals)

Los recorridos permiten visitar todos los nodos en un orden específico.

### 1. In-Order (Inorden)

Izquierda → Raíz → Derecha. **Resultado ordenado ascendentemente**.

```
       10
      /  \
     5    15
    / \     \
   3   7    20

In-order: 3 → 5 → 7 → 10 → 15 → 20
```

### 2. Pre-Order (Preorden)

Raíz → Izquierda → Derecha. **Usado para copiar árboles**.

```
       10
      /  \
     5    15
    / \     \
   3   7    20

Pre-order: 10 → 5 → 3 → 7 → 15 → 20
```

### 3. Post-Order (Postorden)

Izquierda → Derecha → Raíz. **Usado para eliminar nodos**.

```
       10
      /  \
     5    15
    / \     \
   3   7    20

Post-order: 3 → 7 → 5 → 20 → 15 → 10
```

### 4. Level-Order (Por niveles)

Por niveles de izquierda a derecha usando **BFS**.

```
       10
      /  \
     5    15
    / \     \
   3   7    20

Level-order: 10 → 5 → 15 → 3 → 7 → 20
```

## Implementación en Go

```go
package main

import (
    "fmt"
    "container/list"
)

// Nodo del árbol binario
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}

// NewTreeNode crea un nuevo nodo
func NewTreeNode(val int) *TreeNode {
    return &TreeNode{Val: val}
}

// ============ RECORRIDOS ============

// In-order: Izquierda → Raíz → Derecha
func inOrder(root *TreeNode) []int {
    var result []int
    var inOrderHelper func(*TreeNode)
    
    inOrderHelper = func(node *TreeNode) {
        if node == nil {
            return
        }
        inOrderHelper(node.Left)
        result = append(result, node.Val)
        inOrderHelper(node.Right)
    }
    
    inOrderHelper(root)
    return result
}

// Pre-order: Raíz → Izquierda → Derecha
func preOrder(root *TreeNode) []int {
    var result []int
    var preOrderHelper func(*TreeNode)
    
    preOrderHelper = func(node *TreeNode) {
        if node == nil {
            return
        }
        result = append(result, node.Val)
        preOrderHelper(node.Left)
        preOrderHelper(node.Right)
    }
    
    preOrderHelper(root)
    return result
}

// Post-order: Izquierda → Derecha → Raíz
func postOrder(root *TreeNode) []int {
    var result []int
    var postOrderHelper func(*TreeNode)
    
    postOrderHelper = func(node *TreeNode) {
        if node == nil {
            return
        }
        postOrderHelper(node.Left)
        postOrderHelper(node.Right)
        result = append(result, node.Val)
    }
    
    postOrderHelper(root)
    return result
}

// Level-order usando cola (BFS)
func levelOrder(root *TreeNode) []int {
    if root == nil {
        return nil
    }
    
    var result []int
    queue := list.New()
    queue.PushBack(root)
    
    for queue.Len() > 0 {
        node := queue.Remove(queue.Front()).(*TreeNode)
        result = append(result, node.Val)
        
        if node.Left != nil {
            queue.PushBack(node.Left)
        }
        if node.Right != nil {
            queue.PushBack(node.Right)
        }
    }
    
    return result
}
```

## Operaciones Adicionales

```go
// Contar nodos
func countNodes(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return 1 + countNodes(root.Left) + countNodes(root.Right)
}

// Contar hojas
func countLeaves(root *TreeNode) int {
    if root == nil {
        return 0
    }
    if root.Left == nil && root.Right == nil {
        return 1
    }
    return countLeaves(root.Left) + countLeaves(root.Right)
}

// Calcular altura
func height(root *TreeNode) int {
    if root == nil {
        return -1  // -1 para árbol vacío, 0 para solo raíz
    }
    leftHeight := height(root.Left)
    rightHeight := height(root.Right)
    
    if leftHeight > rightHeight {
        return leftHeight + 1
    }
    return rightHeight + 1
}

// Encontrar máximo
func findMax(root *TreeNode) int {
    if root == nil {
        return -1 << 31  // Int min
    }
    max := root.Val
    leftMax := findMax(root.Left)
    rightMax := findMax(root.Right)
    
    if leftMax > max {
        max = leftMax
    }
    if rightMax > max {
        max = rightMax
    }
    return max
}

// Buscar elemento
func search(root *TreeNode, target int) bool {
    if root == nil {
        return false
    }
    if root.Val == target {
        return true
    }
    return search(root.Left, target) || search(root.Right, target)
}
```

## Complejidad

| Operación | Tiempo | Espacio |
|------------|--------|---------|
| **In-order** | O(n) | O(h) |
| **Pre-order** | O(n) | O(h) |
| **Post-order** | O(n) | O(h) |
| **Level-order** | O(n) | O(w) |
| **Buscar** | O(n) | O(h) |
| **Altura** | O(n) | O(h) |

> [!note] Donde h = altura del árbol, w = ancho máximo (ancho del nivel más amplio)

## Árbol Binario como Tipo Abstracto de Datos

| Operación | Descripción |
|-----------|-------------|
| **Insert** | Insertar nodo |
| **Delete** | Eliminar nodo |
| **Search** | Buscar por valor |
| **Traverse** | Recorrer todos los nodos |
| **Height** | Calcular altura |
| **Size** | Contar nodos |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[BST (Binary Search Tree)]]
- [[AVL]]
- [[Heap]]
- [[Grafos - Recorridos]]
