## MongoDB Cheatsheet: Guía Rápida de Consultas (`mongosh`)

Esta guía te ayudará a recordar los comandos y operadores más comunes para realizar consultas en MongoDB.

### 1. Operaciones CRUD Básicas

* **Seleccionar Base de Datos:**
    ```javascript
    use nombre_de_tu_base_de_datos
    ```

* **Mostrar Colecciones:**
    ```javascript
    show collections
    ```

* **Insertar Documentos:**
    * Insertar uno:
        ```javascript
        db.nombre_coleccion.insertOne({ campo1: "valor1", campo2: 123 })
        ```
    * Insertar varios:
        ```javascript
        db.nombre_coleccion.insertMany([
          { campo1: "valorA", campo2: 10 },
          { campo1: "valorB", campo2: 20 }
        ])
        ```

* **Buscar Documentos (`find`):**
    * Encontrar todos:
        ```javascript
        db.nombre_coleccion.find({})
        ```
    * Encontrar con filtro:
        ```javascript
        db.nombre_coleccion.find({ campo1: "valor_buscado" })
        ```
    * Encontrar uno solo:
        ```javascript
        db.nombre_coleccion.findOne({ campo1: "valor_buscado" })
        ```

* **Actualizar Documentos:**
    * Actualizar el primer documento que coincida:
        ```javascript
        db.nombre_coleccion.updateOne(
          { criterio_campo: "valor_criterio" }, // Criterio de búsqueda
          { $set: { campo_a_actualizar: "nuevo_valor" } } // Operación de actualización
        )
        ```
    * Actualizar todos los documentos que coincidan:
        ```javascript
        db.nombre_coleccion.updateMany(
          { criterio_campo: "valor_criterio" },
          { $set: { campo_a_actualizar: "nuevo_valor_multiple" } }
        )
        ```
    * Reemplazar un documento (cuidado, borra campos no especificados):
        ```javascript
        db.nombre_coleccion.replaceOne(
            { criterio_campo: "valor_criterio" },
            { nuevo_campo1: "val1", nuevo_campo2: "val2" } // El documento completo nuevo
        )
        ```

* **Eliminar Documentos:**
    * Eliminar el primer documento que coincida:
        ```javascript
        db.nombre_coleccion.deleteOne({ criterio_campo: "valor_criterio" })
        ```
    * Eliminar todos los documentos que coincidan:
        ```javascript
        db.nombre_coleccion.deleteMany({ criterio_campo: "valor_criterio" })
        ```

* **Contar Documentos:**
    ```javascript
    db.nombre_coleccion.countDocuments({ criterio_campo: "valor_criterio" }) // Con filtro
    db.nombre_coleccion.countDocuments({}) // Todos los documentos
    ```

### 2. Operadores de Consulta (en el objeto de filtro de `find`)

* **Comparación:**
    * `$eq`: Igual a (implícito si solo se da el valor: `{ campo: "valor" }`)
    * `$ne`: No igual a (`{ campo: { $ne: "valor" } }`)
    * `$gt`: Mayor que (`{ campo: { $gt: 10 } }`)
    * `$gte`: Mayor o igual que (`{ campo: { $gte: 10 } }`)
    * `$lt`: Menor que (`{ campo: { $lt: 10 } }`)
    * `$lte`: Menor o igual que (`{ campo: { $lte: 10 } }`)
    * `$in`: Está en un array de valores (`{ campo: { $in: ["valor1", "valor2"] } }`)
    * `$nin`: No está en un array de valores (`{ campo: { $nin: ["valor1", "valor2"] } }`)

* **Lógicos:**
    * `$and`: Une condiciones con Y lógico (`{ $and: [ { campo1: "v1" }, { campo2: { $gt: 5 } } ] }`)
        * (Nota: `{ campo1: "v1", campo2: { $gt: 5 } }` es un `$and` implícito)
    * `$or`: Une condiciones con O lógico (`{ $or: [ { campo1: "v1" }, { campo2: "v2" } ] }`)
    * `$not`: Niega una condición (`{ campo: { $not: { $gt: 10 } } }`) (es decir, menor o igual a 10)
    * `$nor`: Ni una ni otra (`{ $nor: [ { precio: 1.99 }, { cantidad: { $lt: 20 } } ] }`)

* **Elementos:**
    * `$exists`: El campo existe (o no) (`{ campo: { $exists: true } }`)
    * `$type`: El campo es de un tipo específico (`{ campo: { $type: "string" } }` o `$type: 2`)

* **Arrays:**
    * `$all`: El array contiene todos los elementos especificados (`{ tags: { $all: ["mongo", "db"] } }`)
    * `$elemMatch`: Al menos un elemento del array cumple la condición (`{ scores: { $elemMatch: { $gte: 80, $lt: 90 } } }`)
    * `$size`: El array tiene un tamaño específico (`{ tags: { $size: 3 } }`)

### 3. Proyección, Ordenación y Paginación

* **Proyección (seleccionar campos a mostrar):**
    * Mostrar solo `nombre` y `email` (y `_id` por defecto):
        ```javascript
        db.nombre_coleccion.find({}, { nombre: 1, email: 1 })
        ```
    * Excluir `direccion` (y mostrar todos los demás, incluido `_id`):
        ```javascript
        db.nombre_coleccion.find({}, { direccion: 0 })
        ```
    * Mostrar solo `nombre` y `email`, y excluir `_id`:
        ```javascript
        db.nombre_coleccion.find({}, { nombre: 1, email: 1, _id: 0 })
        ```

* **Ordenación (`sort`):**
    * Ordenar por `nombre` ascendente (1):
        ```javascript
        db.nombre_coleccion.find().sort({ nombre: 1 })
        ```
    * Ordenar por `puntuacion` descendente (-1):
        ```javascript
        db.nombre_coleccion.find().sort({ puntuacion: -1 })
        ```
    * Ordenar por múltiples campos:
        ```javascript
        db.nombre_coleccion.find().sort({ categoria: 1, precio: -1 })
        ```

* **Paginación:**
    * `limit()`: Limitar el número de resultados:
        ```javascript
        db.nombre_coleccion.find().limit(10)
        ```
    * `skip()`: Omitir un número de resultados (para paginar):
        ```javascript
        db.nombre_coleccion.find().skip(20).limit(10) // Página 3 si el tamaño de página es 10
        ```

### 4. Framework de Agregación (`aggregate`)

Se usa para procesar datos en múltiples etapas (pipeline).
`db.nombre_coleccion.aggregate([ { $etapa1 }, { $etapa2 }, ... ])`

* **Etapas Comunes:**
    * `$match`: Filtra documentos (similar al filtro de `find`).
        ```javascript
        { $match: { status: "A" } }
        { $match: { edad: { $gte: 18 } } }
        ```
    * `$group`: Agrupa documentos por un identificador y calcula valores agregados.
        ```javascript
        // Agrupar por 'categoria' y contar cuántos hay en cada una
        {
          $group: {
            _id: "$categoria", // Campo por el que se agrupa (prefijo '$' para referenciar campo)
            total_productos: { $sum: 1 },
            precio_promedio: { $avg: "$precio" }
          }
        }
        ```
    * `$project`: Remodela los documentos (selecciona, excluye, añade campos calculados).
        ```javascript
        {
          $project: {
            _id: 0, // Excluir el _id
            nombre_mayusculas: { $toUpper: "$nombre" }, // Nuevo campo calculado
            info_producto: { nombre: "$nombre", precio: "$precio" } // Subdocumento
          }
        }
        ```
    * `$sort`: Ordena los documentos (similar al `sort` de `find`).
        ```javascript
        { $sort: { total_ventas: -1 } } // Después de un $group, por ejemplo
        ```
    * `$limit`: Limita el número de documentos que pasan a la siguiente etapa.
        ```javascript
        { $limit: 5 }
        ```
    * `$skip`: Omite un número de documentos.
        ```javascript
        { $skip: 10 }
        ```
    * `$unwind`: Deconstruye un campo de tipo array para generar un documento por cada elemento del array.
        ```javascript
        { $unwind: "$tags" } // Si un doc tiene { tags: ["a", "b"] }, genera dos docs
        ```
    * `$addFields`: Añade nuevos campos a los documentos (similar a `$project` pero mantiene los campos existentes).
        ```javascript
        { $addFields: { nuevo_campo: "valor_calculado", total_score: { $sum: "$scores_array" } } }
        ```
    * `$count`: Cuenta el número de documentos que llegan a esta etapa y lo devuelve en un campo.
        ```javascript
        { $count: "documentos_filtrados_y_agrupados" }
        ```
    * `$lookup`: Realiza un "left outer join" con otra colección.
        ```javascript
        {
          $lookup: {
            from: "otra_coleccion",      // La colección con la que hacer join
            localField: "id_usuario",    // Campo de la colección actual
            foreignField: "_id",         // Campo de 'otra_coleccion'
            as: "info_usuario_completa" // Nombre del nuevo array donde se guarda la info del join
          }
        }
        ```
    * `$out` / `$merge`: Escribe los resultados de la agregación a una nueva colección o actualiza una existente.
        * `$out`: Reemplaza la colección de salida (¡cuidado!).
            ```javascript
            { $out: "nombre_coleccion_resultado" }
            ```
        * `$merge`: Fusiona los resultados en una colección existente (más flexible).
            ```javascript
            { $merge: { into: "coleccion_destino", on: "_id", whenMatched: "replace", whenNotMatched: "insert" } }
            ```

* **Operadores de Acumulación (usados dentro de `$group`):**
    * `$sum`: Suma valores (`{ $sum: "$cantidad" }` o `{ $sum: 1 }` para contar)
    * `$avg`: Calcula el promedio (`{ $avg: "$precio" }`)
    * `$min`: Obtiene el valor mínimo (`{ $min: "$puntuacion" }`)
    * `$max`: Obtiene el valor máximo (`{ $max: "$puntuacion" }`)
    * `$push`: Añade un valor a un array (`{ $push: "$nombre_producto" }`)
    * `$addToSet`: Añade un valor a un array solo si no existe ya (`{ $addToSet: "$tag" }`)
    * `$first`: Obtiene el primer valor de un grupo (útil después de `$sort`) (`{ $first: "$fecha_ingreso" }`)
    * `$last`: Obtiene el último valor de un grupo (`{ $last: "$fecha_actualizacion" }`)

* **Otros Operadores Útiles en Agregación (dentro de `$project`, `$addFields`, etc.):**
    * **Arrays:**
        * `$size`: Devuelve el tamaño de un array (`{ $size: "$comentarios" }`)
        * `$filter`: Selecciona un subconjunto de un array (`{ $filter: { input: "$items", as: "item", cond: { $gte: [ "$$item.precio", 100 ] } } }`)
        * `$map`: Aplica una expresión a cada elemento de un array (`{ $map: { input: "$precios", as: "p", in: { $multiply: [ "$$p", 1.21 ] } } }`)
    * **Condicionales:**
        * `$cond`: `if-then-else` (`{ $cond: { if: { $gte: ["$cantidad", 100] }, then: "alto_stock", else: "bajo_stock" } }`)
        * `$ifNull`: Devuelve un valor si la expresión es nula (`{ $ifNull: [ "$descripcion", "Sin descripción" ] }`)
    * **Fechas:** (Ej: `$year`, `$month`, `$dayOfMonth`, `$dateToString`)
        ```javascript
        { $project: { anio_venta: { $year: "$fechaVenta" } } }
        { $project: { fecha_formateada: { $dateToString: { format: "%Y-%m-%d", date: "$fechaCreacion" } } } }
        ```
    * **Strings:** (Ej: `$toUpper`, `$toLower`, `$concat`, `$substrCP`)
        ```javascript
        { $project: { nombre_completo: { $concat: ["$nombre", " ", "$apellido"] } } }
        ```
    * **Aritméticos:** (Ej: `$add`, `$subtract`, `$multiply`, `$divide`, `$mod`)
        ```javascript
        { $project: { precio_con_iva: { $multiply: ["$precio_base", 1.21] } } }
        ```

### 5. Índices

Mejoran el rendimiento de las consultas.

* **Crear un índice simple ascendente:**
    ```javascript
    db.nombre_coleccion.createIndex({ campo_a_indexar: 1 }) // 1 para ascendente, -1 para descendente
    ```
* **Crear un índice compuesto:**
    ```javascript
    db.nombre_coleccion.createIndex({ campo1: 1, campo2: -1 })
    ```
* **Crear un índice único (evita duplicados en ese campo):**
    ```javascript
    db.nombre_coleccion.createIndex({ email: 1 }, { unique: true })
    ```
* **Ver índices de una colección:**
    ```javascript
    db.nombre_coleccion.getIndexes()