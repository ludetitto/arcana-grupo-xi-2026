---
title: 'Cubo de Luces - Fuerza Bruta'
tags:
  - cubo-de-luces
  - solucion
---

## Técnicas utilizadas

Búsqueda por [[fuerza bruta]] de n = 0 a n-1, para cada fila y columna, probando todas las combinaciones posibles de pulsaciones de botones.

## Idea de la solución

Cada luz depende solo de cuántas veces se presionaron su botón de fila y su botón de columna, así que el orden de las pulsaciones no afecta el resultado. Además, presionar dos veces el mismo botón deja todo como estaba. Por lo tanto, cualquier secuencia de pulsaciones equivale a otra en la que cada botón se presiona a lo sumo una vez, y esta usa igual o menos pulsaciones. Alcanza entonces con decidir, para cada botón, si se presiona (1) o no (0).

Llamamos $f[i]$ a la decisión sobre la fila i y $c[j]$ a la decisión sobre la columna j. Como hay $n$ botones de fila y $n$ de columna, existen $2^{2n}$ combinaciones. Para cada una, el estado final de la luz $(i, j)$ es:

```
matriz_temporal[i][j] = matriz[i][j] XOR f[i] XOR c[j]
```

Si alguna luz queda apagada, descartamos la combinación. Si todas quedan encendidas, la cantidad de pulsaciones es la cantidad de unos entre $f$ y $c$, y actualizamos el mínimo con ese valor. Si ninguna combinación funciona, devolvemos -1.

Revisar cada combinación cuesta $O(n^2)$, así que el algoritmo es $O(2^{2n} \cdot n^2)$. Solo es viable para $n$ muy chico, lejos de la cota $n\le 1000$.

## Código

```python
def minimo_pulsaciones_botones(n, matriz):
    from itertools import product

    minimo_pulsaciones = float('inf')
    se_encontro_solucion = False

    # Generar todas las combinaciones de pulsaciones para filas y columnas
    for pulsaciones_filas in product([0, 1], repeat=n):
        for pulsaciones_columnas in product([0, 1], repeat=n):
            # Crear una copia de la matriz para simular las pulsaciones
            matriz_temporal = [fila[:] for fila in matriz]

            # Aplicar las pulsaciones de las filas
            for i in range(n):
                if pulsaciones_filas[i] == 1:
                    for j in range(n):
                        matriz_temporal[i][j] ^= 1  # Cambiar el estado de la luz

            # Aplicar las pulsaciones de las columnas
            for j in range(n):
                if pulsaciones_columnas[j] == 1:
                    for i in range(n):
                        matriz_temporal[i][j] ^= 1  # Cambiar el estado de la luz

            # Verificar si todas las luces están encendidas
            if all(matriz_temporal[i][j] == 1 for i in range(n) for j in range(n)):
                se_encontro_solucion = True
                total_pulsaciones = sum(pulsaciones_filas) + sum(pulsaciones_columnas)
                minimo_pulsaciones = min(minimo_pulsaciones, total_pulsaciones)

    return minimo_pulsaciones if se_encontro_solucion else -1
```

## Traza de ejemplo

Para la siguiente matriz inicial:

```
0 1 1 0
1 0 0 1
1 0 0 1
0 1 1 0
```


El algoritmo encuentra dos soluciones que requieren el mínimo de pulsaciones:

| Filas pulsadas | Columnas pulsadas | Pulsaciones totales | Resultado |
|---|---|---:|---|
| 0110 | 1001 | 4 | Todas las luces quedan encendidas |
| 1001 | 0110 | 4 | Todas las luces quedan encendidas |

Primera solución
Primero se pulsan las filas 2 y 3:

```
0 1 1 0
0 1 1 0
0 1 1 0
0 1 1 0
```


Luego se pulsan las columnas 1 y 4:

```
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```


Segunda solución
Primero se pulsan las filas 1 y 4:

```
1 0 0 1
1 0 0 1
1 0 0 1
1 0 0 1
```


Luego se pulsan las columnas 2 y 3:

```
1 1 1 1
1 1 1 1
1 1 1 1
1 1 1 1
```

**Candidato 151.** Al invertir las filas 1 y 4, las cuatro filas quedan como 1001. Al invertir las columnas 2 y 3, todo queda en 1. También usa 4 pulsaciones, así que no mejora el mínimo y este se mantiene.

Ningún otro candidato enciende todas las luces. Después de evaluar los 256, el algoritmo devuelve 4, que coincide con la salida del ejemplo.

## Complejidad

### Temporal
$O(2^{2n} \cdot n^2)$

Hay $2^n$ combinaciones de filas y $2^n$ de columnas, así que se evalúan $2^n \cdot 2^n = 2^{2n}$ candidatos. Para cada candidato:

- Armar $f$ y $c$ a partir de los bits cuesta $O(n)$.
- Revisar el estado final de las $n^2$ luces con `matriz[i][j] XOR f[i] XOR c[j]` cuesta $O(1)$ por luz, o sea $O(n^2)$.
- Contar las pulsaciones cuesta $O(n)$.

El costo por candidato es $O(n^2)$, y en total queda $O(2^{2n} \cdot n^2)$. Descartar un candidato en cuanto aparece una luz apagada puede acelerar el caso promedio, pero no mejora el peor caso: un candidato puede fallar recién en la última luz, y si no hay solución se revisan todos.


### Espacial
$O(n^2)$

- El tablero de entrada ocupa $O(n^2)$.
- Los arreglos $f$ y $c$ ocupan $O(n)$, y se reutilizan en cada candidato.
- El mínimo y los contadores ocupan $O(1)$.

Si el estado final se calcula con la fórmula, no hace falta copiar el tablero y el espacio adicional es $O(n)$. Si cada candidato se simula sobre una copia del tablero, el espacio adicional sube a $O(n^2)$. En ambos casos el total es $O(n^2)$, dominado por la entrada.

## Cuándo usar esta técnica

### Contexto favorable

- **Cuando $n$ es chico.** Por ejemplo, con $n = 10$ hay $1.048.576$ de combinaciones (ya que es $2^{20}$) y cada una revisa 100 luces, lo que da unos 100 millones de operaciones.
- **Cuando se busca la solución más simple de escribir y de probar.** No hay que decidir un orden de los botones ni diseñar podas. Se generan todas las combinaciones y se verifica cada una con la misma fórmula.
- **Para verificar otra solución.** Como revisa todas las combinaciones, sirve como referencia: se generan tableros aleatorios chicos y se comparan sus respuestas.

### Limitaciones

- **El tiempo crece exponencialmente.** Cada vez que $n$ aumenta en 1, la cantidad de candidatos se multiplica por 4, debido a que $2^{2n}=(2^{2})^{n}=4^{n}$. Con $n = 12$ ya son unos $2.415.919.104$ pasos, y con la cota del enunciado, $n\le 1000$, lo que resulta intratable.
- **No aprovecha información parcial.** Aunque las primeras decisiones ya dejen una luz apagada sin arreglo posible, la fuerza bruta igual completa y revisa todas las combinaciones que empiezan así. En el ejemplo, de 256 candidatos solo 2 son solución.

### Comparación con backtracking

Las dos técnicas recorren el mismo espacio de decisiones: cada botón se presiona o no. La diferencia es cuándo verifican. La fuerza bruta arma la combinación completa y recién después la revisa. El backtracking decide los botones de a uno y verifica sobre la marcha. En cuanto una luz con su fila y su columna ya decididas queda apagada, descarta toda la rama sin completarla. También puede descartar una rama cuando las pulsaciones acumuladas ya no pueden mejorar el mínimo.

| | Fuerza bruta | Backtracking |
|---|---|---|
| Cuándo verifica | Al final, con la combinación completa | Durante la construcción, en cada decisión |
| Candidatos explorados | Siempre $2^{2n}$ | Muchos menos, porque las podas cortan ramas enteras |
| Tiempo en el peor caso | $O(2^{2n} \cdot n^2)$ | Exponencial en teoría; en la práctica depende del orden y de las podas |
| Espacio adicional | $O(n)$ | $O(n)$ para $f$, $c$ y la pila de recursión |
| Dificultad de implementación | Baja | Media: hay que elegir el orden de los botones y las podas |

El orden en que se deciden los botones importa mucho para el backtracking. Por ejemplo, si se decide primero $f[0]$ y después las columnas, cada $c[j]$ queda forzado por la luz $(0, j)$: una de las dos opciones se poda de inmediato. Lo mismo pasa después con cada fila por la luz $(i, 0)$. Así sobreviven muy pocas ramas, mientras que la fuerza bruta recorre siempre las $2^{2n}$ combinaciones.

En resumen, la fuerza bruta conviene para $n$ chico o como referencia para verificar. Para valores de $n$ más grandes, el backtracking es la mejor opción de las dos, porque evita explorar combinaciones que ya se sabe que fallan.

## Referencias
- N/A
