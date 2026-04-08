---
title: Estructuras de Datos y Análisis de Algoritmos
type: literature
created: 2026-04-02
tags: [estructura-datos, algoritmos, estudio]
source: Apuntes de clase - Estructura de Datos
---

# Estructuras de Datos y Análisis de Algoritmos

---

## 1. Fundamentos de Algoritmos y Análisis de Eficiencia

### Definiciones Básicas

| Concepto | Descripción |
|----------|-------------|
| **Algoritmo** | Secuencia de pasos computacionales que transforman una entrada en una salida |
| **Independencia** | Los algoritmos son independientes de los lenguajes de programación |

### Análisis de Algoritmos

> [!info] Enfoques de análisis
> - **Análisis Empírico (A posteriori):** Programar y probar midiendo tiempos reales. Limitaciones: depende del hardware y lenguaje
> - **Análisis Teórico (A priori):** Determina matemáticamente los recursos necesarios sin implementación

El análisis de eficiencia se centra en:
- **Tiempo**: Número de pasos en función del tamaño de entrada (n)
- **Espacio**: Memoria requerida

---

## 2. Complejidad Computacional

### Notación Big O

La **Notación Big O** clasifica algoritmos según su crecimiento asintótico:

| Complejidad | Notación | Ejemplo | Descripción |
|------------|----------|---------|-------------|
| **Constante** | O(1) | Acceso a array por índice | No depende de n |
| **Logarítmica** | O(log n) | Búsqueda binaria | Crecimiento muy lento |
| **Lineal** | O(n) | Búsqueda lineal | Proporcional a n |
| **Lineal-logarítmica** | O(n log n) | Merge Sort | Eficiente para ordenamiento |
| **Cuadrática** | O(n²) | Bubble Sort | Crecimiento rápido |
| **Cúbica** | O(n³) | Multiplicación de matrices | |
| **Exponencial** | O(2ⁿ) | Fuerza bruta | Impractical para n grande |
| **Factorial** | O(n!) | Permutaciones | Muy lento |

> [!tip] Selección de algoritmo
> Para entrada grande, la diferencia entre O(n²) y O(n log n) es significativa. Elegir siempre el algoritmo con menor complejidad posible.

---

## 3. Tipos Abstractos de Datos (TAD)

### Concepto de Abstracción

Un **TAD** (Tipo Abstracto de Datos) define:
- Un conjunto de datos
- Las operaciones permitidas sobre ellos
- Sin especificar la implementación interna

### Especificación de un TAD

| Componente | Descripción |
|------------|-------------|
| **Conjunto de datos** | Qué elementos almacena |
| **Operaciones** | Qué se puede hacer (Sintaxis) |
| **Comportamiento** | Efecto de las operaciones (Semántica/Axiomas) |

> [!note] Beneficio de los TADs
> La abstracción permite cambiar la implementación interna sin afectar el código que usa el TAD.

---

## 4. Estructuras de Datos Lineales

Las estructuras lineales se caracterizan porque cada elemento tiene un solo predecesor (excepto el primero) y un solo sucesor (excepto el último).

### Clasificación

| Estructura           | Principio                              | Operaciones Principales                          |
| -------------------- | -------------------------------------- | ------------------------------------------------ |
| [[Pilas (Stack)]]    | LIFO (Last In, First Out)              | Push, Pop, Top                                   |
| [[Colas (Queue)]]    | FIFO (First In, First Out)             | Enqueue, Dequeue                                 |
| [[Listas Enlazadas]] | Secuencia de nodos                     | Insert, Delete, Search                           |
| [[Deques]]           | Inserción/eliminción por ambos extremos | InsertFront, InsertBack, RemoveFront, RemoveBack |

---

## 5. Estructuras de Datos No Lineales

### Árboles

| Estructura | Descripción | Complejidad Búsqueda |
|------------|-------------|----------------------|
| [[Arboles Binarios]] | Árbol donde cada nodo tiene máximo 2 hijos | O(n) |
| [[BST (Binary Search Tree)]] | BST con propiedad de orden | O(log n) promedio |
| [[AVL]] | BST auto-balanceado | O(log n) |
| [[Heap]] | Árbol completo con propiedad heap | O(log n) |

### Grafos

| Estructura | Descripción |
|------------|-------------|
| [[Grafos - Fundamentos]] | Definiciones, representaciones (matriz/lista adyacencia) |
| [[Grafos - Recorridos]] | BFS y DFS |
| [[Grafos - Algoritmos]] | Dijkstra, Kruskal, Topological Sort |

### Tablas Hash

| Estructura | Descripción |
|------------|-------------|
| [[Tablas Hash]] |映射 key-value con función hash, resolución de colisiones |

---

## 6. Algoritmos de Ordenamiento

### Ordenamiento Básico (O(n²))

| Algoritmo | Best Case | Promedio | Peor Caso | Estable |
|-----------|-----------|----------|-----------|---------|
| [[Ordenamiento Basico#Bubble Sort|Bubble Sort]] | O(n) | O(n²) | O(n²) | ✅ |
| [[Ordenamiento Basico#Selection Sort|Selection Sort]] | O(n²) | O(n²) | O(n²) | ❌ |
| [[Ordenamiento Basico#Insertion Sort|Insertion Sort]] | O(n) | O(n²) | O(n²) | ✅ |

### Ordenamiento Avanzado (O(n log n))

| Algoritmo | Best Case | Promedio | Peor Caso | Espacio |
|-----------|-----------|----------|-----------|---------|
| [[Ordenamiento Avanzado#Merge Sort|Merge Sort]] | O(n log n) | O(n log n) | O(n log n) | O(n) |
| [[Ordenamiento Avanzado#Quick Sort|Quick Sort]] | O(n log n) | O(n log n) | O(n²) | O(log n) |
| [[Ordenamiento Avanzado#Heap Sort|Heap Sort]] | O(n log n) | O(n log n) | O(n log n) | O(1) |

---

## 7. Técnicas Algorítmicas

| Técnica | Descripción | Ejemplos |
|---------|-------------|----------|
| [[Recursion]] | Funciones que se llaman a sí mismas | Fibonacci, Factorial |
| [[Divide and Conquer]] | Dividir, resolver, combinar | Merge Sort, Binary Search |
| [[Greedy]] | Decisiones locales óptimas | Dijkstra, Kruskal, Huffman |
| [[Programacion Dinamica]] | Subproblemas que se solapan | Knapsack, LCS, Fibonacci |

---

## 8. Implementación en Go (Golang)

### Elementos del Lenguaje

Go es un lenguaje concurrente y compilado con tipado estático. Utiliza **paquetes** para organizar el código.

### Estructuras de Datos en Go

| Estructura | Descripción |
|------------|-------------|
| **Arreglos (Arrays)** | Estructuras estáticas de tamaño fijo y memoria contigua |
| **Slices** | Segmentos dinámicos con puntero, longitud y capacidad |
| **Mapas (Maps)** | Colecciones de parejas llave-valor basadas en hash |
| **Structs** | Tipos de datos definidos por el usuario |

### Punteros y Memoria

- `&x`: Obtiene la dirección de `x`
- `*p`: Desreferencia el puntero `p`

> [!info] Gestión de memoria en Go
> - **Stack**: Memoria automática para variables locales
> - **Heap**: Almacenamiento dinámico con Garbage Collector

---

## 6. Recursos

### PDFs de Referencia

- [[Attachments/02-EstructurasLineales.pdf|Estructuras Lineales]]
- [[Attachments/01-ED_AnalisisAlgoritmos_2026.pdf|ED y Análisis de Algoritmos 2026]]
- [[Attachments/Programacion-estructurada-en-Go-lang.pdf|Programación en Go]]

---

## Conexiones

- [[Analisis de Algoritmos]]
- [[Notacion Big O]]
- [[Tipos Abstractos de Datos (TAD)]]
- [[Pilas (Stack)]]
- [[Colas (Queue)]]
- [[Listas Enlazadas]]
- [[Deques]]
- [[Recursion]]
- [[Arboles Binarios]]
- [[BST (Binary Search Tree)]]
- [[AVL]]
- [[Heap]]
- [[Grafos - Fundamentos]]
- [[Grafos - Recorridos]]
- [[Grafos - Algoritmos]]
- [[Tablas Hash]]
- [[Ordenamiento Basico]]
- [[Ordenamiento Avanzado]]
- [[Divide and Conquer]]
- [[Greedy]]
- [[Programacion Dinamica]]
- [[Implementacion en Go - Estructuras de Datos]]

---

## Fuentes

- Apuntes de clase - Estructura de Datos
