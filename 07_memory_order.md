# Ordenación en memoria

Para las consultas ordenadas Mongo permite evitar la ordenación en memoria usando
eficientemente los índices

Para evitar el uso de ordenación en memoria el sentido de ordenación de los campos
debe ser igual o inverso (en todos los campos) al de la mayoría de consultas.

## Con índices simples este efecto no influye

db.runners.createIndex({dni: 1})

db.runners.find({}).sort({dni: 1}) // No ordena en memoria
db.runners.find({}).sort({dni: -1}) // Tampoco ordena en memoria

## Muy importante en índices compuestos

db.runners.dropIndexes()

db.runners.createIndex({age: -1, surname1: 1})

db.runners.find({}).sort({age: -1, surname1: 1}).explain('executionStats') // OK no ordena en memoria
db.runners.find({}).sort({age: 1, surname1: -1}).explain('executionStats') // OK no ordena en memoria
db.runners.find({}).sort({age: 1, surname1: 1}).explain('executionStats') // Ordena en memoria: evitar

db.runners.find({age: {$gte: 18}}).sort({age: 1, surname1: 1}).explain('executionStats') // Una
// forma de consulta puede usar el índice pero, por el orden de los campos, necesitar usar la memoria para ordenar


Caso de uso muy particular de mongoDB. ¡Ojo certificación! Si una consulta tiene una ordenación con
campos que no sean prefijo del índice, podría evitar la ordenación en memoria si la consulta tiene el campo
o campos que le faltan como prefijo y la consulta es de igualdad (con expresión no funciona)

db.runners.find({age: 40}).sort({surname1: 1}) // Ok no ordena en memoria