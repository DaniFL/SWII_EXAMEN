
## Ejercicios para practicar en **sample\_airbnb**

1. **Encuentra el alojamiento con más reviews.**
   Muestra solo el nombre y la cantidad de reviews.

    - size del array de reviews
    - ordenarlos de mayor a menor
    - Limit (1)

    Claves: 
    - Uso  de `aggregate([...])` porque queremos hacer varios pasos encadenados.
    - Usar `$project` para añadir un nuevo campo (el size del array de reviews).

    ```javascript
    db.listingsAndReviews.aggregate([
    {$project: { _id: 0,name: 1,numReviews: { $size: "$reviews" }}},
    { $sort: { numReviews: -1 } },
    { $limit: 1 }
    ])
    ```

Nota: cuando usar `$`y cuando no
| Contexto                        | Usar `$`                        | No usar `$`                     |
|--------------------------------|--------------------------------|--------------------------------|
| Nombre de campo en filtro       | No: `{ beds: 2 }`              |                                |
| Referencia a campo dentro expr | Sí: `{ $size: "$reviews" }`    |                                |
| Operadores MongoDB             | Sí: `$gte`, `$sum`, `$group`   |                                |
| Campos en `$group` (clave)     | Sí, para referirse al valor: `$property_type` | No para la clave `_id`          |


2. **Encuentra el alojamiento con más amenities.**
   Muestra el nombre y la cantidad de amenities.

    - size del array amenities
    - campo NumAmmenities
    - Ordendar descendente

    ```javascript
    db.listingsAndReviews.aggregate([
        {$project: {_id:0, name:1, NumAmenities:{$size: "$amenities"}}},
        {$sort: {NumAmenities: -1}},
        {$limit: 1}
    ])
    ```

3. **Agrupa los alojamientos por tipo de propiedad (`property_type`) y muestra cuántos hay de cada tipo.**

    - Agruppar por property_type
    - Aplicamos un count

    ```javascript
    db.listingsAndReviews.aggregate([
        {$group:{_id: "$property_type", totalProperties:{$sum: 1}}},
        {$project:{_id: 0, property_type: "$_id" ,totalProperties: 1 }}
    ])
    ```
    > totalProperties:{$sum: 1} --> 1 por cada elemento
    > `_id: 0, property_type: "$_id"` y no `{$project:{_id:0, property_type:1, totalProperties:1 }}` porque digamos que has cambiado el `_id` a `property_type`

> `$group` en MongoDB se usa para agrupar documentos según un campo o expresión, y calcular agregados como suma, promedio, conteo, máximo o mínimo.
> La sintaxis básica es:

  ```js
  {
    $group: {
      _id: <campo o expresión para agrupar>,
      campoAgregado: { <operador>: <valor> },
      ...
    }
  }
  ```
> Algunos operadores comunes en `$group` son: `$sum`, `$avg`, `$max`, `$min`, `$push`, `$first`, `$last`.
> El campo `_id` es obligatorio y define cómo se agrupan los documentos. Se puede agrupar por un campo específico, una expresión o `null` para agrupar todo en un solo grupo.



4. **Cuenta cuántos alojamientos tienen entre 2 y 5 camas (inclusive).**
    - del campo beds --> mayor a 1 y menor a 6
    ```javascript
    db.listingsAndReviews.find(
        {$and: [
            {beds: {$gte: 2}},
            {beds: {$lte: 5}},
        ]},
        {_id:0, name:1, beds:1}
    )
    ```
    > `$and` recibe un array de condiciones (**[]**, no {})
    ```javascript
    db.listingsAndReviews.find(
        { beds: { $gte: 2, $lte: 5 } }, 
        {_id:0, name:1, beds:1}
    )
    ```

---

## Ejercicios para practicar en **sample\_training**

5. **Agrupa las empresas por categoría (`category_code`) y muestra cuántas empresas hay en cada categoría.**
```javascript
db.companies.aggregate([
    {$group:{_id: "$category_code", TotalPerCategories:{$sum: 1}}},
    {$project: {_id: 0, category_code:"$_id", TotalPerCategories: 1}}
])
```

6. **Cuenta cuántas empresas (`companies`) tienen al menos un milestone con el atributo `stoned_year` anterior a 2000..**
    ```javascript
    db.companies.find({
        milestones: {$elemMatch: { stoned_year: { $lt: 2000 } }}
        }).count()
    ```

    > `$elemMatch` se usa para buscar documentos donde al menos un elemento de un array cumple una condición específica.

7. **Agrupa los viajes por `usertype` y muestra la duración total de viajes que hizo cada tipo de usuario. Luego, muestra los 5 tipos de usuario con más duración acumulada**

```javascript
db.trips.aggregate([
    {$group: {_id: "$usertype", TotalPerType: {$sum: 1}}}
])
```

---

# Ejercicios nivel 3
# Nivel 3
1. En `sample_airbnb.listingsAndReviews`, ¿qué "room type" existen? 
    ```javascript
    db.listingsAndReviews.aggregate([
        {$group: {_id: "$room_type"}}
    ])
    ```
2. En `sample_training.companies`, haga una query que devuelva el nombre y el año en el que se fundaron las 5 compañías más antiguas.  
    - founded_year
    ```javascript
        db.companies.aggregate([
            {$sort:{ founded_year: 1}},
            {$project: {_id:0, name:1,founded_year:1 }},
            {$limit:5}
        ])
    ```
> Muchos documentos tienen `founded_year`: `null`, lo cual hace que al ordenar con `$sort: { founded_year: 1 }`, esos valores null aparezcan primero.
    ```javascript
    db.companies.aggregate([
        {$match: { founded_year: { $ne: null } } },
        {$sort:{ founded_year: 1}},
        {$project: {_id:0, name:1,founded_year:1 }},
        {$limit:5}
    ])
    ```

3. En `sample_training.trips`, ¿en qué año nació el ciclista más joven? (sol. 1999)
```javascript
    db.trips.aggregate([
        {$match: { "birth year": { $type: 'number' } } },
        {$sort:{ "birth year": -1}},
        {$project: {_id:0, name:1,"birth year":1 }},
        {$limit:1}
    ])
```

> Otra forma de excluir valores inválidos en `'birth year'` es usando `$nin` para filtrar los que son `null` o cadenas vacías:

```js
db.trips.aggregate([
  { $match: { 'birth year': { $nin: [null, ''] } } },
  { $sort: { 'birth year': -1 } },
  { $project: { _id: 0, 'birth year': 1 } },
  { $limit: 5 }
])
```

# Nivel 2
1. En `sample_training.companies`, ¿cuántas empresas tienen más empleados que el año en el que se fundaron? (sol. 324)

> Cuando se necesita comparar dos campos del mismo documento (por ejemplo, si `number_of_employees` es mayor que `founded_year`), se usa el operador `$expr`, que permite evaluar expresiones con múltiples campos:

```js
db.companies.find({
  $expr: {
    $gt: ["$number_of_employees", "$founded_year"]
  }
}).count()

```
2. En `sample_training.companies`, ¿en cuántas empresas coinciden su `permalink` con su `twitter_username`? (sol. 1299)
```js
db.companies.find({
  $expr: {
    $eq: ["$permalink", "$twitter_username"]
  }
}).count()

```

3. En `sample_airbnb.listingsAndReviews`, ¿cuál es el nombre del alojamiento en el que pueden estar más de 6 personas alojadas y tiene exactamente 50 reviews? (sol. Sunset Beach Lodge Retreat)
    - Personas alojadas: accommodates > 6
    - reviews: size = 50
```js
db.listingsAndReviews.find({
  $expr: {
    $and: [
      { $gt: ["$accommodates", 6] },
      { $eq: [ { $size: "$reviews" }, 50 ] }
    ]
  }
})
```

4. En `sample_airbnb.listingsAndReviews`, ¿cuántos documentos tienen el `"property_type"` "House" e incluyen `"Changing table"` como una de las `"amenities"`? (sol. 11)


5. En `sample_training.companies`, ¿cuántas empresas tienen oficinas en Seattle? (sol. 117)
6. En `sample_training.companies`, haga una query que devuelva únicamente el nombre de las empresas que tengan exactamente 8 `"funding_rounds"`
7. En `sample_training.trips`, ¿cuántos viajes empiezan en estaciones que están al oeste de la longitud -74? (sol. 1928)  
   Nota 1: Hacia el oeste la longitud decrece  
   Nota 2: el formato es `<field_name>: [ <longitud>, <latitud> ]`
8. En `sample_training.inspections`, ¿cuántas inspecciones se llevaron a cabo en la ciudad de `"NEW YORK"`? (sol. 18279)
9. En `sample_airbnb.listingsAndReviews`, haga una query que devuelva el nombre y la dirección de los alojamientos que tengan `"Internet"` como primer elemento de `"amenities"`