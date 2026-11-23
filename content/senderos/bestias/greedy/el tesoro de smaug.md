---
title: El Tesoro de Smaug
tags:
  - greedy
  - desafio
---

### Dificultad: ★☆☆☆☆

Tras siglos dormido sobre su tesoro, Smaug debe abandonar temporalmente su guarida en la Montaña Solitaria: los enanos han comenzado a horadar un nuevo túnel y el dragón, prudente a su manera, prefiere trasladar lo que pueda antes de que lo descubran. El problema es que ni siquiera un dragón puede cargar todo el oro de Erebor de una sola vez: sus garras y su lomo soportan, como mucho, un peso máximo $W$. Ante Smaug hay $n$ sacos de tesoro, cada uno con su propio peso $w_i$ y su propio valor $v_i$ (las esmeraldas pesan poco y valen mucho, el oro en bruto pesa como el oro en bruto). A diferencia de un ladrón cualquiera, Smaug puede desgarrar un saco con sus garras y llevarse solo una fracción de su contenido, si eso le permite aprovechar mejor el espacio que le queda.

### Enunciado

El problema es la variante fraccionaria de la mochila: dada una capacidad $W$ y $n$ sacos, cada uno con peso $w_i$ y valor $v_i$, se puede tomar cualquier fracción $x_i \in [0, 1]$ de cada saco, no solo el saco entero, maximizando $\sum_i x_i v_i$ sujeto a $\sum_i x_i w_i \le W$.

1. Explicar por qué ordenar los sacos por su razón valor/peso $\frac{v_i}{w_i}$, de mayor a menor, y tomarlos en ese orden hasta completar la capacidad $W$, produce siempre una solución óptima en la variante fraccionaria. Justificar con un argumento de intercambio: ¿qué pasaría si Smaug tomara primero una unidad de peso de un saco con menor razón valor/peso, existiendo todavía unidades disponibles de un saco con mayor razón?
2. Diseñar el algoritmo paso a paso, indicando qué ocurre cuando el peso restante de la capacidad es menor que el peso completo del siguiente saco en el orden.
3. Trazar a mano la ejecución con capacidad $W = 50$ y los siguientes sacos:

    |Saco|Peso ($w_i$)|Valor ($v_i$)|
    |---|---|---|
    |Esmeraldas|10|60|
    |Oro amonedado|20|100|
    |Oro en bruto|30|120|
    |Gemas talladas|15|90|

    Mostrar el orden elegido, qué fracción de cada saco toma Smaug y el valor total transportado.
4. Determinar la complejidad temporal del algoritmo en función de $n$, identificando qué paso domina el costo total.
