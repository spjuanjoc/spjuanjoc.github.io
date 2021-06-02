---
title           : "Operador nave espacial: de comparación de tres vías"
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
funcionalidad específica, como lo son las secciones de código de los distintos 
operadores de comparación.

Previo a `C++20` se puede encontrar código como el siguiente:
 
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

El código compila y funciona pero puede ser mucho más simple, además puede ser 
óptimo dejar que el compilador genere estos operadores automáticamente
para aprovechar funcionalidades adicionales de ordenamiento. 

## Comparación de tres vías

Al definir el operador de comparación de tres vías como predeterminado para que 
lo genere el compilador de manera automática, el código anterior es reducido a:

{% raw %}
````c++
struct S
{
  int value;

  // Operador nave espacial
  auto operator<=>(const S&) const = default;
};
````
{% endraw %}

El compilador genera todas las comparaciones por defecto.
El tipo de retorno puede ser `bool`, sin embargo es conveniente dejarlo como 
`auto` para tener en cuenta el ordenamiento, del cual hablo en la publicación
sobre [ordenamiento intuitivo]({{ site.baseurl }}{% link _posts/es/experto/2020-11-22-ordenamiento-intuitivo-rangos.md %}).

## Usos

¿Por qué definir las comparaciones para una estructura? 
Suponiendo que se quiere tener una colección de objetos de `S` que crezca de 
manera dinámica en memoria contigua, y que se puedan visualizar en la consola. 
En este caso se puede escoger un contenedor `std::vector` de la biblioteca `STL`.

Una de las condiciones para usar la biblioteca `STL` en una estructura
definida por el usuario es que sus objetos se puedan comparar, lo cual se logra
con el operador nave espacial.  
Crear un vector de `S` y visualizar sus elementos por medio de un 
_range-based for-loop_ es tan simple como: 

{% raw %}
````c++
#include <iostream>
#include <vector>

struct S 
{
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

El resultado es:

    vector de S: 3 2 1 42 

---
## Consideraciones

El operador de comparación de tres vías está disponible en:
- Estándar: `-std=C++20`, `-std=gnu++2a` o `-std=c++2a`, con biblioteca: `libstdc++ 2020`. 
- Versión del compilador: `gcc > '10.2'`

El código mostrado se puede compilar en [compiler-explorer](https://compiler-explorer.com/).

## Fuentes
- [Comparaciones por defecto](https://es.cppreference.com/w/cpp/language/default_comparisons)
- [C++ 20's Spaceship operator](https://devblogs.microsoft.com/cppblog/simplify-your-code-with-rocket-science-c20s-spaceship-operator/) por Cameron DaCamara.
- [Spaceship operator](https://blog.tartanllama.xyz/spaceship-operator/) por Sy Brand.
- [C++20 comparisons](https://blog.jetbrains.com/rscpp/2020/12/10/cpp20-comparisons/) por JetBrains
