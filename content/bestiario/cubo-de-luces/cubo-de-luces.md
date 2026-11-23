---
title: 'Cubo de Luces'
tags:
  - cubo-de-luces
  - bestia
  - fuerza-bruta
  - backtracking
---

## Nombre y enunciado

Agustina tiene un tablero de n × n dónde se cumple que $1\le n\le 1000$, formado por $n^{2}$ cuadraditos unitarios idénticos. Cada cuadradito unitario tiene una luz que puede estar encendida o apagada.
Se tiene además, por cada rectángulo de 1 × n del tablero (cada fila o columna) un botón que cambia de estado esas n luces del rectángulo (las apagadas se encienden, y las encendidas se apagan). Dada una configuración inicial, tu tarea consiste en indicarle a Agustina la mínima cantidad de pulsaciones de botones que deja todas las luces encendidas. Si no es posible lograrlo, debes indicarlo con − 1.

**Problema**: Hallar la mínima cantidad de pulsaciones de botones que deja todas las luces encendidas. Si no es posible lograrlo, debes indicarlo con − 1.

[Problema original](https://www.oia.unsam.edu.ar/wp-content/uploads/2021/03/cubo.pdf)

---

## Intuición
A primera vista, pareciera que el problema se resuelve apretando los botones de aquellas filas o columnas con luces apagadas. Sin embargo, esto no siempre es así, ya que una elección incorrecta puede hacer que se enciendan luces que ya estaban encendidas. Por lo tanto, es necesario analizar cuidadosamente la configuración inicial y determinar la secuencia óptima de pulsaciones de botones para lograr el objetivo.

---

## Definición formal

**Entrada:** Un valor n y una matriz de n × n con valores 0 o 1, donde 0 representa una luz apagada y 1 representa una luz encendida.

**Salida:** Un valor entero que representa la mínima cantidad de pulsaciones de botones necesarias para encender todas las luces. Si no es posible lograrlo, se debe retornar −1.

**Restricciones**: n es un número entero tal que $1\le n\le 1000$.

---

## Ejemplo concreto

Si tomamos n = 4 y la siguiente configuración inicial:

```
0 1 1 0
1 0 0 1
1 0 0 1
0 1 1 0
```

Para este caso, la mínima cantidad de pulsaciones de botones necesarias para encender todas las luces es 4. Una posible secuencia de pulsaciones sería:

1. Pulsar el botón de la primera fila.

```
1 0 0 1
1 0 0 1
1 0 0 1
0 1 1 0
```

2. Pulsar el botón de la última fila.

```
1 0 0 1
1 0 0 1
1 0 0 1
1 0 0 1
```

3. Pulsar el botón de la segunda columna.

```
1 1 0 1
1 1 0 1
1 1 0 1
1 1 0 1
```
4. Pulsar el botón de la tercer columna.

```
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```

---

## Por dónde empezar

Una primera aproximación razonable es utilizar fuerza bruta para probar todas las combinaciones posibles de pulsaciones de botones. Esto implica iterar sobre todas las filas y columnas, aplicando las pulsaciones y verificando si se logra encender todas las luces. Sin embargo, dado que n puede ser tan grande como 1000, esta estrategia puede no ser eficiente.

Dada la naturaleza del problema, una mejor estrategia podría ser utilizar backtracking para explorar las posibles combinaciones de pulsaciones de botones de manera más eficiente. Esto permitiría descartar rápidamente aquellas combinaciones que no conducen a una solución válida, reduciendo así el espacio de búsqueda.

---

## Soluciones disponibles

- [[cubo-de-luces-fuerza-bruta]]
- [[cubo-de-luces-backtracking]]