---
title: B-Tree
tags:
  - data-structures
alias:
  - árbol B
  - arbol-b
---
## 1. Qué es y cómo funciona

### Intuición
- Idea central: ¿Cuál es la idea simple detrás de esta estructura?
- Problema que resuelve: ¿Qué tipo de problema hace sencillo o eficiente?

### Definición / propiedades
- Definición formal: invariantes y reglas que siempre se cumplen.
- Propiedades clave: orden, acotamiento, restricciones sobre elementos, estabilidad, etc.

### Representación
- Descripción de la organización interna (arrays, nodos enlazados, árboles, tablas, etc.).
- Ilustración sugerida: incluye aquí un diagrama ASCII o referencia a una imagen en `attachments/`.

Debe responder a: "¿qué estoy mirando?"

## 2. Operaciones y complejidad

### Operaciones principales

- `find`: Comienza en la raíz. Compara la clave buscada con las claves del nodo actual. Si hay coincidencia, retorna el valor. Si no, desciende al hijo cuyo rango contenga la clave buscada.
- `insert`: Añade una nueva clave en el nivel de las hojas. Para evitar problemas de balanceo ascendente, a medida que desciende por el árbol buscando dónde insertar, divide preventivamente ("split") cualquier nodo que esté completamente lleno.
- `delete`: Remueve una clave. Si la clave está en un nodo interno, se reemplaza por su predecesor o sucesor lógico. Si la eliminación deja a un nodo con menos claves del mínimo permitido, se rebalancea pidiendo una clave prestada a un hermano adyacente o fusionándose ("merge") con él. 
- `traverse`: Visita todas las claves del árbol de forma secuencial y ordenada (in-order). Recorre recursivamente los hijos izquierdos, luego las claves del nodo, y finalmente los hijos derechos.

### Complejidad

| Operación | Tiempo Promedio | Tiempo Peor Caso | Espacio Adicional |
| :--- | :--- | :--- | :--- |
| `find` | $O(\log n)$ | $O(\log n)$ | $O(1)$ |
| `insert` | $O(\log n)$ | $O(\log n)$ | $O(1)$ |
| `delete` | $O(\log n)$ | $O(\log n)$ | $O(1)$ |
| `traverse` | $O(n)$ | $O(n)$ | $O(\log n)$ (por la pila de llamadas) |

### Costos ocultos
*   **Operaciones de E/S (Disco):** La métrica de rendimiento real de un árbol B no es solo el uso de CPU, sino cuántos bloques de disco debe leer. Gracias a que cada nodo coincide con el tamaño de una página de disco (por ejemplo, 4KB u 8KB), la altura del árbol es muy baja, minimizando los accesos físicos.
*   **Gestión de memoria (Splits/Merges):** Insertar o eliminar elementos puede desencadenar una cascada de divisiones (splits) o fusiones (merges) de nodos que sube hasta la raíz. Esto implica reasignación de memoria (`reallocs`) y copiado masivo de arrays de claves y punteros dentro del nodo.
*   **Búsqueda intra-nodo:** Una vez que se carga un nodo en memoria, el algoritmo debe encontrar la clave correcta dentro de ese nodo (que puede tener cientos de claves). Esto agrega un costo de $O(\log m)$ por nivel si se usa búsqueda binaria internamente, aunque se considera una constante amortizada frente al costo de disco.

### Detalles operativos
### Casos especiales
*   **Estructura vacía:** Un `find` retorna un fallo de inmediato. Un `insert` crea el nodo raíz (que en este punto es también la única hoja) y coloca la clave allí.
*   **Estructura llena (Límites de tamaño):** Un nodo está "lleno" cuando alcanza (m-1) claves, siendo m el orden del árbol. Si el nodo raíz se llena y se necesita hacer un `split`, la raíz se divide en dos y se crea una nueva raíz por encima de ellas. Es el único momento en el que el árbol B aumenta su altura.
*   **Duplicados:** El árbol B clásico requiere claves únicas. Si se necesitan almacenar duplicados, el algoritmo se modifica para que cada clave apunte a una lista/array de valores, o se anexa un identificador único oculto a cada clave para forzar la unicidad.
*   **Orden:** A diferencia de una tabla hash, el árbol B mantiene las claves estrictamente ordenadas. Esto lo hace ideal para consultas de rangos (ej. `SELECT * FROM tabla WHERE edad BETWEEN 20 AND 30`).

### Comportamiento en concurrencia y fallos
*   **Concurrencia:** Permitir que múltiples hilos lean y escriban simultáneamente en un árbol B es muy complejo, ya que un `split` o `merge` altera la estructura de los punteros, invalidando los caminos de lectura de otros hilos. Se utilizan protocolos estrictos de *latching* (bloqueos temporales) conocidos como "hand-over-hand locking" o variantes estructurales como el **B-link tree** (que añade punteros laterales entre nodos hermanos para mitigar cuellos de botella en la raíz).
*   **Tolerancia a fallos:** Si un sistema colapsa en medio de un `split` de un nodo, el árbol quedará corrupto y los datos inaccesibles. Por esto, en los motores de bases de datos, cualquier modificación al árbol B se escribe primero de forma secuencial en un registro de transacciones (Write-Ahead Log) antes de tocar el árbol físico.

## 3. Implementación

### Idea de implementación
- Descripción de la(s) estrategia(s) típica(s) para implementar la estructura.
- Algoritmos clave y pasos principales.

### Invariantes
- Lista de comprobaciones e invariantes que el código debe garantizar siempre (por ejemplo: punteros no nulos, tamaño consistente, heap property, ordenamiento mantenido).

### Ejemplo de código
- Proporciona 1-2 snippets claros y mínimos (en Python).
- Ejemplo de uso típico con entrada y salida esperada.

Debe responder a: "¿cómo lo programo sin romperlo?"

## 4. Uso y criterio

### Casos de uso
- Situaciones y problemas donde la estructura encaja naturalmente.

### Cuándo NO usarlo
- Escenarios donde su uso es contraproducente o subóptimo.

### Comparaciones
- Alternativas comunes y cuándo elegir cada una (lista comparativa breve).

### Ventajas / desventajas
- Trade-offs prácticos en rendimiento, memoria, simplicidad, y facilidad de implementación.

### Señales de reconocimiento
- Pistas en el enunciado de un problema que indican que esta estructura es adecuada.

Debe responder a: "¿cuándo conviene usarlo?"

## 5. Relaciones y extensiones

### Variantes
- Variantes y mejoras (por ejemplo: versiones balanceadas, persistentes, acotadas, indexadas, con hashing, etc.).

### Relación con otras estructuras
- Dependencias conceptuales y cómo se combina con otras estructuras.

### Notas avanzadas
- Temas avanzados como persistencia, concurrencia, paralelismo, ordenamientos aleatorios, caching, tuning de parámetros.

Debe responder a: "¿cómo encaja en el mapa general de estructuras de datos?"

## 6. Referencias y recursos
- Enlaces y libros de referencia, artículos científicos.
- Visualizaciones y demostraciones.
