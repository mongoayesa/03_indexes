## Índice simple

```
use gimnasio

db.clientes.createIndex({apellidos: 1})
```

Si estuviera en campos de subdocumentos se usa notación del punto

```
db.clientes.createIndex({"direccion.calle": 1})
```

## Índices compuestos

```
db.clientes.createIndex({apellidos: 1, nombre: -1}) // Influye el orden de los campos
```
