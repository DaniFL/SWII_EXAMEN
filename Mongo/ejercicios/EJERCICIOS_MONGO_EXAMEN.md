# Ejercicios Mongo Examen

## EJ1

### 1. En `sample_training.zips` ¿Cuántas colecciones tienen menos de 1000 personas en el campo `pop`?

**Solución:** 8065

```javascript
sample_training > db.zips.countDocuments({ pop: { $lt: 1000 } });
8065;
```

### 2. En `sample_training.trips` ¿Cuál es la diferencia entre la gente que nació en 1998 y la que nació después de 1998?

**Solución:** 6

```javascript
sample_training > db.trips.countDocuments({ "birth year": { $eq: 1998 } });
12;
sample_training > db.trips.countDocuments({ "birth year": { $gt: 1998 } });
18;
```

### 3. En `sample_training.routes` ¿Cuántas rutas tienen al menos una parada?

**Solución:** 11

```javascript
sample_training > db.routes.countDocuments({ stops: { $gte: 1 } });
11;
```

## EJ2

### 1. En `sample_training.companies`, ¿cuántas empresas tienen más empleados que el año en el que se fundaron?

**Solución:** 324

```javascript
sample_training >
  db.companies
    .find({ $expr: { $gt: ["$number_of_employees", "$founded_year"] } })
    .count();
324;
```

### 2. En `sample_training.companies`, ¿en cuántas empresas coinciden su `permalink` con su `twitter_username`?

**Solución:** 1299

```javascript
sample_training >
  db.companies
    .find({ $expr: { $eq: ["$permalink", "$twitter_username"] } })
    .count();
1299;
```

### 3. En `sample_airbnb.listingsAndReviews`, ¿cuál es el nombre del alojamiento en el que pueden estar más de 6 personas alojadas y tiene exactamente 50 reviews?

**Solución:** Sunset Beach Lodge Retreat

```javascript
sample_airbnb> db.listingsAndReviews.findOne({ accommodates: {$gt:6},  number_of_reviews:50}, {name:1})
{ _id: '4294289', name: 'Sunset Beach Lodge Retreat' }
```

### 4. En `sample_airbnb.listingsAndReviews`, ¿cuántos documentos tienen el `property_type` "House" e incluyen "Changing table" como una de las `amenities`?

**Solución:** 11

```javascript
sample_airbnb >
  db.listingsAndReviews.countDocuments({
    $and: [
      { property_type: { $eq: "House" } },
      { amenities: { $in: ["Changing table"] } },
    ],
  });
11;
```

### 5. En `sample_training.companies`, ¿Cuántas empresas tienen oficinas en Seattle?

**Solución:** 117

```javascript
sample_training > db.companies.countDocuments({ "offices.city": "Seattle" });
117;
```

### 6. En `sample_training.companies`, haga una query que devuelva únicamente el nombre de las empresas que tengan exactamente 8 `funding_rounds`

```javascript
sample_training >
  db.companies.find({ funding_rounds: { $size: 8 } }, { name: 1, _id: 0 })[
    ({ name: "Twitter" },
    { name: "LinkedIn" },
    { name: "PayScale" },
    { name: "Xobni" },
    { name: "Zynga" },
    { name: "ShareThis" },
    { name: "TicketLeap" },
    { name: "Moblyng" },
    { name: "PlumChoice" },
    { name: "SolFocus" },
    { name: "HyperWeek" },
    { name: "Virident Systems" },
    { name: "Extreme Enterprises" },
    { name: "CipherMax" },
    { name: "Stemgent" },
    { name: "Sonos" },
    { name: "BridgeLux" },
    { name: "Silicor Materials" },
    { name: "1366 Technologies" },
    { name: "Biolex Therapeutics" })
  ];
```

### 7. En `sample_training.trips`, ¿cuántos viajes empiezan en estaciones que están al oeste de la longitud -74?

**Solución:** 1928

```javascript
sample_training >
  db.trips.countDocuments({
    "start station location.coordinates.0": { $lt: -74 },
  });
1928;
```

### 8. En `sample_training.inspections`, ¿cuántas inspecciones se llevaron a cabo en la ciudad de "NEW YORK"?

**Solución:** 18279

```javascript
sample_training > db.inspections.countDocuments({ "address.city": "NEW YORK" });
18279;
```

### 9. En `sample_airbnb.listingsAndReviews`, haga una query que devuelva el nombre y la dirección de los alojamientos que tengan "Internet" como primer elemento de `amenities`

```javascript
sample_airbnb >
  db.listingsAndReviews.find(
    { "amenities.0": "Internet" },
    { name: 1, _id: 0, address: 1, amenities: 1 }
  );
```

## EJ3

### 1. En `sample_airbnb.listingsAndReviews`, ¿qué `room types` existen?

```javascript
sample_airbnb >
  db.listingsAndReviews.distinct("room_type")[
    ("Entire home/apt", "Private room", "Shared room")
  ];
```

### 2. En `sample_training.companies`, haga una query que devuelva el nombre y el año en el que se fundaron las 5 compañías más antiguas.

```javascript
sample_training >
  db.companies
    .find({ founded_year: { $ne: null } }, { name: 1, _id: 0, founded_year: 1 })
    .sort({ founded_year: 1 })
    .limit(5)[
    ({ name: "US Army", founded_year: 1800 },
    { name: "Alstrasoft", founded_year: 1800 },
    { name: "DuPont", founded_year: 1802 },
    { name: "Bachmann Industries", founded_year: 1833 },
    { name: "Bertelsmann", founded_year: 1835 })
  ];
```

### 3. En `sample_training.trips`, ¿en qué año nació el ciclista más joven?

**Solución:** 1999

```javascript
sample_training >
  db.trips
    .find({ "birth year": { $nin: [null, ""] } }, { _id: 0, "birth year": 1 })
    .sort({ "birth year": -1 })
    .limit(1)[{ "birth year": 1999 }];
```
