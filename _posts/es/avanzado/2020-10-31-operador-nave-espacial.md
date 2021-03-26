---
title           : "Operador «nave espacial»"
date            : 2020-10-31
last_modified_at: 2020-12-14

categories:
  - Avanzado
tags:
  - C++20
  - STL

excerpt: "Nave espacial es el nombre con el que se conoce al operador `<=>` 
          introducido en el estándar `C++20`; hace referencia al operador de 
          comparación de tres vías."
header:
  og_image      : /assets/images/unsplash-john-baker-rocket.jpg
  teaser        : /assets/images/unsplash-john-baker-rocket.jpg
  overlay_image : /assets/images/unsplash-john-baker-rocket.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**John Baker**](https://unsplash.com/@jlondonbaker?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Nave espacial es el nombre con el que se conoce al `operator<=>` introducido
en el estándar `C++20`; hace referencia al operador de comparación de tres vías
que es usado para: primero comparar dos objetos, y luego comparar ese resultado 
con cero.
Una de sus principales ventajas es que reduce el código _boilerplate_: secciones 
de código que se repiten en varias partes y son requeridas para una 
funcionalidad específica.

Previo a `C++20` se podría encontrar código como el siguiente:
 
{% raw %}
````c++
struct S 
{
  int value;

  // Operadores de comparación
  bool operator==(const S& rhs) const { return value == rhs.value; }
  bool operator!=(const S& rhs) const { return !(*this == rhs);    }
  bool operator<(const S& rhs)  const { return value < rhs.value;  }
  bool operator<=(const S& rhs) const { return !(rhs < *this);     }
  bool operator>(const S& rhs)  const { return rhs < *this;        }
  bool operator>=(const S& rhs) const { return !(*this < rhs);     }
};
````
{% endraw %}

Allí se definen las comparaciones requeridas para una nueva estructura `S`.

El código compila y funciona pero podría ser mucho más simple, además sería 
óptimo dejar que el compilador generara estos operadores automáticamente
para aprovechar funcionalidades adicionales de ordenamiento. 

## Comparación de tres vías

Al definir el operador de comparación de tres vías como predeterminado para que 
lo genere el compilador de manera automática, el código anterior será reducido a:

{% raw %}
````c++
struct S {
  int value;

  // Operador nave espacial
  auto operator<=>(const S&) const = default;
};
````
{% endraw %}

El compilador generará todas las comparaciones por defecto.
El tipo de retorno podría ser `bool`, sin embargo es conveniente dejarlo como 
`auto` para tener en cuenta el ordenamiento, del cual hablo en la publicación
sobre [ordenamiento intuitivo]({{ site.baseurl }}{% link _posts/es/experto/2020-11-22-ordenamiento-intuitivo-rangos.md %}).

## Usos

Uno podría preguntarse ¿Por qué definir las comparaciones para una estructura? 
Supongamos que se quiere tener una colección de objetos de `S` que crezca de 
manera dinámica en memoria contigua, y que se puedan visualizar en la consola. 
En este caso se podría escojer un contenedor `std::vector` de la biblioteca `STL`.

Pues bien, una de las condiciones para usar la biblioteca `STL` en una estructura
definida por el usuario es que sus objetos se puedan comparar, lo cual se logra
con el operador nave espacial.  
Crear un vector de `S` y visualizar sus elementos por medio de un 
_range-based for-loop_ será tan simple como: 

{% raw %}
````c++
#include <iostream>
#include <vector>

struct S {
  int value;
  auto operator<=>(const S&) const = default;
};

int main()
{
  std::vector<S> vec{{3}, {2}, {1}, {42}};
  std::cout << "vector de S: ";
  for (auto it : vec)
  {
    std::cout << it.value << ' ';
  }
  return 0;
}
````
{% endraw %}

El resultado será:

    vector de S: 3 2 1 42 

---
## Consideraciones

El operador de comparación de tres vías está disponible en:
- Estándar: `-std=C++20`, `-std=gnu++2a` o `-std=c++2a`, con biblioteca: `libstdc++ 2020`. 
- Versión del compilador: `gcc > '10.2'`

Todo el código se puede compilar en [godbolt.org](https://godbolt.org/).

## Fuentes
- [Comparaciones por defecto](https://es.cppreference.com/w/cpp/language/default_comparisons)
- [C++ 20's Spaceship operator](https://devblogs.microsoft.com/cppblog/simplify-your-code-with-rocket-science-c20s-spaceship-operator/) por Cameron DaCamara.
- [Spaceship operator](https://blog.tartanllama.xyz/spaceship-operator/) por Sy Brand.
- [C++20 comparisons](https://blog.jetbrains.com/rscpp/2020/12/10/cpp20-comparisons/) por JetBrains
