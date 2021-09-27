---
title           : "Funciones Miembro Especiales"
date            : 2021-09-22
last_modified_at: 2021-09-22

categories:
  - Intermedio
tags:
  - C++

excerpt: "Son aquellas funciones que bajo ciertas circunstancias se definen 
          automáticamente por el compilador aun si no son declaradas por el 
          usuario."

header:
  og_image      : /assets/images/unsplash-clay-banks-hojas.jpg
  teaser        : /assets/images/unsplash-clay-banks-hojas.jpg
  overlay_image : /assets/images/unsplash-clay-banks-hojas.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Clay Banks**](https://unsplash.com/@claybanks?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---


Las funciones miembro especiales son aquellas funciones no estáticas
especificadas dentro de una clase, que bajo ciertas circunstancias se definen
automáticamente por el compilador aun si no son declaradas por el usuario.

Para C++98 se pueden contar cuatro funciones miembro especiales: el constructor
por defecto, el destructor, el constructor de copia, y el operador de asignación
de copia. Estas funciones solo van a ser generadas si son requeridas; por
ejemplo, si una clase especifica un constructor con parámetros entonces no se
genera uno por defecto; de no declararse constructor alguno el compilador lo
genera de manera predeterminada.  
Desde C++11 están disponibles dos miembros especiales adicionales: el
constructor de movimiento, y el operador de asignación de movimiento.[^item17]


## Operaciones de movimiento

Para la copia, si se declara el constructor de copia, pero no el operador de
asignación de copia el compilador lo puede generar automáticamente siempre y
cuando se use en alguna parte del código. Para el movimiento es diferente, si se
declara solamente uno, el otro no es generado de manera automática pues el
compilador interpreta que las operaciones de movimiento para esa estructura no 
son triviales. Si se declara alguna operación de copia (construcción o
asignación) tampoco se generan las operaciones de movimiento. Adicionalmente,
declarar operaciones de movimiento y no hacerlo con las de copia causa que el
compilador deshabilite las operaciones de copia. Para que el compilador genere
las operaciones de movimiento (si son requeridas en algún lugar del código) en
la clase no puede haber declarado un destructor, ni operaciones de copia, ni
alguna operación de movimiento.


## Regla de Cinco

Lo anterior tiene repercusiones en la conocida Regla de Tres, que a partir de
C++11 se extiende para ser la Regla de Cinco: si se declara uno de las
siguientes cinco funciones miembro especiales se deben declarar todas (o ninguna
para la Regla del Cero): Destructor, constructor de copia, constructor de
movimiento, operador de asignación de copia, operador de asignación de
movimiento.[^ruleofthree]


## Default

Teniendo en cuenta la Regla de Cinco, la manera de indicarle al compilador que
genere las demás funciones cuando una es declarada (e.g. cuando el destructor es
virtual) es usando la palabra clave `default` así:

```c++
class Base
{
public:
  Base() = default;  // Constructor
  virtual ~Base() = default;  // Destructor

  // Operaciones de copia
  Base(const Base&) = default;
  Base& operator=(const Base&) = default;

  // Operaciones de movimiento
  Base(Base&&) = default;
  Base& operator=(Base&&) = default;
};
```


## No copiable

La expresión _no-copiable_ (_non-copyable idiom_[^noncopyable]) se refiere a
estructuras que tienen deshabilitadas las operaciones de copia. Previo a C++11
esta expresión indicaba que las funciones miembro especiales de copia se
declaraban privadas y se dejaban sin definir. Fue un a expresión ampliamente
usada, pero presentaba algunos inconvenientes; uno de ellos es que al intentar
usar una operación de copia que no está permitida solamente se va a presentar un
error en tiempo de _Enlace_, y no en tiempo de _Compilación_. De manera similar
se define `boost::noncopiable` que es una clase dentro de las bibliotecas de
Boost.[^boost]

Desde C++11 la forma correcta de deshabilitar las operaciones de copia en 
una estructura es por medio de la palabra clave `delete`, definiendo
el constructor de copia y el operador de asignación de copia como _borrados_
y con acceso público.[^item12] De esta forma el compilador puede detectar si se
está intentando hacer uso de la copia, y de ser así genera un error de
compilación.


## Delete

De manera similar al uso de `default`, para indicarle explícitamente que
deshabilite determinadas operaciones se usa la palabra clave `delete`. Esta no
se encuentra limitada a las funciones miembro especiales, sino que puede ser
usada sobre cualquier función (no necesariamente miembro). Para hacer una
clase _no-copiable_ usando `delete`, y siguiendo la Regla de Cinco, se procede 
así:

```c++
class Base
{
public:
  Base() = default;
  virtual ~Base() = default;

  // Operaciones de copia deshabilitadas
  Base(const Base&) = delete;
  Base& operator=(const Base&) = delete;

  // Operaciones de movimiento 
  Base(Base&&) = default;
  Base& operator=(Base&&) = default;
};
```

Deshabilitar la copia y habilitar el movimiento es el caso del `std::unique_ptr`
y del `std::thread`.


## Fuentes

- Funciones Miembro Especiales [cppreference](https://es.cppreference.com/w/cpp/language/member_functions)
- Scott Meyers - Effective Modern C++ 

---
[^item17]: Item 17: Scott Meyers - _Effective Modern C++_
[^item12]: Item 12: Scott Meyers - _Effective Modern C++_
[^noncopyable]: Ver [Non copyable](https://dev-faqs.blogspot.com/2010/07/c-idioms-non-copyable.html)
[^boost]: Ver [boost::noncopyable](https://www.boost.org/doc/libs/master/libs/core/doc/html/core/noncopyable.html)
[^ruleofthree]: [La Regla de Tres/Cinco/Cero](https://es.cppreference.com/w/cpp/language/rule_of_three)
