---
title: Tablas Hash
type: zettel
created: 2026-04-03
tags: [estructura-datos,hash,tabla-hash,diccionario]
related: [Estructuras de Datos y Análisis de Algoritmos, BST (Binary Search Tree), Programacion Dinamica]
---

# Tablas Hash

## Definición

Una **Tabla Hash** (Hash Table) es una estructura de datos que implementa un **mapeo de llave-valor** usando una función hash para convertir llaves en índices de un array. Permite operaciones de búsqueda, inserción y eliminación en tiempo promedio **O(1)**.

> [!tip] Analogía
> Imagina un archivador con cajones etiquetados (A-Z). Para guardar una carpeta, usas la primera letra del nombre para elegir el cajón. La función hash es "primera letra".

## Conceptos Clave

### Función Hash

Convierte una llave en un índice del array.

```
funcionHash("key") → índice del array

Propiedades deseables:
- Determinista (misma llave → mismo índice)
- Distribución uniforme
- Rápida de calcular
```

### Resolución de Colisiones

Cuando dos llaves diferentes generan el mismo índice.

## Métodos de Resolución de Colisiones

### 1. Linear Probing (Sondeo Lineal)

Si hay colisión, buscar el siguiente espacio libre secuencialmente.

```
Insertar: clave27, hash("clave27") = 5 (ya ocupado)
→ Intentar índice 6 (libre) → guardar ahí

Búsqueda: buscar índice hash, si no está, seguir buscando linealmente
```

```go
package main

import "fmt"

// Linear Probing Hash Table
type HashTableLP struct {
    table   []interface{}
    size    int
    used    int
}

func NewHashTableLP(size int) *HashTableLP {
    return &HashTableLP{
        table: make([]interface{}, size),
        size:  size,
        used:  0,
    }
}

func (ht *HashTableLP) hash(key string) int {
    hash := 0
    for _, c := range key {
        hash = (hash*31 + int(c)) % ht.size
    }
    return hash
}

func (ht *HashTableLP) Insert(key string, value interface{}) bool {
    if float64(ht.used)/float64(ht.size) > 0.7 {
        return false // Load factor > 0.7
    }
    
    index := ht.hash(key)
    original := index
    
    for ht.table[index] != nil {
        index = (index + 1) % ht.size
        if index == original {
            return false // Tabla llena
        }
    }
    
    ht.table[index] = value
    ht.used++
    return true
}

func (ht *HashTableLP) Get(key string) interface{} {
    index := ht.hash(key)
    original := index
    
    for ht.table[index] != nil {
        index = (index + 1) % ht.size
        if index == original {
            return nil
        }
    }
    
    return nil
}
```

### 2. Separate Chaining (Encadenamiento)

Cada índice del array contiene una lista enlazada (o lista) de elementos.

```
Índice 0: [key1 → key5]
Índice 1: [key2]
Índice 2: [key3 → key4]
```

```go
// Separate Chaining Hash Table
type HashTableSC struct {
    buckets [][]interface{}
    size    int
    used    int
}

func NewHashTableSC(size int) *HashTableSC {
    buckets := make([][]interface{}, size)
    for i := range buckets {
        buckets[i] = []interface{}{}
    }
    return &HashTableSC{
        buckets: buckets,
        size:    size,
        used:    0,
    }
}

func (ht *HashTableSC) hash(key string) int {
    hash := 0
    for _, c := range key {
        hash = (hash*31 + int(c)) % ht.size
    }
    return hash
}

func (ht *HashTableSC) Insert(key string, value interface{}) {
    index := ht.hash(key)
    ht.buckets[index] = append(ht.buckets[index], value)
    ht.used++
}

func (ht *HashTableSC) Get(key string) interface{} {
    index := ht.hash(key)
    return ht.buckets[index]
}

func (ht *HashTableSC) Len() int {
    return ht.used
}

func (ht *HashTableSC) LoadFactor() float64 {
    return float64(ht.used) / float64(ht.size)
}
```

### 3. Double Hashing

Usar una segunda función hash cuando hay colisión.

```
h2(key) = PRIME - (key mod PRIME)

Índice final = (h1(key) + i * h2(key)) mod size
```

## Implementación Profesional en Go

```go
package main

import (
    "fmt"
)

// Pair representa un par key-value
type Pair struct {
    Key   string
    Value interface{}
}

// HashTable con Separate Chaining
type HashTable struct {
    buckets [][]Pair
    size    int
    used    int
}

func NewHashTable(size int) *HashTable {
    if size < 16 {
        size = 16
    }
    buckets := make([][]Pair, size)
    for i := range buckets {
        buckets[i] = []Pair{}
    }
    return &HashTable{
        buckets: buckets,
        size:    size,
        used:    0,
    }
}

func (ht *HashTable) hash(key string) int {
    hash := 0
    for i, c := range key {
        hash = (hash*31 + int(c)) * 31^(len(key)-i-1)
    }
    return abs(hash) % ht.size
}

func abs(n int) int {
    if n < 0 {
        return -n
    }
    return n
}

// Insert o Update
func (ht *HashTable) Put(key string, value interface{}) {
    index := ht.hash(key)
    bucket := ht.buckets[index]
    
    // Buscar si ya existe la key
    for i := range bucket {
        if bucket[i].Key == key {
            bucket[i].Value = value
            return
        }
    }
    
    // Insertar nuevo par
    ht.buckets[index] = append(bucket, Pair{Key: key, Value: value})
    ht.used++
    
    // Rehash si load factor > 0.75
    if float64(ht.used)/float64(ht.size) > 0.75 {
        ht.rehash()
    }
}

// Get
func (ht *HashTable) Get(key string) (interface{}, bool) {
    index := ht.hash(key)
    bucket := ht.buckets[index]
    
    for _, pair := range bucket {
        if pair.Key == key {
            return pair.Value, true
        }
    }
    
    return nil, false
}

// Delete
func (ht *HashTable) Delete(key string) bool {
    index := ht.hash(key)
    bucket := ht.buckets[index]
    
    for i, pair := range bucket {
        if pair.Key == key {
            ht.buckets[index] = append(bucket[:i], bucket[i+1:]...)
            ht.used--
            return true
        }
    }
    
    return false
}

// Contains
func (ht *HashTable) Contains(key string) bool {
    _, found := ht.Get(key)
    return found
}

// Keys
func (ht *HashTable) Keys() []string {
    keys := make([]string, 0, ht.used)
    
    for _, bucket := range ht.buckets {
        for _, pair := range bucket {
            keys = append(keys, pair.Key)
        }
    }
    
    return keys
}

// Rehash - redimensionar tabla
func (ht *HashTable) rehash() {
    oldBuckets := ht.buckets
    newSize := ht.size * 2
    
    ht.buckets = make([][]Pair, newSize)
    for i := range ht.buckets {
        ht.buckets[i] = []Pair{}
    }
    ht.size = newSize
    ht.used = 0
    
    // Reinsertar todos los elementos
    for _, bucket := range oldBuckets {
        for _, pair := range bucket {
            ht.Put(pair.Key, pair.Value)
        }
    }
}
```

## Funciones Hash Comunes

### hash de strings

```go
// djb2
func hashDJB2(s string) int {
    hash := 5381
    for _, c := range s {
        hash = ((hash << 5) + hash) + int(c) // hash * 33 + c
    }
    return hash
}

// FNV-1a
func hashFNV(s string) int {
    hash := 2166136261
    for _, c := range s {
        hash ^= int(c)
        hash *= 16777619
    }
    return hash
}
```

## Análisis de Complejidad

| Operación | Promedio | Peor Caso |
|-----------|----------|-----------|
| **Insert** | O(1) | O(n) |
| **Get** | O(1) | O(n) |
| **Delete** | O(1) | O(n) |
| **Search** | O(1) | O(n) |

> [!warning] Peor caso
> Ocurre cuando todas las keys tienen el mismo hash (colisiones extremas). Para evitar esto: buena función hash y mantener load factor bajo.

## Load Factor

```
Load Factor = n / k

donde:
- n = número de elementos
- k = número de buckets

Load factor óptimo: 0.6 - 0.75
- Si > 0.75: hacer rehash
- Si < 0.25: reducir tamaño
```

## Aplicaciones

| Aplicación | Uso |
|------------|-----|
| **Diccionarios** | Almacenamiento key-value |
| **Sets** | Verificar pertenencia rápida |
| **Cachés** | Almacenamiento temporal |
| **Indices de bases de datos** | Búsqueda rápida |
| **Comprobación de duplicados** | Deduplicación |
| **Memcached/Redis** | Almacenamiento en memoria |

## Ejemplo Completo

```go
func main() {
    ht := NewHashTable(16)
    
    // Insertar
    ht.Put("nombre", "Juan")
    ht.Put("edad", 30)
    ht.Put("ciudad", "Madrid")
    
    // Buscar
    nombre, found := ht.Get("nombre")
    fmt.Printf("nombre: %v, encontrado: %v\n", nombre, found)
    
    // Actualizar
    ht.Put("edad", 31)
    edad, _ := ht.Get("edad")
    fmt.Printf("edad actualizada: %v\n", edad)
    
    // Eliminar
    ht.Delete("ciudad")
    
    // Keys
    fmt.Println("Keys:", ht.Keys())
    
    // Load factor
    fmt.Printf("Load factor: %.2f\n", ht.LoadFactor())
}
```

## Hash Table vs BST

| Operación | Hash Table | BST |
|-----------|-----------|-----|
| **Búsqueda** | O(1) promedio | O(log n) |
| **Inserción** | O(1) promedio | O(log n) |
| **Ordenamiento** | No garantiza | In-order = ordenado |
| **Memoria** | Más | Menos |
| **Peor caso** | O(n) | O(n) |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[BST (Binary Search Tree)]]
- [[Programacion Dinamica]]
