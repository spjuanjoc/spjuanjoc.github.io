---
title           : "Tamaño de objetos invocables"
date            : 2021-12-29
last_modified_at: 2022-01-06

categories:
  - Intermedio
tags:
  - Lambda
  - Funtor
  - Puntero a función
  - std::function

excerpt: "Todo objeto que pueda ser invocado usando la sintaxis de llamado a
          función es considerado un objeto invocable. Por ejemplo: Punteros a 
          funciones (miembro, y no-miembro), _Funtores_, y desde C++11 
          expresiones lambda, y objetos de tipo `std::function`."

header:
  og_image      : /assets/images/unsplash-nick-fewings-telephonebooth.jpg
  teaser        : /assets/images/unsplash-nick-fewings-telephonebooth.jpg
  overlay_image : /assets/images/unsplash-nick-fewings-telephonebooth.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Nick Fewings**](https://unsplash.com/@jannerboy62?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"
  
---


Los objetos invocables (_callables_) se refiere aquellos elementos tipo objeto 
que pueden ser llamados desde el código usando algún tipo de sintaxis de llamada
a función. Se distinguen de las funciones regulares en que son de tipo objeto 
(instancias), no son de tipo función. Una función regular no es un objeto, ni 
es de tipo objeto. Por ejemplo, no se puede usar el operador `sizeof` sobre una
función regular[^sizeof].

Todo objeto que pueda ser invocado usando la sintaxis de llamado a función es
considerado un objeto invocable. Por ejemplo: Punteros a funciones (miembro, 
y no-miembro), _Funtores_ (un tipo clase-objeto-función: instancia de una clase
que tiene sobrecargado el operador de ejecución `operator ()`). En pocas
palabras, es un objeto que actúa como una función. Un uso importante de los
objetos invocables es poder ser llamados en algoritmos.


## Invocables en C++ moderno

Previo a C++11 los objetos invocables tenían ciertas limitaciones, era bastante
complejo declarar uno en ámbitos locales, y la sintaxis podría llegar a ser 
confusa, especialmente para punteros a funciones.

Con la llegada de las expresiones lambda aumentaron las facilidades al hacer
uso de este tipo de objetos. También se agregó `std::function` que es un 
envoltorio de objetos invocables, de propósito general.

Intuitivamente se puede pensar que el uso de `std::function` es el ideal, sin
embargo, éste tiene algunas desventajas con respecto a las expresiones lambda,
como que no maneja tipos que sean solo movibles. Adicionalmente, es un objeto
pesado porque necesita manejar todos los objetos invocables, y para hacer eso
requiere mecánicas internas avanzadas como manipulación de tipos o incluso
asignación de memoria dinámica[^cpplambdas18].


## Lambdas sin estado

Un objeto de una clase con una variable miembro puede almacenar un estado
guardando información en esa variable. Si una clase no tiene variable-miembro,
por ejemplo solo tiene funciones, entonces no almacena estados, es una clase 
sin estado.

Esto funciona de manera similar para las expresiones lambda; una lambda sin
estado es aquella que no captura ninguna variable. Tal y como pasa con un
funtor, una estructura o clase sin variables miembro, solamente con el operador
de ejecución, se toma como una [clase vacía]({{ site.baseurl }}{% link _posts/es/principiante/2020-11-29-tamano-clase-vacia.md %})
y su tamaño es 1 Byte.


## Comparativa de tamaños de objetos invocables

Teniendo la siguiente función regular que muestra en consola el mensaje
"Hola mundo":

```c++
void imprime() {
  fmt::print("Hola mundo\n");
}
```

Se pueden crear los siguientes objetos invocables:

```c++
void (*punteroAImprime)() = imprime;                       // puntero a función
struct { void operator()() const { imprime(); } } funtor;  // funtor
const auto lambdaImprime = [](){ imprime(); };             // lambda
const std::function<void(void)> functionImprime = imprime; // function

fmt::print("sizeof(lambdaImprime)   es: {:#2} Bytes \n", sizeof(lambdaImprime));
fmt::print("sizeof(functionImprime) es: {:#2} Bytes \n", sizeof(functionImprime));
```

La expresión lambda del ejemplo anterior es una lambda sin estado.
En GCC y clang el tamaño de `lambdaImprime` es de 1 Byte, mientras
que el tamaño de `functionImprime` es 32 Bytes.


## Fuentes

- [Lambdas en C++](https://leanpub.com/cpplambdaspanish) - Bartlomiej Filipek
- [Stateless lambda](https://www.modernescpp.com/index.php/more-lambdas-features-with-c-20)
- Terminology and Conventions: [Effective Modern C++](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/) - Scott Meyers.
- [std::function](https://es.cppreference.com/w/cpp/utility/functional/function)

---

[^sizeof]: [Operador sizeof](https://es.cppreference.com/w/cpp/language/sizeof)
[^cpplambdas18]: El tipo de una expresión lambda: Bartlomiej Filipek - Lambdas en C++
