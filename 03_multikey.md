# Índices multikey sobre campos que almacenan arrays
Sintaxis (idem a indice simple o compuesto)
```
db.<coleccion>.createIndex({<array>: 1 | -1, ...})
```

Set de datos

use shop

db.productos.insert({
    nombre: "Sudadera FTR34",
    marca: "Nike",
    stock: [
        {color: "azul", talla: "xs", cantidad: 2},
        {color: "azul", talla: "l", cantidad: 12}
    ],
    categorias: ["mujer","ropa"]
})


### Una colección puede tener varios índices multikey cuando son simples

```
db.productos.createIndex({stock: 1})
db.productos.createIndex({categorias: 1})
```

### Una colección solo puede tener un índice multikey multiple (es decir de los campos del índice solo uno puede ser array)

```
db.productos.createIndex({stock: 1, marca: -1}) // Sin problema
db.productos.createIndex({categorias: 1, stock: -1}) // No permitido
```

### Cuando tengamos índices multikey múltiples las operaciones de inserción impedirán que
se añadan arrays al campo con otros tipos ¡Ojo certificación!

db.productos.insert({
    nombre: "Gorra Sport",
    marca: ["Nike","Adidas"],
    stock: [
        {color: "rojo", talla: "única", cantidad: 2},
        {color: "azul", talla: "única", cantidad: 12}
    ],
    categorias: ["hombre","ropa"]
})

MongoBulkWriteError: cannot index parallel arrays [marca] [stock]
