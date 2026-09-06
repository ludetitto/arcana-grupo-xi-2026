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
Un **árbol B (B-tree)** es un árbol de búsqueda balanceado en el que cada nodo puede guardar varias claves ordenadas y tener varios hijos, en vez de una sola clave y dos hijos como en un árbol binario. Resuelve el problema de que, con muchos datos, un árbol binario necesita demasiados pasos (demasiados nodos) para llegar a cualquier dato (algo carísimo cuando cada nodo vive en el disco por ejemplo).

### Definición / propiedades
Un B-Tree de grado M $\ge$ 3 cumple:
- Cada nodo tiene como máximo M hijos y M−1 claves. Salvo la raíz, como mínimo M/2 hijos y (M/2)-1 claves.
- Un nodo interno con k claves tiene exactamente k+1 hijos.
- Las claves de un nodo están ordenadas y separan los rangos de los subárboles. El hijo i contiene solo claves entre `keys[i-1]` y `keys[i]`.
- **Todas las hojas están al mismo nivel** (invariante clave: el árbol siempre está perfectamente balanceado en altura).

### Representación
Cada nodo guarda un arreglo de claves ordenadas, un arreglo de punteros a hijos (uno más que la cantidad de claves) y un flag de si es hoja.

![Diagrama de un B-Tree de grado 4, con raíz de dos claves y tres hojas|254](/attachments/grimorio/data-structures/b-tree.svg)
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
