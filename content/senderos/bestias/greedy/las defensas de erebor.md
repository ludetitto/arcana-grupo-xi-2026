---
title: Las Defensas de Erebor
tags:
  - greedy
  - desafio
---

### Dificultad: ★★★★★

Los años pasaron y Smaug, ahora del otro lado del problema, necesita defender lo que le queda de tesoro. La Montaña tiene $n$ accesos conocidos (puertas viejas, chimeneas de ventilación, grietas que los enanos abrieron y nunca sellaron del todo) y cada uno es vulnerable solamente durante un intervalo de tiempo determinado: la puerta oeste solo puede forzarse mientras dura cierto relevo, cierta grieta solo es practicable mientras la luna ilumina cierta piedra, y así con cada acceso. Smaug quiere ubicar trampas o centinelas que actúen en un instante puntual (no durante un intervalo, sino en un momento exacto) de forma que cada acceso quede cubierto por al menos una trampa durante su ventana de vulnerabilidad. Colocar cada trampa tiene un costo, así que Smaug quiere usar la menor cantidad posible.

### Enunciado

Dados $n$ accesos, cada uno con un intervalo de vulnerabilidad $[s_i, f_i]$, se busca el menor conjunto de puntos $\{p_1, p_2, \dots\}$ tal que todo intervalo contenga al menos un $p_j$.

Este problema es distinto al de [[el consejo de elrond]]: ahí se buscaba elegir la mayor cantidad de intervalos compatibles entre sí; acá se busca elegir la menor cantidad de puntos que, entre todos, toquen a todos los intervalos dados (un punto ubicado dentro de un intervalo lo cubre).

1. Explicar la estrategia voraz: ordenar los accesos por su instante de finalización $f_i$ de vulnerabilidad, de menor a mayor; colocar una trampa exactamente en el instante de finalización del primer acceso sin cubrir; descartar todos los accesos que esa trampa cubre; y repetir con los accesos restantes. Justificar por qué conviene colocar la trampa en el extremo final del intervalo, y no en cualquier otro punto interior, para maximizar la cantidad de accesos adicionales que esa misma trampa puede llegar a cubrir.
2. Argumentar, aunque sea de forma intuitiva, por qué ninguna estrategia puede cubrir todos los accesos con menos trampas que las que propone el algoritmo voraz (pensar qué pasaría si una solución óptima no incluyera ese primer punto de finalización, ni ningún punto anterior a él, para cubrir al acceso que vence primero).
3. Trazar a mano la ejecución con los siguientes accesos, dados como intervalos de vulnerabilidad (inicio, fin):

    |Acceso|Inicio|Fin|
    |---|---|---|
    |Puerta Oeste|1|4|
    |Chimenea Sur|2|6|
    |Grieta del Cuervo|4|7|
    |Túnel de los Enanos|5|9|
    |Puerta Principal|8|10|
    |Escalinata Oculta|9|12|
    |Ventana de la Guardia|11|13|

    Mostrar en qué orden se procesan los accesos, dónde se coloca cada trampa y qué accesos cubre cada una.
4. Determinar la complejidad del algoritmo en función de $n$, identificando qué paso domina el costo total.
