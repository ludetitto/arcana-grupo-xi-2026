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
- Lista de operaciones con nombres estandarizados (por ejemplo: push/pop/peek, insert/delete/find, append/concat, union/intersect).
- Para cada operación: breve descripción de lo que hace.

### Complejidad
- Por operación: tiempo (peor/ promedio/ amortizado) y complejidad espacial adicional.
- Notas sobre costos ocultos (reallocs, rehash, recorridos, copias).

### Detalles operativos
- Casos especiales: operaciones en estructura vacía/llena, duplicados, orden, límites de tamaño.
- Comportamiento en concurrencia o fallos (si aplica).

Debe responder a: "¿qué puedo hacer y cuánto cuesta?"

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
- Índices de bases de datos relacionales, por igualdad y por rango (PostgreSQL lo usa por defecto).
- Metadatos de filesystems que deben escalar sin perder balance (HFS+ y NTFS).
- Almacenamiento embebido de pares clave-valor (SQLite implementa sus índices como B-Trees).
- Datasets que no entran en RAM y requieren acceso ordenado con mínimas lecturas físicas.

### Cuándo NO usarlo
- Si el dataset entra en memoria, un AVL Tree o Red-Black Tree tiene menor overhead por nodo.
- Si solo hacen falta búsquedas puntuales sin orden, una [[hash table]] da $O(1)$ promedio.
- Si predominan las búsquedas por rango secuenciales sobre disco, conviene un B+Tree, no un B-Tree puro.
- Si el volumen es chico, el balanceo no se justifica frente a un array ordenado.

### Comparaciones
- **vs Red-Black Tree / AVL Tree:** igual $O(\log n)$ en memoria, pero al tener máximo 2 hijos por nodo necesitan más niveles de altura, disparando los accesos a disco; el B-Tree agrupa claves por nodo para calzar con el tamaño de página física.
- **vs [[hash table]]:** gana en búsqueda puntual con $O(1)$, pero no soporta recorridos ordenados ni búsquedas por rango.
- **vs B+Tree:** guarda datos solo en las hojas y las enlaza, optimizando las búsquedas por rango; el B-Tree también guarda datos en nodos internos, ahorrando una búsqueda a veces pero complicando el recorrido. InnoDB usa B+Tree.

### Ventajas / desventajas
| Ventajas | Desventajas |
| :--- | :--- |
| Pocos accesos a disco por su altura baja | Implementación más compleja (splits, merges) |
| Balanceo garantizado en toda operación | Poco eficiente si todo entra en RAM |
| Algunas búsquedas terminan antes de llegar a una hoja | Búsquedas por rango más lentas que en un B+Tree |
| Cada nodo aprovecha un bloque completo de disco | Puede desperdiciar espacio en nodos no llenos |

### Señales de reconocimiento
- "Minimizar accesos a disco"
- "Índice de base de datos"
- "Metadatos de un filesystem"
- "Grandes volúmenes de datos que no entran en memoria"
- "Cada nodo puede tener múltiples claves e hijos"

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
