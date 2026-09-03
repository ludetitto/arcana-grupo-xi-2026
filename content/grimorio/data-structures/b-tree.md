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
Se utiliza una estructura Arbol compuesta de un puntero al nodo raíz. Este nodo raíz contiene un arreglo de claves y un arreglo de punteros a sus hijos.
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
Mantener un árbol no binario dónde sus claves estén ordenadas. Se usan dos estructuras de datos; Arbol para contener la raíz y Nodo para contener claves, información y referencia a hijos.

### Invariantes
- El puntero a la raíz nunca es nulo.
- Cada nodo tiene un número mínimo y máximo de claves que depende del orden del árbol.
- Después de insertar, el arbol debe dividirse en dos nodos si se excede el número máximo de claves.
- Despues de eliminar, el arbol debe fusionarse con otro nodo si no alcanza el mínimo de hijos.
- No acceder a hijos si el nodo es hoja.

### Ejemplo de código
```python
class Nodo:
    def __init__(self):
        self.claves: list = []
        self.hijos: list["Nodo"] = []


class BTree:
    def __init__(self, orden=5):
        if orden < 3:
            raise ValueError("El orden debe ser mayor o igual que 3")

        self.orden = orden
        self.maximo_claves = orden - 1
        self.minimo_claves = (orden + 1) // 2 - 1 - 1
        self.raiz = Nodo()

    def posicion(a, x):
      izq, der = 0, len(a)

      while izq < der:
          medio = (izq + der) // 2

          if a[medio] < x:
              izq = medio + 1
          else:
              der = medio

      return izq

    def buscar(self, clave, nodo=None):
        if nodo is None:
            nodo = self.raiz

        indice = posicion(nodo.claves, clave)
        if indice < len(nodo.claves) and nodo.claves[indice] == clave:
            return nodo
        if nodo.hoja:
            return None
        return self.buscar(clave, nodo.hijos[indice])

    def insertar(self, clave):
        if self.buscar(clave) is not None:
            return False

        division = self._insertar(self.raiz, clave)
        if division is not None:
            clave_media, nodo_derecho = division
            nodo_derecho = Nodo()
            nodo_derecho.claves = [clave_media]
            nodo_derecho.hijos = [self.raiz, nodo_derecho]
            self.raiz = nodo_derecho
        return True

    def _insertar(self, nodo, clave):
        indice = posicion(nodo.claves, clave)

        if nodo.hoja:
            nodo.claves.insert(indice, clave)
        else:
            division = self._insertar(nodo.hijos[indice], clave)
            if division is not None:
                clave_media, nodo_derecho = division
                nodo.claves.insert(indice, clave_media)
                nodo.hijos.insert(indice + 1, nodo_derecho)

        if len(nodo.claves) > self.maximo_claves:
            return self._dividir(nodo)
        return None

    def _dividir(self, nodo):
        medio = len(nodo.claves) // 2
        clave_media = nodo.claves[medio]
        nodo_derecho = Nodo()

        nodo_derecho.claves = nodo.claves[medio + 1 :]
        nodo.claves = nodo.claves[:medio]

        if not nodo.hoja:
            nodo_derecho.hijos = nodo.hijos[medio + 1 :]
            nodo.hijos = nodo.hijos[: medio + 1]

        return clave_media, nodo_derecho
  ```

- Ejemplo de uso típico
```python
btree = BTree(orden=3)
btree.insertar(10)
btree.insertar(20)
btree.insertar(5)
btree.insertar(6)
print(btree.buscar(10))  # Devuelve el nodo que contiene la clave 10
```

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
- B-Trees. (s/f). Umich.edu. Recuperado el 3 de septiembre de 2026, de https://www.eecs.umich.edu/courses/eecs380/ALG/niemann/s_btr.htm
- Visualizaciones y demostraciones.
