---
title: Programacion Dinamica
type: zettel
created: 2026-04-03
tags: [algoritmos,tecnicas-algoritmicas,programacion-dinamica,dp]
related: [Estructuras de Datos y Análisis de Algoritmos, Divide and Conquer, Greedy, Recursion]
---

# Programacion Dinamica

## Definición

La **Programación Dinámica** (DP) es una técnica algorítmica que resuelve problemas dividiéndolos en subproblemas que se solapan, almacenando las soluciones para evitar cálculos重复ados.

> [!tip] Diferencia clave vs Divide and Conquer
> - **Divide and Conquer**: Subproblemas son independientes
> - **DP**: Subproblemas se solapan y comparten soluciones

## Cuándo Usar DP

| Característica | Descripción |
|----------------|-------------|
| **Subproblemas repetidos** | Mismo subproblema aparece múltiples veces |
| **Subestructura óptima** | Solución óptima se construye con soluciones óptimas de subproblemas |
| **Memoización posible** | Se pueden guardar y reutilizar resultados |

## Enfoques de DP

### 1. Top-Down (Memoización)

```go
package main

import "fmt"

// Fibonacci con memoización (Top-Down)
func fibMemo(n int, memo map[int]int) int {
    // Caso base
    if n <= 1 {
        return n
    }
    
    // Verificar si ya se calculó
    if val, ok := memo[n]; ok {
        return val
    }
    
    // Calcular y guardar
    memo[n] = fibMemo(n-1, memo) + fibMemo(n-2, memo)
    return memo[n]
}

// Wrapper para usar memoización
func fibonacci(n int) int {
    memo := make(map[int]int)
    return fibMemo(n, memo)
}
```

### 2. Bottom-Up (Tabulation)

```go
// Fibonacci con tabulación (Bottom-Up)
func fibonacciBU(n int) int {
    if n <= 1 {
        return n
    }
    
    // Crear tabla
    dp := make([]int, n+1)
    dp[0] = 0
    dp[1] = 1
    
    // Llenar tabla (de abajo hacia arriba)
    for i := 2; i <= n; i++ {
        dp[i] = dp[i-1] + dp[i-2]
    }
    
    return dp[n]
}

// Optimizado: solo necesitamos los últimos dos valores
func fibonacciOptimized(n int) int {
    if n <= 1 {
        return n
    }
    
    prev, curr := 0, 1
    for i := 2; i <= n; i++ {
        prev, curr = curr, prev+curr
    }
    return curr
}
```

## Problemas Clásicos de DP

### 1. Climbing Stairs

```go
// Climbing Stairs - subir escalera
func climbStairs(n int) int {
    if n <= 2 {
        return n
    }
    
    dp := make([]int, n+1)
    dp[1] = 1
    dp[2] = 2
    
    for i := 3; i <= n; i++ {
        dp[i] = dp[i-1] + dp[i-2]
    }
    
    return dp[n]
}

// Con opción de dar 1, 2 o 3 pasos
func climbStairsDP(n int) int {
    dp := make([]int, n+1)
    dp[0] = 1
    
    for i := 1; i <= n; i++ {
        dp[i] = dp[i-1]
        if i >= 2 {
            dp[i] += dp[i-2]
        }
        if i >= 3 {
            dp[i] += dp[i-3]
        }
    }
    
    return dp[n]
}
```

### 2. 0/1 Knapsack

```go
// 0/1 Knapsack - maximizar valor con peso limitado
func knapsack(weights []int, values []int, capacity int) int {
    n := len(weights)
    dp := make([][]int, n+1)
    
    for i := range dp {
        dp[i] = make([]int, capacity+1)
    }
    
    for i := 1; i <= n; i++ {
        for w := 0; w <= capacity; w++ {
            // No incluir el item
            dp[i][w] = dp[i-1][w]
            
            // Incluir el item si es posible
            if weights[i-1] <= w {
                dp[i][w] = max(dp[i][w], values[i-1]+dp[i-1][w-weights[i-1]])
            }
        }
    }
    
    return dp[n][capacity]
}

// Optimizado: solo 1D array
func knapsackOptimized(weights []int, values []int, capacity int) int {
    dp := make([]int, capacity+1)
    
    for i := 0; i < len(weights); i++ {
        for w := capacity; w >= weights[i]; w-- {
            dp[w] = max(dp[w], values[i]+dp[w-weights[i]])
        }
    }
    
    return dp[capacity]
}
```

### 3. Longest Common Subsequence (LCS)

```go
// Longest Common Subsequence
func longestCommonSubsequence(s1, s2 string) int {
    m, n := len(s1), len(s2)
    dp := make([][]int, m+1)
    
    for i := range dp {
        dp[i] = make([]int, n+1)
    }
    
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if s1[i-1] == s2[j-1] {
                dp[i][j] = dp[i-1][j-1] + 1
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }
    
    return dp[m][n]
}

// Reconstruir LCS
func getLCS(s1, s2 string) string {
    m, n := len(s1), len(s2)
    dp := make([][]int, m+1)
    
    for i := range dp {
        dp[i] = make([]int, n+1)
    }
    
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if s1[i-1] == s2[j-1] {
                dp[i][j] = dp[i-1][j-1] + 1
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }
    
    // Reconstruir
    lcs := ""
    i, j := m, n
    for i > 0 && j > 0 {
        if s1[i-1] == s2[j-1] {
            lcs = string(s1[i-1]) + lcs
            i--
            j--
        } else if dp[i-1][j] > dp[i][j-1] {
            i--
        } else {
            j--
        }
    }
    
    return lcs
}
```

### 4. Edit Distance (Levenshtein)

```go
// Edit Distance - mínimo de operaciones para transformar s1 en s2
func minDistance(word1, word2 string) int {
    m, n := len(word1), len(word2)
    dp := make([][]int, m+1)
    
    for i := range dp {
        dp[i] = make([]int, n+1)
    }
    
    // Inicializar
    for i := 0; i <= m; i++ {
        dp[i][0] = i
    }
    for j := 0; j <= n; j++ {
        dp[0][j] = j
    }
    
    // Llenar DP
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if word1[i-1] == word2[j-1] {
                dp[i][j] = dp[i-1][j-1]
            } else {
                dp[i][j] = 1 + min(
                    dp[i-1][j],   // delete
                    dp[i][j-1],   // insert
                    dp[i-1][j-1], // replace
                )
            }
        }
    }
    
    return dp[m][n]
}
```

### 5. Coin Change

```go
// Coin Change - mínimo número de monedas
func coinChange(coins []int, amount int) int {
    dp := make([]int, amount+1)
    for i := range dp {
        dp[i] = amount + 1 // Infinito
    }
    dp[0] = 0
    
    for i := 1; i <= amount; i++ {
        for _, coin := range coins {
            if coin <= i {
                dp[i] = min(dp[i], dp[i-coin]+1)
            }
        }
    }
    
    if dp[amount] > amount {
        return -1
    }
    return dp[amount]
}

// Coin Change -是否可以dar el cambio exacto
func canChange(coins []int, amount int) bool {
    dp := make([]bool, amount+1)
    dp[0] = true
    
    for i := 1; i <= amount; i++ {
        dp[i] = false
        for _, coin := range coins {
            if coin <= i && dp[i-coin] {
                dp[i] = true
                break
            }
        }
    }
    
    return dp[amount]
}
```

### 6. Longest Increasing Subsequence (LIS)

```go
// LIS - Longest Increasing Subsequence
func lengthOfLIS(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    dp := make([]int, len(nums))
    for i := range dp {
        dp[i] = 1
    }
    
    for i := 1; i < len(nums); i++ {
        for j := 0; j < i; j++ {
            if nums[j] < nums[i] {
                dp[i] = max(dp[i], dp[j]+1)
            }
        }
    }
    
    result := 0
    for _, v := range dp {
        result = max(result, v)
    }
    
    return result
}

// LIS optimizado con Binary Search - O(n log n)
func lengthOfLISOptimized(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    tails := []int{nums[0]}
    
    for i := 1; i < len(nums); i++ {
        pos := binarySearchInsert(tails, nums[i])
        if pos == len(tails) {
            tails = append(tails, nums[i])
        } else {
            tails[pos] = nums[i]
        }
    }
    
    return len(tails)
}

func binarySearchInsert(arr []int, target int) int {
    left, right := 0, len(arr)
    for left < right {
        mid := (left + right) / 2
        if arr[mid] < target {
            left = mid + 1
        } else {
            right = mid
        }
    }
    return left
}
```

## Análisis de Complejidad

| Problema | Time | Space |
|----------|------|-------|
| **Fibonacci** | O(n) | O(n) o O(1) |
| **Climbing Stairs** | O(n) | O(n) o O(1) |
| **0/1 Knapsack** | O(n*W) | O(n*W) o O(W) |
| **LCS** | O(m*n) | O(m*n) |
| **Edit Distance** | O(m*n) | O(m*n) o O(min(m,n)) |
| **Coin Change** | O(n*amount) | O(amount) |
| **LIS** | O(n²) o O(n log n) | O(n) |

## DP vs Greedy vs Divide and Conquer

| Técnica | Subproblemas | Solapamiento | Memoria |
|---------|--------------|---------------|----------|
| **Divide and Conquer** | Independientes | No | Stack |
| **Greedy** | No aplica | No aplica | No |
| **DP** | Dependientes | Sí | Tabla |

## Tipos de DP

### 1. DP sobre Secuencias

```go
// Problemas donde la solución depende de prefijos
func dpOnSequence(arr []int) {
    // dp[i] = mejor solución para el subarray 0..i
    dp := make([]int, len(arr))
    // ...
}
```

### 2. DP sobre Matrices

```go
// Problemas de dos dimensiones
func dpOnMatrix(s1, s2 string) {
    // dp[i][j] = mejor solución para s1[0..i] y s2[0..j]
    dp := make([][]int, len(s1)+1)
    // ...
}
```

### 3. DP con Bits

```go
// Problemas donde el estado se representa con bits
func dpWithBits(n int) {
    // dp[mask] = mejor solución con conjunto mask
    dp := make([]int, 1<<n)
    // ...
}
```

## Patrones de DP

| Patrón | Cuando Usar | Ejemplos |
|--------|-------------|----------|
| **Cadena** | Secuencia de decisiones | LCS, Edit Distance |
| **Knapsack** | items con peso/valor | 0/1 Knapsack |
| **Intervalos** | subarrays contiguos | Matrix Chain Multiplication |
| **Bits** | conjuntos pequeños | Traveling Salesman |

## Ejemplo: Matrix Chain Multiplication

```go
// MCM - minimum scalar multiplications
func minMatrices(p []int) int {
    n := len(p) - 1 // número de matrices
    dp := make([][]int, n)
    
    for i := range dp {
        dp[i] = make([]int, n)
    }
    
    // Longitud de la cadena
    for length := 2; length <= n; length++ {
        for i := 0; i < n-length+1; i++ {
            j := i + length - 1
            dp[i][j] = 1 << 31
            
            // Probar todos los puntos de partición
            for k := i; k < j; k++ {
                cost := dp[i][k] + dp[k+1][j] + p[i]*p[k+1]*p[j+1]
                dp[i][j] = min(dp[i][j], cost)
            }
        }
    }
    
    return dp[0][n-1]
}
```

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Recursion]]
- [[Divide and Conquer]]
- [[Greedy]]
- [[Ordenamiento Avanzado]]
