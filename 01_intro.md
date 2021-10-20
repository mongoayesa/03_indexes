#  Introducción a Índices en MongoDB

Índices son a nivel de colección

## Crear índices createIndex()
Sintaxis

```
db.<coleccion>.createIndex({<campo>: 1 | -1, ...}, <opciones>)
```

```
use gimnasio

db.clientes.createIndex({apellidos: 1}) // Índice simple {name: "<nombre-específico"}
```

## Visualizar los índices

```
db.clientes.getIndexes()
```

## Eliminar índice


```
db.clientes.dropIndex('apellidos_1')
```

## Eliminar todos los índices


```
db.clientes.dropIndexes() // Elimina todos los índices excepto _id
```