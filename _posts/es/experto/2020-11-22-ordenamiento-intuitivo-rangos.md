---
title           : "Ordenamiento intuitivo con Rangos"
date            : 2020-11-22
last_modified_at: 2021-03-22

categories:
  - Experto
tags:
  - C++20
  - Rangos
  - std::ranges
  - std::ranges::sort
  - std::ranges::views
  - Nave espacial


excerpt: "La biblioteca de rangos incluida en `C++20` trae finalmente una
          funcionalidad con la cual realizar ordenamientos en contenedores 
          secuenciales de la STL de una manera intuitiva y fácil de comprender."
header:
  og_image      : /assets/images/unsplash-fleur-scales.jpg
  teaser        : /assets/images/unsplash-fleur-scales.jpg
  overlay_image : /assets/images/unsplash-fleur-scales.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Fleur**](https://unsplash.com/@yer_a_wizard?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Una forma sencilla de ordenar un contenedor secuencial, siempre y cuando éste
soporte iteradores de acceso aleatorio, es por medio del algoritmo `std::sort`.
Para contenedores como `std::array`, `std::deque`, y `std::vector`, usando el
método de comparación predefinida, el ordenamiento consiste en  
indicarle al algoritmo dónde comienza y dónde termina el contenedor por medio
de iteradores:

```c++
  std::sort(std::begin(contenedor), std::end(contenedor));
```

Se pueden usar los iteradores inversos para que el ordenamiento sea de manera
descendiente:

```c++
  std::sort(std::rbegin(contenedor), std::rend(contenedor));
```

Los contenedores secuenciales `std::list` y `std::forward_list` no soportan 
iteradores de acceso aleatorio por lo cual tienen definidos sus propios 
algoritmos de ordenamiento.

## Biblioteca de Rangos

Esta biblioteca se puede entender como una versión avanzada de la biblioteca de
plantillas (`STL`). Aunque no es 100 % compatible con las versiones anteriores
contiene una cantidad importante de nuevas características, y la mayoría de
ellas están en un _namespace_ independiente: `std::ranges`, lo que implica que
el código anterior a esta biblioteca no se verá afectado.

Un rango es un intervalo de datos; en `C++` se puede ver como un objeto
construido sobre iteradores. Un contenedor puede almacenar un conjunto de
valores, entonces un contenedor puede ser visto como un rango almacenado; así,
por ejemplo un `std::vector` será tratado como un rango. La biblioteca de Rangos
opera sobre rangos, claramente, entonces un algoritmo de esta biblioteca podrá
operar sobre un `std::vector`.

Los rangos típicamente son de tipo de entrada (solo lectura), salida (escritura)
, o de entrada/salida (lectura y escritura).  
Los más conocidos son los contenedores: son propietarios de sus
elementos. Con esta biblioteca también aparecen las Vistas (_views_) que son
rangos que no tienen propiedad sobre los elementos.  
La biblioteca abarca temas relacionados con Conceptos, Algoritmos optimizados,
Centinelas, Proyecciones, y Vistas.

## Ordenamiento de rangos

Con la biblioteca de rangos el ordenamiento es de una manera mucho más
intuitiva: no se le pasan iteradores, sino que se le indica que se va a «ordenar
el siguiente rango»:

```c++
  std::ranges::sort(rango);
```

Y como un `std::vector` es un rango, entonces:

```c++
  std::vector vec{5,4,2,3,1};
  std::ranges::sort(vec);
```

`vec` ahora está ordenado de manera ascendente: `1,2,3,4,5`. 

El algoritmo `std::sort` se encuentra en el archivo de cabecera `algorithm`. 

La restricción sobre los iteradores de acceso aleatorio se mantiene;
adicionalmente los mensajes de error de compilación al intentar usar este
algoritmo sobre un rango que no los soporta son muchísimo más claros que los de
la `STL`:

Teniendo:
```c++
  std::list lista{5,4,3,2,1};
  std::ranges::sort(lista);
```

Al intentar compilar saldrá un mensaje de error similar al siguiente:
```
<source>: In function 'int main()':
<source>:19:22: error: 
  no match for call to '(const std::ranges::__sort_fn) (std::__cxx11::list<int>&)'
   19 |   std::ranges::sort(lista);
      |                          ^
In file included from /include/c++/11.0.0/algorithm:64,
                 from <source>:1:
/include/c++/11.0.0/bits/ranges_algo.h:2019:7: 
  note: candidate: 'template<...>  requires (random_access_iterator<_Iter>) && ...'
 2019 |       operator()(...,
      |       ^~~~~~~~
/include/c++/11.0.0/bits/ranges_algo.h:2019:7: 
  note:   template argument deduction/substitution failed:
<source>:19:22: note:   candidate expects 4 arguments, 1 provided
   19 |   std::ranges::sort(lista);
      |                          ^
In file included from /include/c++/11.0.0/algorithm:64,
                 from <source>:1:
/include/c++/11.0.0/bits/ranges_algo.h:2032:7: 
  note: candidate: 'template<...>  requires (random_access_range<_Range>) && ...'
 2032 |       operator()(...) const
      |       ^~~~~~~~
```

La parte del mensaje que indica que no se cumplen las condiciones para el uso
del algoritmo es:

    requires (random_access_iterator<_Iter>)

Son unas 20 líneas de mensajes de error. En contraste, con la STL pueden llegar
a verse unas 442 líneas de mensajes de error.

## Rangos de contenedores de estructuras personalizadas

Para una estructura personalizada hace falta generarle operadores relacionales
consistentes, lo cual requiere definir el operador de comparación de tres vías
predeterminado, conocido como
[operador «nave espacial»]({{ site.baseurl }}{% link _posts/es/avanzado/2020-10-31-operador-nave-espacial.md %}).

{% raw %}
```c++
#include <iostream>
#include <vector>
#include <algorithm>

struct Persona
{
  unsigned    edad;
  std::string nombre;

  // Operadores relacionales predeterminados: nave espacial
  auto operator<=>(const Persona&) const = default; 
};

int main()
{
  std::vector<Persona> personas{{4, "Juan"}, {2, "Ana"}, {42, "Juana"}};
  std::ranges::sort(personas);

  for (const auto it: personas)
  {
    std::cout << it.nombre << " : " << it.edad << '\n';
  }

  return 0;
}
```
{% endraw %}

El resultado del ordenamiento es primero por edad y luego por nombre:

    Juana : 42
    Juan  : 4
    Ana   : 2

Si todos tuvieran la misma edad entonces el orden se da por el nombre:  

    Ana   : 30
    Juan  : 30
    Juana : 30


## Rangos de Vistas

A partir del uso de rangos se pueden utilizar funcionalidades más complejas de
manera simple, tales como las vistas (_views_). Por ejemplo para ordenar el
rango de manera inversa:

```c++
#include <ranges>
//...

  std::ranges::sort(std::ranges::views::reverse(personas));

```

También se puede reordenar directamente sobre el _for loop_ con el operador
_pipe_:

```c++
#include <ranges>
//...

  for (const auto& it: personas | std::ranges::views::reverse)
  {
    std::cout << it.nombre << " : " << it.edad << '\n';
  }
```


---
## Consideraciones

La biblioteca de rangos está disponible en:

- Estándar: `-std=c++20`, `-std=gnu++2a` o `-std=c++2a`, con
  biblioteca: `libstdc++ 2020`.
- Versión del compilador: `gcc > '10.2'`
- Como biblioteca de terceros en Range-V3.

## Fuentes

- Hannes Hauswedell: [A beginner's guide to C++ Ranges](https://hannes.hauswedell.net/post/2019/11/30/range_intro/)
- Eric Niebler: [Range-V3](https://ericniebler.github.io/range-v3/)
- Tristan Brindle: [An Overview of Standard Ranges](https://youtu.be/SYLgG7Q5Zws) - CppCon 2019.
- Geeks for geeks: [Iteradores de Acceso aleatorio](https://www.geeksforgeeks.org/random-access-iterators-in-cpp/)
- CppReference: [Rangos](https://es.cppreference.com/w/cpp/ranges)
