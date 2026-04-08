---
title: Análisis de Algoritmos
type: zettel
created: 2026-04-02
tags: [algoritmos, analisis, complejidad]
related: [Notacion Big O, Tipos Abstractos de Datos (TAD)]
---

# Análisis de Algoritmos

## Definición

El **análisis de algoritmos** es el proceso de determinar matemáticamente los recursos necesarios (tiempo y espacio) en función del tamaño de la entrada, sin necesidad de implementar el algoritmo.

## Enfoques de Análisis

### Análisis Empírico (A posteriori)

Consiste en programar el algoritmo y probarlo en una computadora midiendo tiempos de ejecución reales.

| Ventajas                  | Limitaciones             |
| ------------------------- | ------------------------ |
| Resultados reales         | Dependiente del hardware |
| Incluye optimizaciones del compilador | Dependiente del lenguaje |
| Resultados reproducibles  | Dependiente del entorno  |

### Análisis Teórico (A priori)

Determina matemáticamente los recursos necesarios. Es independiente del hardware y lenguaje.

> [!tip] Importancia del análisis teórico
> Permite predecir el comportamiento del algoritmo antes de implementarlo, facilitando la elección del algoritmo más adecuado.

## Complejidad Temporal

La complejidad temporal se expresa como el **número de pasos** en función del tamaño del problema (n).

### ¿Qué es un "paso"?

Un paso es un segmento de código cuyo tiempo de ejecución no depende del tamaño del problema:
- Operaciones aritméticas
- Asignaciones
- Accesos a memoria
- Comparaciones

## Complejidad Espacial

Mide la **memoria** que requiere el algoritmo:

| Tipo | Descripción |
|------|-------------|
| **Espacio estático** | Variables globales y constantes |
| **Espacio de la pila** | Llamadas a funciones |
| **Espacio dinámico** | Memoria asignada durante ejecución |

## Referencias

- [[Estructuras de Datos y Análisis de Algoritmos]]
- [[Notacion Big O]]
