# Consultas totalmente cubiertas ¡Ojo certificación!

Las consultas más eficientes que se pueden llegar a hacer porque
solo necesitan acceder al índice

Las covered queries tienen que cumplir

- Todos los campos de la consulta forman parte de un mismo índice
- Todos los campos devueltos en la consulta (definimos en proyección) están en ese mismo índice

db.runners.createIndex({surname1: 1, age: -1})

// Ej. covered query

db.runners.find({surname1: 'Nadal', age: {$lte: 50}}, {surname1: 1, age: 1, _id: 0}) // Excluimos _id porque no está en el índice

