---
title: Recursion
type: zettel
created: 2026-04-03
tags: [algoritmos, recursion, programacion]
related: [Estructuras de Datos y Analisis de Algoritmos, Divide and Conquer, Programacion Dinamica]
---

# Recursion

## Definición

La **recursión** es una técnica de programación donde una función se llama a sí misma para resolver un problema dividiéndolo en subproblemas más pequeños del mismo tipo.

> [!tip] Analogía
> Imagina ver dos espejos enfrentados: cada uno genera una imagen infinita de sí misma. Cada llamada recursiva crea una "copia" del problema.

## Componentes Fundamentales

### Caso Base

Es la condición que detiene la recursión. Sin él, la función se llamaría infinitamente.

```go
func factorial(n int) int {
    if n <= 1 {  // Caso base
        return 1
    }
    return n * factorial(n-1)
}
```

### Caso Recursivo

Es la parte donde la función se llama a sí misma con una versión más pequeña del problema.

```go
return n * factorial(n-1)  // Caso recursivo
```

## Recursión vs Iteración

| Aspecto | Recursión | Iteración |
|---------|-----------|-----------|
| **Definición** | Auto-llamada de función | Repeticiones con bucles |
| **Memoria** | Usa stack de llamadas | Usa memoria constante |
| **Legibilidad** | Más limpia para problemas recursivos | Más eficiente en casos simples |
| **Riesgo** | Stack overflow si es muy profunda | Menos riesgo |
| **Uso** | Árboles, grafos, divide & conquer | Recorridos simples, filtros |

> [!note] Cuándo usar cada uno
> - **Recursión**: Cuando el problema tiene estructura recursiva natural (factorial, árboles, Torres de Hanoi)
> - **Iteración**: Cuando puedes usar bucles simples y el problema no lo requiere

## Ejemplos Clásicos

### 1. Factorial

```go
// Iterativo
func factorialIter(n int) int {
    result := 1
    for i := 2; i <= n; i++ {
        result *= i
    }
    return result
}

// Recursivo
func factorialRec(n int) int {
    if n <= 1 {
        return 1
    }
    return n * factorialRec(n-1)
}

// Factorial con recursión de cola (tail recursion)
func factorialTail(n, acc int) int {
    if n <= 1 {
        return acc
    }
    return factorialTail(n-1, n*acc)
}
```

### 2. Sucesion de Fibonacci

```go
// Naive - O(2^n) - EXPONENCIAL!
func fibonacci(n int) int {
    if n <= 1 {
        return n
    }
    return fibonacci(n-1) + fibonacci(n-2)
}

// Con memoización - O(n)
func fibonacciMemo(n int, memo map[int]int) int {
    if n <= 1 {
        return n
    }
    if val, ok := memo[n]; ok {
        return val
    }
    memo[n] = fibonacciMemo(n-1, memo) + fibonacciMemo(n-2, memo)
    return memo[n]
}

// Iterativo - O(n)
func fibonacciIter(n int) int {
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

### 3. Suma de Elementos de un Array

```go
func sumArray(arr []int, index int) int {
    if index >= len(arr) {  // Caso base
        return 0
    }
    return arr[index] + sumArray(arr, index+1)
}

// Versión con punteros (más eficiente en memoria)
func sumRange(arr []int, left, right int) int {
    if left > right {
        return 0
    }
    mid := (left + right) / 2
    return sumRange(arr, left, mid) + sumRange(arr, mid+1, right)
}
```

## Análisis de Complejidad

### Complejidad Temporal

| Ejemplo | Complejidad | Explicación |
|---------|-------------|-------------|
| **Factorial** | O(n) | Llamadas lineales |
| **Fibonacci (naive)** | O(2^n) | Árbol de llamadas exponencial |
| **Fibonacci (memoizado)** | O(n) | Cada subproblema se resuelve una vez |
| **Suma de array** | O(n) | Una llamada por elemento |

### Complejidad Espacial

```go
// Stack de llamadas para factorial(5):
// factorial(5)
//   → factorial(4)
//     → factorial(3)
//       → factorial(2)
//         → factorial(1)
//           → return 1
//         → return 2
//       → return 6
//     → return 24
//   → return 120
// 
// Profundidad: O(n)
```

| Implementación | Complejidad Espacial |
|----------------|---------------------|
| Factorial simple | O(n) |
| Factorial tail recursion | O(1) |
| Fibonacci memoizado | O(n) |
| Fibonacci iterativo | O(1) |

## Recursión de Cola (Tail Recursion)

Optimización donde la última operación es la llamada recursiva. El compilador puede reusar el mismo frame de stack.

```go
// No es tail recursion (la última operación es multiplicación)
func factorial(n int) int {
    if n <= 1 {
        return 1
    }
    return n * factorial(n-1)  // Multiplicación después de retornar
}

// SÍ es tail recursion (la última operación es la llamada)
func factorialTail(n, accumulator int) int {
    if n <= 1 {
        return accumulator
    }
    return factorialTail(n-1, n*accumulator)  // La llamada es lo último
}
```

## Implementación en Go

### Recursión Genérica

```go
package main

import "fmt"

// Node genérico para listas enlazadas
type Node[T any] struct {
    Value T
    Next  *Node[T]
}

// Suma recursiva de elementos
func sumList[T Numeric](head *Node[T]) T {
    if head == nil {
        return 0
    }
    return head.Value + sumList(head.Next)
}

// Binary search recursivo
func binarySearch(arr []int, target, left, right int) int {
    if left > right {
        return -1
    }
    mid := (left + right) / 2
    if arr[mid] == target {
        return mid
    } else if arr[mid] < target {
        return binarySearch(arr, target, mid+1, right)
    } else {
        return binarySearch(arr, target, left, mid-1)
    }
}

// Invertir string recursivo
func reverseString(s string) string {
    if len(s) <= 1 {
        return s
    }
    return reverseString(s[1:]) + string(s[0])
}

// Torres de Hanoi
func hanoi(n int, from, to, aux string) {
    if n == 1 {
        fmt.Printf("Mover disco 1 de %s a %s\n", from, to)
        return
    }
    hanoi(n-1, from, aux, to)
    fmt.Printf("Mover disco %d de %s a %s\n", n, from, to)
    hanoi(n-1, aux, to, from)
}
```

## Aplicaciones Reales

| Aplicación | Uso de Recursión |
|------------|------------------|
| **DFS en grafos** | Exploración profunda de árboles y grafos |
| **OrdenamientoMerge Sort** | Dividir array hasta elementos individuales |
| **Parseo de expresiones** | Árboles de sintaxis |
| **Traversal de directorios** | Exploración recursiva de carpetas |
| **Algoritmos de backtracking** | N-Queens, Sudoku, Knight's Tour |

## Ejercicios de Práctica

```go
// 1. Contar dígitos de un número
func countDigits(n int) int {
    if n/10 == 0 {
        return 1
    }
    return 1 + countDigits(n/10)
}

// 2. Verificar palindromo
func isPalindrome(s string) bool {
    if len(s) <= 1 {
        return true
    }
    if s[0] != s[len(s)-1] {
        return false
    }
    return isPalindrome(s[1 : len(s)-1])
}

// 3. Potencia (a^b)
func power(a, b int) int {
    if b == 0 {
        return 1
    }
    return a * power(a, b-1)
}

// 4. Suma de dígitos
func sumDigits(n int) int {
    if n == 0 {
        return 0
    }
    return n%10 + sumDigits(n/10)
}
```

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Divide and Conquer]]
- [[Programacion Dinamica]]
- [[Grafos - Recorridos]]
- [[Ordenamiento Avanzado]]
- [[03-Recursión.pdf]]
