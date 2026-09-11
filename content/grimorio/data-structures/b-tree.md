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
Un **árbol B** puede pensarse como las puertas de embarque de un aeropuerto: si tu vuelo sale por la Puerta 2, seguís los carteles principales que dividen la terminal ([Puertas 1-20 | Puertas 21-40 | Puertas 41-60]), vas directo a la Terminal 3 y caminas unos pocos metros hasta el asiento 42, sin tener que pasar por cada puerta desde la 1.
Este es un árbol de búsqueda balanceado en el que cada nodo puede guardar varias claves ordenadas y tener varios hijos, en vez de una sola clave y dos hijos como en un árbol binario. Resuelve el problema de que, con muchos datos, un árbol binario necesita demasiados nodos para llegar a cualquier dato.

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
# Suponiendo que tenemos que cargar en memoria los índices de los registros de una base de datos, utilizamos un B-Tree
btree = BTree(orden=3)
btree.insertar(10)
# [10]

btree.insertar(20)
# [10, 20]

btree.insertar(5)
# [5, 10, 20]

btree.insertar(6)
#       [10]
#    /         \
# [5, 6]       [20]

print(btree.buscar(10))  # Devuelve el nodo que contiene la clave 10
```

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
Existen variantes de B-Tree, como B+ Tree y B* Tree, cada una con sus propias características y optimizaciones. Por su lado los B+ Tree contienen información únicamente en sus hojas, facilitando la búsqueda secuencial referenciando nodos contiguos.
Por otro lado, B* mejora la eficiencia de la estructura al dividir los nodos de manera más equitativa.

### Relación con otras estructuras
Al tratarse de un arbol compuesto de nodos, el B-Tree es capaz de almacenar claves e información en arreglos, listas enlazadas, o incluso en otras estructuras de datos como tablas hash.

### Notas avanzadas
- Temas avanzados como persistencia, concurrencia, paralelismo, ordenamientos aleatorios, caching, tuning de parámetros.

Debe responder a: "¿cómo encaja en el mapa general de estructuras de datos?"

## 6. Referencias y recursos
- B-Trees. (s/f). Umich.edu. Recuperado el 3 de septiembre de 2026, de https://www.eecs.umich.edu/courses/eecs380/ALG/niemann/s_btr.htm
- Introduction of B+ tree. (2018, abril 4). GeeksforGeeks. https://www.geeksforgeeks.org/dbms/introduction-of-b-tree/
- Visualizaciones y demostraciones.
