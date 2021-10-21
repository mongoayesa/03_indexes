# Índices y collation ¡Ojo certificacion!

La colacción se puede establecer a nivel de índice

```
use maraton

db.runners.dropIndexes()

db.runners.createIndex({surname1: 1}, {collation: {locale: 'es', strength: 1}})

```

Cuando tengamos un índice con colacción, para que el índice sea utilizado la
operación debe tener la colación detallada con los mismos valores

```
db.runners.find({surname1: 'lopez'}).collation({locale: 'es', strength: 1}) // usa el índice

db.runners.find({surname1: 'lopez'}).collation({locale: 'en', strength: 1}) // no usa el índice

```

Cuando tengamos una colección con colación (creada al momento de crear la colección)
- Si en la consulta no se especifica colación, usará el índice
- Si en la consulta se especifica colación y es distinta a la de la colección, no usará el índice


```
db.createCollection('jueces', {collation: {locale: 'es', strength: 1}})

db.jueces.insert({nombre: 'Juan', apellidos: 'Pérez'})

db.jueces.createIndex({apellidos: 1})

db.jueces.find({apellidos: 'pérez'}) // Usa el índice

db.jueces.find({apellidos: 'pérez'}).collation({locale: 'en', strength: 1}) // no usa el índice

```