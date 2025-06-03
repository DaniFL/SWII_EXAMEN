# Ejercicios de XML
## DTD Ejercicio 1 

**Crea un XML que sea válido para el siguiente DTD:**

```xml
<!DOCTYPE TVSCHEDULE [
<!ELEMENT TVSCHEDULE (CHANNEL+)>
<!ELEMENT CHANNEL (BANNER,DAY+)>
<!ELEMENT BANNER (#PCDATA)>
<!ELEMENT DAY (DATE,(HOLIDAY|PROGRAMSLOT+)+)>
<!ELEMENT HOLIDAY (#PCDATA)>
<!ELEMENT DATE (#PCDATA)>
<!ELEMENT PROGRAMSLOT (TIME,TITLE,DESCRIPTION?)>
<!ELEMENT TIME (#PCDATA)>
<!ELEMENT TITLE (#PCDATA)>
<!ELEMENT DESCRIPTION (#PCDATA)>

<!ATTLIST TVSCHEDULE NAME CDATA #REQUIRED>
<!ATTLIST CHANNEL CHAN CDATA #REQUIRED>
<!ATTLIST PROGRAMSLOT VTR CDATA #IMPLIED>
<!ATTLIST TITLE RATING CDATA #IMPLIED>
<!ATTLIST TITLE LANGUAGE CDATA #IMPLIED>
]>
```
### Solución:
**Resumen obligatorio y opcional**
| Elemento        | ¿Obligatorio? | Contenido o atributos obligatorios | Comentario                            |
|-----------------|---------------|------------------------------------|----------------------------------------|
| `TVSCHEDULE`    | ✅            | `NAME`                             | Raíz del XML                           |
| `CHANNEL`       | ✅            | `CHAN`                             | Hijo de `TVSCHEDULE`                   |
| `BANNER`        | ✅            | Texto                              | Hijo de `CHANNEL`                      |
| `DAY`           | ✅            | -                                  | Hijo de `CHANNEL`                      |
| `DATE`          | ✅            | Texto                              | Hijo de `DAY`                          |
| `HOLIDAY`       | ❌ (pero útil) | Texto                              | Puede repetirse dentro de `DAY`       |
| `PROGRAMSLOT`   | ✅ (en muchos casos) | `TIME`, `TITLE`               | Puede tener `VTR`, `DESCRIPTION`      |
| `TITLE`         | ✅            | Texto                              | Puede tener `RATING`, `LANGUAGE`      |
| `TIME`          | ✅            | Texto                              | Dentro de `PROGRAMSLOT`               |
| `DESCRIPTION`   | ❌            | Texto                              | Opcional en `PROGRAMSLOT`             |


```xml
<TVSCHEDULE NAME="Guía TV de España">
  <CHANNEL CHAN="Antena 3">
    <BANNER>¡Disfruta de tus programas favoritos en Antena 3!</BANNER>
    <DAY>
      <DATE>2025-04-29</DATE>
      <HOLIDAY>Día Mundial de la Danza</HOLIDAY>
      <PROGRAMSLOT VTR="false">
        <TIME>21:45</TIME>
        <TITLE RATING="TP" LANGUAGE="es">El Hormiguero</TITLE>
        <DESCRIPTION>Programa de entrevistas y humor con Pablo Motos. Invitado especial: Pedro Pascal.</DESCRIPTION>
      </PROGRAMSLOT>
      <PROGRAMSLOT>
        <TIME>23:00</TIME>
        <TITLE RATING="12" LANGUAGE="es">Cine de noche</TITLE>
        <DESCRIPTION>Película: "Origen", dirigida por Christopher Nolan.</DESCRIPTION>
      </PROGRAMSLOT>
    </DAY>
  </CHANNEL>
</TVSCHEDULE>
```

## DTD Ejercicio 2

Crea un DTD que valide el siguiente XML:

```xml
<articles>
  <article id="x34675">
    <name>Apache Spark Architecture</name>
    <month>december</month>
    <author name="kay vennisla"/>
    <reviews lang=""/>
    <feedback>high rating</feedback>
    <reviews lang="de">The best content with diagrams</reviews>
  </article>
</articles>
```

### Solución:

```dtd
<!ELEMENT articles (article+)>

<!ELEMENT article (name, month, author, reviews*, feedback?, reviews*)>
<!ATTLIST article id CDATA #REQUIRED>

<!ELEMENT name (#PCDATA)>
<!ELEMENT month (#PCDATA)>
<!ELEMENT author EMPTY>
<!ATTLIST author name CDATA #REQUIRED>

<!ELEMENT reviews (#PCDATA)>
<!ATTLIST reviews lang CDATA #REQUIRED>

<!ELEMENT feedback (#PCDATA)>
```

#### Notas:
- Se permite que el elemento `<reviews>` aparezca múltiples veces (por eso `reviews*`).
- El atributo `lang` en `<reviews>` es obligatorio, aunque pueda estar vacío.
- El elemento `<author>` es vacío (`EMPTY`) porque no tiene contenido textual ni hijos, solo atributos.
- El orden de los elementos dentro de `<article>` es importante y está reflejado exactamente como en el XML dado.
reviews* (asterisco):

(Importante para el estudio):\
**"*"**: El elemento <reviews> puede aparecer cero o más veces.\
**"?"**: El elemento <feedback> puede aparecer cero o una vez.
