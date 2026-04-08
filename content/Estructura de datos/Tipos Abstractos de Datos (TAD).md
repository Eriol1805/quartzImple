---
title: Tipos Abstractos de Datos (TAD)
type: zettel
created: 2026-04-02
tags: [estructura-datos, tad, abstraccion, programacion]
related: [Pilas (Stack), Colas (Queue), Listas Enlazadas]
---

# Tipos Abstractos de Datos (TAD)

## Concepto

Un **TAD** (Tipo Abstracto de Datos) es un modelo matemático que define:
- Un conjunto de datos
- Las operaciones permitidas sobre esos datos
- Sin especificar la implementación interna

> [!note] Beneficio principal
> La abstracción permite cambiar la implementación interna sin afectar el código que usa el TAD.

## Componentes de un TAD

| Componente | Descripción |
|------------|-------------|
| **Conjunto de datos** | Qué elementos almacena (ej: enteros, strings) |
| **Operaciones** | Qué se puede hacer (Sintaxis - firma de funciones) |
| **Comportamiento** | Efecto de las operaciones (Semántica - axiomas) |

## Ejemplo: TAD Pila

### Especificación

```
TAD Pila<T>
    operaciones:
        apilar(p: Pila, e: T): Pila
        desapilar(p: Pila): Pila
        tope(p: Pila): T
        vazia(p: Pila): bool
        tamanho(p: Pila): int
    
    axiomas:
        vazia(PilaNueva()) = true
        vazia(apilar(p, e)) = false
        tamanho(PilaNueva()) = 0
        tamanho(apilar(p, e)) = tamanho(p) + 1
        tope(apilar(p, e)) = e
        desapilar(apilar(p, e)) = p
```

## Abstracción de Datos

La **abstracción de datos** es una técnica que permite diseñar estructuras:
- **Resaltando detalles relevantes**: Interfaz pública, operaciones útiles
- **Ocultando detalles irrelevantes**: Implementación interna, gestión de memoria

```
┌─────────────────────────────────────┐
│         INTERFAZ PÚBLICA            │
│   (Operaciones del TAD)             │
├─────────────────────────────────────┤
│         IMPLEMENTACIÓN PRIVADA      │
│   (Detalles internos)               │
└─────────────────────────────────────┘
```

## TADs Fundamentales

| TAD | Descripción | Principio |
|-----|-------------|-----------|
| [[Pilas (Stack)]] | Colección LIFO | Last In, First Out |
| [[Colas (Queue)]] | Colección FIFO | First In, First Out |
| [[Listas Enlazadas]] | Secuencia de nodos | Acceso secuencial |
| [[Deques]] | Cola de dos extremos | Insert/Delete por ambos lados |

## Implementaciones Comunes

| Estructura | Uso de Memoria | Acceso |
|------------|-----------------|--------|
| **Array** | Contiguo | O(1) por índice |
| **Lista enlazada** | Disperso | O(n) secuencial |
| **Árbol** | Dinámico | O(log n) búsqueda |
| **Tabla hash** | Dinámico | O(1) promedio |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Pilas (Stack)]]
- [[Colas (Queue)]]
