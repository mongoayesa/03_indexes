# Planes de consulta en MongoDB
¿Como selecciona los índices MongoDB en las consultas?

El algoritmo por la forma de consulta (campos que tiene, orden, etc...) evaluar los índices disponibles
y seleccionar el plan ganador y esa información la guarda en el Plan Cache para reutilizar.

Método explain(<verbosidad>) devuelve información sobre qué índices escogerá o escogería para esa forma de consulta

Verbosidad ¡Ojo Certificación!
- queryPlanner (por defecto) No ejecuta la consulta pero analiza el plan de ejecución e indica el plan ganador
- executionStats Ejecuta el plan ganador y devuelve las estadísticas de esa ejecución
- allPlansExecution Idem a anterior y devuelve las estadísticas de los planes no ejecutadas

Etapas
- COLLSPAN Escaneando documentos en la colección (ya esté en disco o en datos frecuentemente accesados)
  (no se usa índice)
- IXSCAN Escaneando documentos en índices
- FETCH Recupera documentos de la colección a partir de las lecturas de los índices
- SORT Ordenando los documentos en memoria (evitar)

Set de datos

let names = ['Laura','Juan','Fernando','María','Carlos','Lucía','David'];

let surnames = ['Fernández','Etxevarría','Nadal','Novo','Sánchez','López','García'];

let dniWords = ['A','B','C','D','P','X'];

let runners = [];

for (i = 0; i < 100000; i++) {
    runners.push({
        _id: i,
        name: names[Math.floor(Math.random() * names.length)],
        surname1: surnames[Math.floor(Math.random() * surnames.length)],
        surname2: surnames[Math.floor(Math.random() * surnames.length)],
        age: Math.floor(Math.random() * 100),
        dni: Math.floor(Math.random() * 1e8) + dniWords[Math.floor(Math.random() * dniWords.length)]
    })
}

Creamos indice simple para el campo dni

db.runners.createIndex({dni: 1})

db.runners.find({surname1: 'Nadal'}).explain('executionStats')

db.runners.find({dni: '26428452B'}).explain('executionStats')

Creamos dos índices simples

db.runners.createIndex({age: 1})
db.runners.createIndex({surname1: 1})

db.runners.find({surname1: 'Nadal', age: {$gte: 18}}).explain('executionStats')

Intersección de índices

db.runners.find({
    $or: [
        {surname1: "Etxevarría", age: {$gte: 18}},
        {age: 45}
    ]
}).explain('executionStats')

## Uso de índices múltiples ¡Ojo certificación!

db.runners.dropIndexes()

db.runners.createIndex({age: 1, surname: 1}) // Influye tanto el orden de los campos como el sentido 1 ó -1

### Consultas con un solo campo del índice (para que se use el índice el campo tiene que ser
prefijo del índice)

db.runners.find({age: {$gte: 50}}).explain('executionStats')  // Usará el índice

db.runners.find({surname1: 'Nadal'}).explain('executionStats') // No usará el índice porque no es prefijo

Prefijos

db.foo.createIndex({a: 1, b: 1, c: 1})

{a: <valor>}
{a: <valor>, b: <valor>}
{b: <valor>, a: <valor>}
{a: <valor>, b: <valor>, c: <valor>}
... sucesivos que tengan a, b y c en distinto orden

No sería prefijo y por tanto no usa índice

{b: <valor>, c: <valor>}
{c: <valor>}

db.runners.dropIndexes()

db.runners.createIndex({age: 1, surname1: 1, dni: 1})

db.runners.find({dni: '13054565A', surname1: 'Nadal', age: 20}).explain('executionStats') // Ok
db.runners.find({surname1: 'Nadal', age: 20}).explain('executionStats') // Ok
db.runners.find({surname1: 'Nadal', nombre: 'Fernando'}).explain('executionStats') // No
db.runners.find({dni: '13054565A',  age: 20}).explain('executionStats') // OK pero a veces no lo usará
db.runners.find({surname1: 'Nadal'}).explain('executionStats') // No