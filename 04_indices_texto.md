# Índices de texto
Sintaxis

db.<coleccion>.createIndex({<campo>: "text"})

Uso en las consultas
db.<coleccion>.find({$text: {$search: <termino-string>}}) // No se indica el campo en la
consulta porque solo puede haber un índice de texto por coleccion

```
use biblioteca

db.libros.createIndex({titulo: 'text'})

```

Set de datos

```
db.libros.insert([
    {titulo: "París era una Fiesta", autor: "Ernest Hemingway"},
    {titulo: "París, La Guía Completa", autor: "vv.aa."},
    {titulo: "La Ciudad y los Perros", autor: "Mario Vargas Llosa"}
])
```

## Búsqueda de una palabra

db.libros.find({$text: {$search: 'paRis'}}) // El índice es case insesitive y diacritic insensitives

db.libros.find({$text: {$search: 'pa'}}) // No encuentra fragmentos de palabras

db.libros.find({$text: {$search: /Pa/}}) // No soporta expresiones regulares

## Búsqueda de varias palabras (OR inclusivo) ¡Ojo certificación!

db.libros.find({$text: {$search: 'Paris era'}})

## Búsqueda de varias palabras (AND)

db.libros.find({$text: {$search: '"Paris" "fiesta"'}}) // El documento debe tener todas las palabras en el campo

## Búsqueda de frase (se pasa el string entrecomillada)

db.libros.find({$text: {$search: '"Paris era"'}}) // Busca docs que contengan en titulo esa frase

## Búsqueda excluyendo palabras  ¡Ojo certificación!

db.libros.find({$text: {$search: 'paris -guia'}}) // Entrecomillar si lo que se busca es símbolo

## Stop words (evitar la búsqueda de articulos, preposiciones, etc...)

db.libros.insert({titulo: 'The Second World War', autor: 'John Doe'})

db.libros.find({$text: {$search: 'the'}}) // No devuelve el documento

db.libros.find({$text: {$search: 'la', $language: 'es'}}) // No devuelve documentos que contengan la

