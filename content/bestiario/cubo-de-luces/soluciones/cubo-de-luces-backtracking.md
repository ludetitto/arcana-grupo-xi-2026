---
title: 'Cubo de Luces - Backtracking'
tags:
  - cubo-de-luces
  - solucion
---

## Técnicas utilizadas
Búsqueda por [[fuerza bruta]] con fórmula cerrada (cuadrática): en lugar de sumar los números uno a uno, se usa la [[fórmula de Gauss]] para calcular sumas de series consecutivas en $O(1)$. Esto permite evaluar cada candidato `(h, N)` de forma directa y eficiente.

## Idea de la solución
La suma de los números a la izquierda de h es la suma de 1 hasta h−1, y la suma a la derecha es la suma de h+1 hasta N. Ambas se calculan con la fórmula de Gauss:

Suma izquierda:
$$
gauss(h−1) = (h−1) \times h / 2
$$
Suma derecha:
$$
gauss(N) − gauss(h) = N \times (N+1)/2 − h \times (h+1)/2
$$

El algoritmo itera sobre todos los valores posibles de `N` (el tamaño de la calle), y para cada `N` prueba todas las posiciones h desde 1 hasta N. Si ambas sumas coinciden, se registra el par como solución.

## Código

```python
def gauss(n):
    return n * (n + 1) // 2

soluciones = []
N = 1
while len(soluciones) < 10:
    for h in range(1, N + 1):
        suma_izq = gauss(h - 1)
        suma_der = gauss(N) - gauss(h)
        if suma_izq == suma_der:
            soluciones.append((h, N))
    N += 1

for h, N in soluciones:
    print(f"{h:10}{N:10}")
```

## Traza de ejemplo

Buscamos la primera solución, con N creciente:

|  N  |  h  | Suma izq | Suma der |          ¿Igual?         |
|-----|-----|----------|----------|--------------------------|
|   1 |   1 |        0 |        0 | ✓ (trivial, se descarta) |
| ... | ... |      ... |      ... |                          |
|  8  |  1  |        0 |       35 |                        ✗ |
|  8  |  2  |        1 |       33 |                        ✗ |
|  8  |  3  |        3 |       30 |                        ✗ |
|  8  |  4  |        6 |       26 |                        ✗ |
|  8  |  5  |       10 |       21 |                        ✗ |
|  8  |  6  |       15 |       15 |                        ✓ |

Primera solución encontrada: (6, 8).
> Nota: el caso h=1 con N=1 da suma 0 en ambos lados, pero es trivial. Si se quiere excluir, alcanza con pedir h > 1 o N > 1.


## Complejidad

### Temporal
$O(N_{max}^2)$ donde $N_{max}$ es el valor de la última calle encontrada. Para cada N se prueban hasta N valores de h, y cada evaluación es $O(1)$ gracias a Gauss. $N_{max}$ no es un parámetro controlable: está determinado por la décima solución, que es grande debido al crecimiento exponencial de las soluciones. En la práctica el programa termina rápido porque solo se piden 10 soluciones, pero el algoritmo no escala bien si se piden muchas más.


### Espacial
$O(1)$ auxiliar (más $O(10)$ para guardar los resultados), ya que no se almacena ninguna estructura proporcional a N.

## Cuándo usar esta técnica

### Favorable cuando
- Se buscan pocas soluciones entre muchos candidatos.
- Existe una fórmula cerrada que evita iterar elemento a elemento.
- El espacio de búsqueda es pequeño o las soluciones aparecen pronto.

### Limitaciones
- No escala bien si se piden muchas soluciones: los valores de h y N crecen rápidamente (siguiendo una recurrencia ligada a [[ecuaciones de Pell]]), y el bucle externo debe avanzar hasta N muy grandes.
- Comparado con una solución basada en la recurrencia matemática, esta versión hace trabajo redundante: evalúa muchos pares (h, N) que no son solución.

## Comparación con la solución recursiva
Una solución que explota la ecuación de Pell genera cada par directamente a partir del anterior en $O(1)$ por solución, con complejidad total $O(k)$ para k soluciones. La fuerza bruta cuadrática es más fácil de entender y verificar, pero menos eficiente para k grande.

## Referencias
- N/A
