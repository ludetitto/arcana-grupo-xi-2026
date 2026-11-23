---
title: El Consejo de Elrond
tags:
  - greedy
  - desafio
  - resuelto
---

### Dificultad: ★★★☆☆

Rivendel se ha llenado de visitantes. Elfos, enanos, hombres y hasta algún mago han llegado con noticias urgentes y quieren ser escuchados por el Consejo antes de que termine el día. Cada delegación $i$ necesita hablar durante un intervalo $[s_i, f_i)$ específico (algunas deben esperar a que termine cierto rito, otras necesitan la luz del atardecer para desplegar sus mapas) y ese intervalo no se puede interrumpir ni negociar. Elrond preside el Consejo, pero el día tiene las horas que tiene: dos delegaciones no pueden hablar si sus intervalos se superponen, y una vez que una delegación empieza a exponer hay que dejarla terminar. Elrond quiere decidir a cuáles delegaciones va a recibir, de forma de escuchar a la mayor cantidad posible antes de que caiga la noche.

### Enunciado

Dadas $n$ delegaciones, cada una con un intervalo $(s_i, f_i)$ de inicio y fin, se busca el subconjunto más grande de intervalos mutuamente compatibles (sin superposición).

1. Explicar por qué ordenar las delegaciones por su hora de finalización $f_i$ (y no por su hora de inicio $s_i$, ni por la duración $f_i - s_i$ de su exposición) y elegir en forma ávida, en cada paso, la primera delegación compatible con las ya elegidas, garantiza el número máximo de delegaciones atendidas. Construir un ejemplo concreto donde ordenar por duración (las exposiciones más cortas primero) elija menos delegaciones que el óptimo.
2. Diseñar el algoritmo paso a paso.
3. Trazar a mano la ejecución con las siguientes delegaciones:

    |Delegación|Inicio|Fin|
    |---|---|---|
    |Hombres de Gondor|0|6|
    |Istari|8|9|
    |Enanos de Erebor|3|5|
    |Hobbits de la Comarca|5|7|
    |Dúnedain del Norte|6|10|
    |Elfos de Lórien|1|4|
    |Rohirrim|5|9|

    Indicar en qué orden se evalúan las delegaciones y cuáles quedan finalmente aceptadas.
4. Determinar la complejidad del algoritmo en función de $n$, identificando qué paso domina el costo total.

<details>
    <summary>Ver solución</summary>

### 1. Por qué ordenar por fin de intervalo

**Argumento de intercambio (breve):** supongamos que una solución óptima no elige, entre todas las delegaciones, a la que termina primero (llamémosla $D_1$, con fin $f_1$ mínimo). Sea $D_j$ la primera delegación que sí eligió esa solución óptima. Como $f_1 \le f_j$ y $D_1$ no se superpone con ninguna otra elegida (termina antes que cualquier otra que empiece después de $D_j$), podemos reemplazar $D_j$ por $D_1$ sin perder compatibilidad y sin reducir la cantidad de delegaciones aceptadas. Repitiendo este intercambio se llega a que **siempre existe una solución óptima que contiene a la delegación de fin más temprano**. Por eso conviene elegirla primero, descartar todo lo que se superponga con ella, y repetir el mismo razonamiento sobre lo que queda: es un problema idéntico, pero más chico.

**Por qué falla ordenar por duración:** una delegación corta puede "tapar" a dos delegaciones más largas que sí serían compatibles entre sí. Contraejemplo mínimo:

|Delegación|Intervalo|Duración|
|---|---|---|
|P|(0, 4)|4|
|Q|(3, 5)|2|
|R|(4, 8)|4|

Ordenando por duración se elige primero **Q** (duración 2), que se superpone con **P** y con **R**, así que quedan descartadas ambas → solo **1** delegación atendida.  
Ordenando por fin, en cambio, se elige **P** (fin 4) y después **R** (fin 8, compatible porque empieza justo en 4) → **2** delegaciones atendidas, que es el óptimo real.

### 2. Diseño del algoritmo

1. Ordenar las $n$ delegaciones por su hora de **fin**, de menor a mayor.
2. Inicializar `ultimo_fin = -∞` y `aceptadas = []`.
3. Para cada delegación $(s_i, f_i)$ en ese orden:
    - Si $s_i \ge$ `ultimo_fin`: aceptarla (agregar a `aceptadas`) y actualizar `ultimo_fin = f_i`.
    - Si no, descartarla (se superpone con la última aceptada).
4. Devolver `aceptadas`.

### 3. Traza a mano

Datos originales:

|Delegación|Inicio|Fin|
|---|---|---|
|Hombres de Gondor|0|6|
|Istari|8|9|
|Enanos de Erebor|3|5|
|Hobbits de la Comarca|5|7|
|Dúnedain del Norte|6|10|
|Elfos de Lórien|1|4|
|Rohirrim|5|9|

**Paso 1: ordenar por fin** (Istari y Rohirrim empatan en fin = 9; el orden entre ellos no afecta el resultado):

|Orden|Delegación|Inicio|Fin|
|---|---|---|---|
|1|Elfos de Lórien|1|4|
|2|Enanos de Erebor|3|5|
|3|Hombres de Gondor|0|6|
|4|Hobbits de la Comarca|5|7|
|5|Rohirrim|5|9|
|6|Istari|8|9|
|7|Dúnedain del Norte|6|10|

**Paso 2: recorrido ávido** (`ultimo_fin` inicial $= -\infty$):

|Delegación|Inicio|¿Inicio ≥ último fin?|Decisión|`ultimo_fin` tras el paso|
|---|---|---|---|---|
|Elfos de Lórien|1|$1 \ge -\infty$|**Aceptada**|4|
|Enanos de Erebor|3|$3 \ge 4$ → no|Rechazada|4|
|Hombres de Gondor|0|$0 \ge 4$ → no|Rechazada|4|
|Hobbits de la Comarca|5|$5 \ge 4$|**Aceptada**|7|
|Rohirrim|5|$5 \ge 7$ → no|Rechazada|7|
|Istari|8|$8 \ge 7$|**Aceptada**|9|
|Dúnedain del Norte|6|$6 \ge 9$ → no|Rechazada|9|

**Resultado:** Elrond escucha a **Elfos de Lórien (1,4)**, **Hobbits de la Comarca (5,7)** e **Istari (8,9)**: 3 delegaciones, y no existe forma de encajar una cuarta sin superposición.

### 4. Complejidad

- Ordenar las $n$ delegaciones por fin: $O(n \log n)$.
- Recorrer la lista ordenada una sola vez, con trabajo constante por delegación: $O(n)$.

Total: $O(n \log n) + O(n) = O(n \log n)$, dominado por el paso de ordenamiento.

</details>
