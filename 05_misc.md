# Otros tipos de índices

## Wildcard index

```
use fabrica

db.productos.insert({
    nombre: "Turbina F-48",
    caracteristicas: {
        tuercas: "F-11",
        chapa: "o-27"
    }
})

db.productos.createIndex({"caracteristicas.$**": 1})

db.productos.insert({
    nombre: "Turbina F-48C",
    caracteristicas: {
        tuercas: "F-11",
        chapa: "o-27",
        colector: "F-R-12"
    }
})

db.productos.find({"caracteristicas.colector": "F-R-12"}).explain() // Empleará el índice
```

## Índices geoespaciales
Sintaxis
db.<coleccion>.createIndex({<campo-coordenadas>: "2dsphere"})

```
use newyork

db.restaurants.createIndex({"address.coord": "2dsphere"})

```

### Consulta de proximidad $near

db.restaurants.find({
    "address.coord": {
        $near: {
            $geometry: {
                type: "Point",
                coordinates: [-73.9667, 40.78]
            },
            $minDistance: 100,
            $maxDistance: 500 // Documentos cuyas coordenadas estén entre 100 y 500 mts del punto de referencia
        }
    }
})

### Consulta acotada $geoWithin

db.restaurants.find({
    "address.coord": {
        $geoWithin: {
            $geometry: {
                type: "Polygon",
                coordinates: [
                    [-73.9667, 40.78], // Coordenadas de los puntos que definen la superficie
                    [-74.9667, 40.78],
                    [-74.9667, 41.78],
                    [-73.9667, 41.78],
                    [-73.9667, 40.78],                    
                ] // Documentos cuyas coordenadas esten dentro de la superfice definida
            },
        }
    }
})

## Índices únicos

```
use clinica
db.clientes.createIndex({dni: 1}, {unique: true})

db.clientes.insert({nombre: 'Juan', apellidos: 'Pérez López', dni: '68736237V'})
db.clientes.insert({nombre: 'Sara', apellidos: 'Gómez López', dni: '68736237V'}) // Error

```

¡Ojo certificación! Si el documento no contiene el campo del índice único solo podrá haber un documento
sin el campo ya que a los efectos de índice considera su valor como null

```
db.clientes.insert({nombre: 'Raquel', apellidos: 'Pérez López'})
db.clientes.insert({nombre: 'Sara', apellidos: 'Pérez López'}) // Error
```

Pasaría lo mismo si intentamos construir un índice con un campo único y tengamos varios documentos
en la colección existente que no tengan el campo, en ese caso el índice no se creará.

Es posible crear indices múltiples únicos

## Índices parciales
Sintaxis
db.<coleccion>.createIndex(<documento-indice>, {partialFilterExpression: <expresion>})
En la expresión se definen los documentos que queremos que sean incluidos (y por tanto posteriormente utiizados) en 
el índice

```
db.clientes.dropIndexes()

db.clientes.createIndex({apellidos: 1}, {partialFilterExpression: {createdAt: {$gte: new Date(2015, 0, 1)}}})

db.clientes.insert([
    {apellidos: "Fernández", nombre: "Juan", createdAt: new Date()},
    {apellidos: "Gómez", nombre: "Laura", createdAt: new Date(2013, 2, 2)} 
])

db.clientes.find({apellidos: "Gómez", nombre: "Laura"}).explain()  // No usará el índice
db.clientes.find({apellidos: "Fernández", nombre: "Juan"}).explain() // Empleará el índice

```

## Índices sparse (escasos) 
No incluyen los documentos que no contengan el campo del índice
Sintaxis
db.<colección>.createIndex(<documento>, {sparse: true})

```
use maraton

db.runners.insert([
    {name: 'Juan', surname1: 'Pérez', dni: '46546544V'},
    {name: 'Sara', surname1: 'López'},
])

db.runners.createIndex({dni: 1}, {sparse: true})

db.runners.find().sort({dni: 1}).hint({dni: 1}) // hint() fuerza el uso de un índice en la consulta, en este caso
nos sirve para comprobar que los docs que no tengan DNI no serán devueltos y que efectivamente no se están incluyendo
en el índice
```



