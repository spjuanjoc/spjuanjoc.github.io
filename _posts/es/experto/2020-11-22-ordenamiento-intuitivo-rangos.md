---
title           : "Ordenamiento intuitivo con la biblioteca de Rangos"
date            : 2020-11-22
last_modified_at: 2020-11-23

categories:
  - Experto
tags:
  - C++20
  - Rangos

excerpt: "La biblioteca de rangos incluida en C++20 trae finalmente 
una manera intuitiva y fácil de comprender para realizar ordenamientos 
en contenedores de la STL."
header:
  teaser  : /assets/images/unsplash-fleur-scales.jpg

---

Una forma sencilla de ordenar un contenedor, siempre y cuando éste 
soporte iteradores de acceso aleatorio, es por medio del algoritmo `std::sort`. 
Usando el método de comparación predefinida el ordenamiento es tan simple como 
indicarle al algoritmo dónde comienza y dónde termina el contenedor:

```c++
  std::sort(std::begin(contenedor), std::end(contenedor));
```

Los contenedores `std::list` y `std::forward_list` no soportan iteradores de acceso
aleatorio, por lo cual tienen definidos sus propios algoritmos de ordenamiento.

## Biblioteca de Rangos

Esta biblioteca se puede entender como una versión avanzada de la biblioteca
de plantillas (`STL`). Aunque no es 100 % compatible con las versiones anteriores
contiene una cantidad importante de nuevas características, y la mayoría de ellas
están en un _namespace_ independiente: `std::ranges`, lo que implica que el código
anterior a esta biblioteca no se verá afectado.

Un rango es un intervalo de datos; en `C++` se puede ver como un objeto 
construido sobre iteradores. Un contenedor puede almacenar un conjunto de valores, 
entonces un contenedor puede ser visto como un rango almacenado; así, por ejemplo
un `std::vector` será tratado como un rango. La biblioteca de Rangos opera sobre
rangos, claramente, entonces un algoritmo de esta biblioteca podrá operar sobre 
un `std::vector`.

La biblioteca abarca los siguientes temas:  
- Conceptos
- Algoritmos optimizados
- Centinelas
- Proyecciones
- Vistas

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

Al intentar compilar saldrá un mensaje de error que contiene:
```c++
.../include/c++/11.0.0/algorithm:64,
  from <source>:1:
.../c++/11.0.0/bits/ranges_algo.h:2019:7: note: candidate: 
  template<class _Iter, class _Sent, class _Comp, class _Proj>  
   requires (random_access_iterator<_Iter>) 
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

Todo el código se puede compilar en [godbolt.org](https://godbolt.org/).

## Fuentes
- Hannes Hauswedell: [A beginner's guide to C++ Ranges](https://hannes.hauswedell.net/post/2019/11/30/range_intro/)
- Eric Niebler: [Range-V3](https://ericniebler.github.io/range-v3/)
- Tristan Brindle: [An Overview of Standard Ranges](https://youtu.be/SYLgG7Q5Zws) - CppCon 2019.
- Geeks for geeks: [Iteradores de Acceso aleatorio](https://www.geeksforgeeks.org/random-access-iterators-in-cpp/)
- CppReference: [Rangos](https://es.cppreference.com/w/cpp/ranges)
