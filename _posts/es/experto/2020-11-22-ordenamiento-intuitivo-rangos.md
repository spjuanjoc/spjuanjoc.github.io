---
title           : "Ordenamiento intuitivo con la biblioteca de Rangos"
date            : 2020-11-22
last_modified_at: 2020-11-22

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

## Algoritmos de ordenamiento: std::sort 

La forma C++17 de ordenar un contenedor, siempre y cuando éste soporte iteradores
de acceso aleatorio, es por medio del algoritmo `std::sort`. 
Los contenedores `std::list` y `std::forward_list` no soportan iteradores de acceso
aleatorio, por lo cual tienen definidos sus propios algoritmos de ordenamiento.  
Para los contenedores que sí lo soportan, y usando la comparación predefinida, 
el ordenamiento es tan simple como indicarle al algoritmo donde comienza y donde
termina el contenedor:

```c++
    std::sort(std::begin(contenedor), std::end(contenedor));
```

## Introducción a Rangos

La biblioteca de Rangos se puede entender como una versión avanzada de la biblioteca
de plantillas (STL). Sus componentes principales son:  
- Conceptos
- Algoritmos optimizados
- Centinelas
- Proyecciones
- Vistas

## Ordenamiento en rangos

Con la biblioteca de rangos el ordenamiento es de una manera mucho más intuitiva:
«voy a ordenar mi contenedor»:

```c++
    std::ranges::sort(contenedor);
```

La restricción sobre los iteradores de acceso aleatorio se mantiene; adicionalmente
los mensajes de error de compilación al intentar usar este algoritmo sobre un contenedor sin soporte
son muchísimo más claros:

```c++
.../include/c++/11.0.0/algorithm:64,
  from <source>:1:
.../c++/11.0.0/bits/ranges_algo.h:2019:7: note: candidate: 
  template<class _Iter, class _Sent, class _Comp, class _Proj>  
   requires (random_access_iterator<_Iter>) 
```

Gracias a los requerimientos de Conceptos es claro que no se cumplen las 
condiciones para el uso del algoritmo: 

    requires (random_access_iterator<_Iter>)


## Rangos en contenedores de estructuras personalizadas

Para una estructura personalizada hará falta definir sus comparadores y su 
soporte a los distintos iteradores; aunque parece una tarea dispendiosa, es tan
sencillo como definir el operador de comparación de tres vías, o 
[«nave espacial»]({{ site.baseurl }}{% link _posts/es/avanzado/2020-10-31-operador-nave-espacial.md %}).

{% raw %}
```c++
struct Persona
{
    int edad;
    std::string nombre;

    auto operator<=>(const Persona&) const = default;
};
//...
int main()
{
    std::vector<Persona> personas{{30, "Juan"}, {20, "Sofía"}};
    std::ranges::sort(personas);
}
```
{% endraw %}

El resultado del ordenamiento será primero por edad y luego por nombre:

    Sofía (20)
    Juan  (30)

---
## Consideraciones

La biblioteca de rangos está disponible en:
- Estándar: `-std=C++20`, `-std=gnu++2a` o `-std=c++2a`, con biblioteca: `libstdc++ 2020`. 
- Versión del compilador: `gcc > '10.2'`

Todo el código se puede compilar en [godbolt.org](https://godbolt.org/).

## Fuentes
- CppReference: [Rangos](https://es.cppreference.com/w/cpp/ranges)
- Eric Niebler: [Range-V3](https://ericniebler.github.io/range-v3/)
- Tristan Brindle: [An Overview of Standard Ranges](https://youtu.be/SYLgG7Q5Zws) - CppCon 2019.
- Geeks for geeks: [Iteradores de Acceso aleatorio](https://www.geeksforgeeks.org/random-access-iterators-in-cpp/)
