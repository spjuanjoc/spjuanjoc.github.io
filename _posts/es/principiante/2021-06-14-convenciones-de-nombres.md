---
title           : "Convenciones de nombres"
date            : 2021-07-05
last_modified_at: 2021-07-15

categories:
  - Principiante
tags:
  - C++

excerpt: "La decisión de usar una convención suele acordarse para un proyecto o
          equipo. Lo más importante al elegir una es que sea consistente a 
          través de todo el código del proyecto, independientemente de las 
          notaciones elegidas a usar."

header:
  og_image      : /assets/images/unsplash-that-s-her-business-scrabble.jpg
  teaser        : /assets/images/unsplash-that-s-her-business-scrabble.jpg
  overlay_image : /assets/images/unsplash-that-s-her-business-scrabble.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**That's Her Business**](https://unsplash.com/@thatsherbusiness?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Las convenciones de nombres son reglas para nombrar los componentes de un 
proyecto, tales como carpetas, archivos, estructuras, variables, entre otros.
Estas reglas pueden seguir diferentes notaciones para cada componente.

Las notaciones hacen referencia a la manera de nombrar los conjuntos de
componentes, como los identificadores de variables, tipos, funciones, en el
código fuente. Todo el conjunto de componentes debe seguir una única notación:

En _C++_ se suele limitar estos identificadores a:

- Las letras del abecedario en inglés (aA - zZ ASCII)
- Números (0-9)
- Guion bajo "_"

Las distintas combinaciones forman las siguientes notaciones:

| Notación  | Ejemplo |
| ---       | ---     |
| Upper     |  _NOTACIONMAYUSCULAS_   |
| Lower     |  _notacionminusculas_   |
| Pascal    |  _NotacionPascal_       |
| camel     |  _notacionCamel_        |
| snake     |  _notacion_snake_       |
| kebab     |  _notacion-kebab_       |
| screaming/upper snake | _SCREAMING_SNAKE_  |
| screaming/upper kebab | _UPPER-KEBAB_      |

A partir de las notaciones se definen convenciones para su uso. 

NOTA: Las notaciones kebab en C++ solo se pueden usar en los nombres de archivos
y directorios, no dentro del código fuente.


## Notaciones reservadas

Los identificadores de la biblioteca estándar suelen estar en minúscula y
sin separadores; así son los tipos de datos, y algunas funcionalidades y
algoritmos:

```c++
std::vector
std::begin
std::end
std::replace
std::setlocale
std::cout
std::isupper
std::islower
std::isdigit
std::ispunct
std::isspace
```

Las variables que usan doble guion bajo también suelen ser reservadas, por lo
cual las estructuras de usuario no deben usar como identificadores las variables
que comiencen o terminen con doble guion bajo, pues pueden causar colisiones.


## Descripción de notaciones

- PascalCase: Consiste en que todas las palabras que forman el nombre tienen su
  primera letra en mayúscula y las demás en minúscula: 
  _IdentificadorConMultiplesPalabras_

- camelCase: Consiste en que todas las palabras que forman el nombre tienen su
  primera letra en mayúscula y las demás en minúscula, excepto la primera 
  palabra: _identificadorConMultiplesPalabras_

- snake_case: Todas las palabras que forman el nombre están en minúsculas, y
  separadas por un guion bajo: _identificador_con_multiples_palabras_

- SCREAMING_SNAKE_CASE: Similar al snake_case, todas las palabras están
  separadas por un guion bajo, pero en mayúscula sostenida. También se conoce
  como UPPER_SNAKE_CASE: _IDENTIFICADOR_CON_MULTIPLES_PALABRAS_

- kebab-case: Como con snake, puede estar en minúsculas o mayúsculas
  sostenidas (SCREAMING-KEBAB-CASE), y las palabras se separan por un guion
  medio: _IDENTIFICADOR-CON-MULTIPLES-PALABRAS_


## Acuerdos de uso de convenciones

La decisión de usar una convención suele acordarse para un proyecto o equipo. Lo
más importante al elegir una es que sea consistente a través de todo el código
del proyecto, independientemente de las notaciones elegidas a usar. Es por eso 
que en algunos proyectos se siguen apreciando notaciones como la Húngara.  
Por ejemplo, es posible tomar la decisión de que el proyecto _HolaMundo_ debe
seguir las siguientes:

- Los nombres de estructuras usan la notación _PascalCase_.
- Las funciones miembro o libres usan _camelCase_.
- Los nombres de variables usan _snake_case_.
- Las constantes usan _UPPER_SNAKE_CASE_.
- Los directorios usan _PascalCase_.

También es recomendable no mezclar notaciones; por ejemplo, está desestimado
combinar Pascal con snake, formando _Notacion_Pascal_Snake_.


## Fuentes
- wiki:[Convenciones de nombres](https://es.wikipedia.org/wiki/Convenci%C3%B3n_de_nombres_(programaci%C3%B3n))
- Better programming: [String case styles](https://betterprogramming.pub/string-case-styles-camel-pascal-snake-and-kebab-case-981407998841)
- Neoguías: [Tipos de notaciones de nombres](https://www.neoguias.com/tipos-notacion-nombres/)
- Kyocode: [Notación pascal y camel](https://www.kyocode.com/2018/06/notacion-pascal-y-camel-case/)
