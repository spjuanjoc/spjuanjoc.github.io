---
title           : "Ordenamiento intuitivo"
date            : 2020-11-22
last_modified_at: 2020-11-23

categories:
  - Experto
tags:
  - C++20
  - Rangos

excerpt: "La biblioteca de rangos incluida en C++20 trae finalmente 
una manera intuitiva y fácil de comprender para realizar ordenamientos 
en contenedores secuenciales de la STL."
header:
  teaser  : /assets/images/unsplash-fleur-scales.jpg

---

Una forma sencilla de ordenar un contenedor secuencial, siempre y cuando éste 
soporte iteradores de acceso aleatorio, es por medio del algoritmo `std::sort`. 
Para contenedores como `std::array`, `std::deque`, y `std::vector`,
usando el método de comparación predefinida, el ordenamiento es tan simple como 
indicarle al algoritmo dónde comienza y dónde termina el contenedor:

```c++
  std::sort(std::begin(contenedor), std::end(contenedor));
```

Los contenedores secuenciales `std::list` y `std::forward_list` no soportan 
iteradores de acceso aleatorio, por lo cual tienen definidos sus propios 
algoritmos de ordenamiento.

## Biblioteca de Rangos

Esta biblioteca se puede entender como una versión avanzada de la biblioteca
de plantillas (`STL`). Aunque no es `100 %` compatible con las versiones anteriores
contiene una cantidad importante de nuevas características, y la mayoría de ellas
están en un _namespace_ independiente: `std::ranges`, lo que implica que el código
anterior a esta biblioteca no se verá afectado.

Un rango es un intervalo de datos; en `C++` se puede ver como un objeto 
construido sobre iteradores. Un contenedor puede almacenar un conjunto de valores, 
entonces un contenedor puede ser visto como un rango almacenado; así, por ejemplo
un `std::vector` será tratado como un rango. La biblioteca de Rangos opera sobre
rangos, claramente, entonces un algoritmo de esta biblioteca podrá operar sobre 
un `std::vector`.

La biblioteca abarca temas relacionados con Conceptos, Algoritmos optimizados, 
Centinelas, Proyecciones, y Vistas.

## Ordenamiento de rangos

Con la biblioteca de rangos el ordenamiento es de una manera mucho más intuitiva:
«voy a ordenar el siguiente rango»:

```c++
  std::ranges::sort(rango);
```

Y como un `std::vector` es un rango, entonces:

```c++
  std::vector vec{5,4,3,2,1};
  std::ranges::sort(vec);
```

La restricción sobre los iteradores de acceso aleatorio se mantiene; adicionalmente
los mensajes de error de compilación al intentar usar este algoritmo sobre un 
rango que no los soporta son muchísimo más claros que los de la `STL`:

Teniendo:
```c++
  std::list lista{5,4,3,2,1};
  std::ranges::sort(lista);
```

Al intentar compilar saldrá un mensaje de error similar al siguiente:
```c++
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


## Rangos en contenedores de estructuras personalizadas

Para una estructura personalizada hará falta generarle operadores relacionales
consistentes, lo cual es tan sencillo como definir el operador de comparación 
de tres vías predeterminado, conocido como operador 
«nave espacial», del cual hice una publicación [aquí]({{ site.baseurl }}{% link _posts/es/avanzado/2020-10-31-operador-nave-espacial.md %}).

{% raw %}
```c++
struct Persona
{
  unsigned    edad;
  std::string nombre;

  auto operator<=>(const Persona&) const = default;
};

int main()
{
  std::vector<Persona> personas{{30, "Juan"}, {20, "Sofía"}, {42, "Ana"}};
  std::ranges::sort(personas);

  return 0;
}
```
{% endraw %}

El resultado del ordenamiento será primero por edad y luego por nombre:

    Sofía (20)
    Juan  (30)
    Ana   (42)

---
## Consideraciones

La biblioteca de rangos está disponible en:
- Estándar: `-std=C++20`, `-std=gnu++2a` o `-std=c++2a`, con biblioteca: `libstdc++ 2020`. 
- Versión del compilador: `gcc > '10.2'`
- Como terceros en Range-V3.

## Fuentes
- Hannes Hauswedell: [A beginner's guide to C++ Ranges](https://hannes.hauswedell.net/post/2019/11/30/range_intro/)
- Eric Niebler: [Range-V3](https://ericniebler.github.io/range-v3/)
- Tristan Brindle: [An Overview of Standard Ranges](https://youtu.be/SYLgG7Q5Zws) - CppCon 2019.
- Geeks for geeks: [Iteradores de Acceso aleatorio](https://www.geeksforgeeks.org/random-access-iterators-in-cpp/)
- CppReference: [Rangos](https://es.cppreference.com/w/cpp/ranges)
