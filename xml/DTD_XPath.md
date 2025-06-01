# DTD y XPath

## Índice

- [DTD: Document Type Descriptor](#dtd-document-type-descriptor)
  - [Elementos DTD](#elementos-dtd)
  - [Atributos DTD](#atributos-dtd)
- [XPath](#xpath)
  - [Construcciones básicas para rutas](#construcciones-básicas-para-rutas)
  - [Predicados](#predicados)
  - [Funciones incluidas en XPath](#funciones-incluidas-en-xpath)
  - [Ejes de navegación](#ejes-de-navegación)

## DTD: Document Type Descriptor

- Descriptor de tipo documento
- Estándar para validar XML
- Lenguaje que proporciona una gramática para especificar:
    + Elementos
    + Atributos
    + Ordenación
    + Anidado
    + Número de apariciones

```xml
<! DOCTYPE root-element[
<!ELEMENT root-element (..)>
<!ELEMENT element1 (...)>
<!-- Resto de elementos -->
<! ATTLIST element1 attribute1 attribute-type attribute-value ...>
<!-- Resto de atributos -->
]>
```
### Elementos DTD
####  `<!DOCTYPE root-element [...]>`
- root-element: Nombre del elemento raíz. Tendrá que estar definido a continuación. 

####  `<!ELEMENT element-name category (...)>`
- **element-name**: Nombre del elemento
- **category**:
    + EMPTY: vacío
    + (#PCDATA): Parsed Character Data. Texto que se va a parsear. 
    + ANY: Cualquier combinación de datos parseables
    + (child1, child2, ...)
        - Los hijos tienen que aparecer en el mismo orden
        - Se puede especificar el número de ocurrencias:
            + `+` --> 1 or more
            + `*` --> 0 or more
            + `?` --> 0 or 1
            + Si no se especifica, implica uno y sólo uno
```
<!ELEMENT name (#PCDATA)> --> <name> Ana </name>
<!ELEMENT email (from, to+, body)> -->
    <email>
        <from> ... </from>
        <to> ... </to>
        <to> ... </to>
        <body> ... </body>
    </email>
```

### Atributos DTD
#### `<!ATTRLIST element-name attr-name attr-type attr-value [attr-name attr-type attr-value ...]>`

- **element-name**: Nombre del elemento al que pertenece
- **attr-name**: Nombre del atributo
- **attr-type**:
    - CDATA: Character Data. Texto que NO se va a parsear
    - (en1|en2|en3...): Sólo se permiten esos valores.
    - ID
    - IDREF (Id de referencia de otro elemento)
- **attr-value**:
    - *value*
    - #REQUIRED
    - #IMPLIED: el atributo es opcional
- [attr-name attr-type attr-value ...]
    - Se pueden añadir más atributos con la misma sintaxis
    - También se pueden definir en otra etiqueta ATTLIST

Ejemplo:

Archivo `dtd_ejemplo1.xml`:

```xml
<?xml version="1.0"?>
<!DOCTYPE note [
    <!ELEMENT note (to, from, heading, body)>
    <!ELEMENT to (#PCDATA)>
    <!ELEMENT from (#PCDATA)>
    <!ELEMENT heading (#PCDATA)>
    <!ELEMENT body (#PCDATA)>
]>
<note>
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend</body>
</note>
```

 Archivo `dtd_ejemplo2.xml`:

```xml
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "dtd_ejemplo2.dtd">
<note>
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend!</body>
</note>
```

Archivo `note.dtd`:

```dtd
<!ELEMENT note (to, from, heading, body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```

Ejemplo DTD con punteros:
Archivo `dtd_ejemploPunteros.dtd`:

```xml
<?xml version="1.0"?>
<!ELEMENT Bookstore (Book*, Author*)>

<!ELEMENT Book (Title, Remark?)>
<!ATTLIST Book 
  ISBN ID #REQUIRED
  Price CDATA #REQUIRED
  Authors IDREFS #REQUIRED>

<!ELEMENT Title (#PCDATA)>
<!ELEMENT Remark (#PCDATA)>

<!ELEMENT Author (Name)>
<!ATTLIST Author Ident ID #REQUIRED>

<!ELEMENT Name (#PCDATA)>

<Bookstore>
  <Book ISBN="ISBN-0-13-713526-2" Price="100" Authors="JU JW">
    <Title>A First Course in Database Systems</Title>
  </Book>
  <Book ISBN="ISBN-0-13-815504-6" Price="85" Authors="HG JU JW">
    <Title>Database Systems: The Complete Book</Title>
    <Remark>
      Amazon.com says: Buy this book bundled with
      <BookRef book="ISBN-0-13-713526-2" /> - a great deal!
    </Remark>
  </Book>
  <Author Ident="HG">
    <First_Name>Hector</First_Name>
    <Last_Name>Garcia-Molina</Last_Name>
  </Author>
  <Author Ident="JU">
    <First_Name>Jeffrey</First_Name>
    <Last_Name>Ullman</Last_Name>
  </Author>
  <Author Ident="JW">
    <First_Name>Jennifer</First_Name>
    <Last_Name>Widom</Last_Name>
  </Author>
</Bookstore>
```
## XPath
El DOM (Document Object Model) está pensado como una jerarquía de árbol --> XPath pensado para recorrerlo.

- Sintaxis similar a la de los directorios
- Usado en otros estandares como XLST
- Recomendación del W3C

Tipos de nodo:
- Elemento
- Atributo
- Texto
- Namespaces
- Instrucción a procesar
- Comentario
- Documento

### Construcciones básicas para rutas
- `/`: Separador. Si empieza por `/, referencia al nodo raíz
- `//`: Yo, y cualquier elemento descendiente
- `/book`: Etiquetas
- `/@ISBN`: Atributos
    - Se obtiene su valor con `/data(@ISBN)`
- `|`: OR lógico
- `*`: Comodín
- `../`: Ir a un nivel hacia atrás

Ejemplo
```xml
<?xml version="1.0" encoding="UTF-8">
<class>
<name> L.1.1.1 </name>
<student id = "033">
    <name> Mark </name>
    <year> 2003 </year>
</student>
<student id = "003">
    <name> James </name>
    <year> 2001 </year>
</student>
</class>
```
- Todos los nodos con el nombre student: `student`
- Elemento Raíz: `/class`
- Todos los elementos name independientemente de donde esté: `//name`
- Todos los nombre de los alumnos: `/class/student/name``
- Todos los atributos con nombre id: `//@id`

### Predicados
- `[ ]`: Separador del predicado
    - Se pueden anidar para agrupar condiciones
- `<`, `>`, `=`, `!=`: Comparadores
- `and`: AND lógico, enlaza condiciones
- `or`_ OR lógico, enlaza condiciones
- `[número]`: Contador, se empieza a contar en 1
- `[@attr]`: Selecciona los atributos de nombre *attr*
- [@attr='value']`: Atributos *attr* con el valor *value*

Ejemplo:
```xml
<?xml version="1.0" encoding="UTF-8">
<class>
<name> L.1.1.1 </name>
<student id = "033">
    <name> Mark </name>
    <year> 2003 </year>
</student>
<student id = "003">
    <name> James </name>
    <year> 2001 </year>
</student>
</class>
```
- ¿El segundo estudiante? `/class/student[2]`
- ¿Estudiante con id 035? `/class/student[@id="035"]`
- Estuduantes del año >= 1999 `/class/student[year>=1999]`
- Estudiantes con algún atributo `/class/student[@*]`

### Funciones incluidas en XPath
- Se pueden usar en los predicados
- Por ejemplo:
    - Contine: `contains(elemento, "texto")`
    - Nombre: `name()`
    - Contador: `count()`
    - Contenido del nodo: `text()`
    - Último elemento: `last()`

Ejemplo
```xml
<?xml version="1.0" encoding="UTF-8">
<class>
<name> L.1.1.1 </name>
<student id = "033">
    <name> Mark </name>
    <year> 2003 </year>
</student>
<student id = "003">
    <name> James </name>
    <year> 2001 </year>
</student>
</class>
```
- ¿Cuantos estudiantes hay? `count(//student)`
- ¿Nombre de los estudiantes que han nacido en 1999? `//student[year='1999']/name/text()``
- ¿Año de nacimiento de Ana?
    - `//student/name[contains(text(), 'Ana')]/../year/text()`
    - `//student/name[contains(., 'Ana')]/../year/text()`
    - `//student[last()]`

### Ejes de navegación

| Relación                  | Eje XPath usado     | XPath ejemplo                       | Qué selecciona                                                |
| ------------------------- | ------------------- | ----------------------------------- | ------------------------------------------------------------- |
| 🟩 **Padre**              | `parent`            | `//book/parent::catalog`            | El nodo `<catalog>` que contiene al `<book>`                  |
| 🟦 **Hermano anterior**   | `preceding-sibling` | `//book[2]/preceding-sibling::book` | El primer `<book>` si estás en el segundo                     |
| 🟪 **Hermano siguiente**  | `following-sibling` | `//book[1]/following-sibling::book` | El segundo `<book>` si estás en el primero                    |
| 🟨 **Descendientes**      | `descendant`        | `//book/descendant::*`              | Todos los hijos y nietos (por ejemplo, `<title>`, `<author>`) |
| 🟥 **Propio nodo (self)** | `self`              | `//book/self::book`                 | El mismo nodo `<book>`                                        |
