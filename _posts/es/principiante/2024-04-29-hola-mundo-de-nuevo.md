---
title           : "Hola Mundo, de nuevo: std::print"
date            : 2024-01-29
last_modified_at: 2024-04-29

categories:
  - Principiante
tags:
  - C++23
  - Hola mundo
  - printf
  - fmt
  - std::print
  - iostream

excerpt: "Así como sucede con la mayoría de funcionalidades en C++ hay diversas 
          formas de escribir mensajes en el _stdout_. A partir del estándar 
          C++23 se puede encontrar la funcionalidad `std::print`,
          la cual viene de la ya conocida biblioteca `{fmt}`, y de las ya 
          integradas funcionalidades de formato `std::format`."

header:
  og_image      : /assets/images/unsplash-robert-tjalondo-polaroid.jpg
  teaser        : /assets/images/unsplash-robert-tjalondo-polaroid.jpg
  overlay_image : /assets/images/unsplash-robert-tjalondo-polaroid.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Robert Tjalondo**](https://unsplash.com/@tjalondo?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Hola Mundo, de nuevo. En una publicación anterior ya mencionaba cómo hacer un
[¡Hola Mundo!]({{ site.baseurl }}{% link _posts/es/principiante/2020-10-25-hola-mundo.md %})
en C++. Se ha introducido una característica en el estándar C++23 que modifica
la forma en la que se escribe ese primer programa que sirve como ejercicio de
introducción a un lenguaje de programación, o que se escribe como prueba inicial
al actualizar el sistema operativo, al usar otro compilador, una nueva máquina, 
al probar una biblioteca, una nueva IDE, al probar pipelines en CI/CD, etc.

## `std::print`

Así como sucede con la mayoría de funcionalidades en C++ hay diversas formas de
escribir mensajes en el _stdout_, tales como `printf`, `std::puts()`,
y `std::cout`.  
A partir del estándar `C++23` se puede encontrar la funcionalidad `std::print()`
en las siguientes versiones de la biblioteca estándar:

| Biblioteca    | Versión |
|---------------|:-------:|
| gcc libstdc++ |   14    |
| clang libc++  |   18    |
| msvc stl      |  19.37  |

`std::print` es una funcionalidad que viene de la ya conocida biblioteca
{fmt}[^1], que ya tenía integradas las funcionalidades de
formato `std::format`.

Para usar las funcionalidades basta con usar un compilador con versión 
compatible, e indicarle que se usa el estándar `-std=c++23`.
 

## Sintaxis y Mini-lenguaje

Las características de formato se pueden ver en la tabla de _mini-language_[^2]
de la biblioteca `{fmt}`, y se refiere a las especificaciones de formato
soportadas. Como especificaciones destacadas se encuentran las facilidades para
el formato binario, hexadecimal, de punto-flotante, de _chrono_, y de rangos.

La sintaxis se da por medio de campos de reemplazo y argumentos posicionales
envueltos en llaves `{}`.

|                    |                                    |
|--------------------|------------------------------------|
| campo de reemplazo | `'{' [arg_id] [':' (formato)] '}'` |
| arg_id             | identificador del argumento        |
| formato            | formato de la cadena               |
| ejemplo            | `std::print("{0:d}", 42);`         |


## Hola de nuevo

Un nuevo "Hola Mundo", en comparación con los anteriores, podría verse de la 
siguiente manera:

<div>
  <iframe 
    width="100%" height="650px"
    src="https://godbolt.org/e?readOnly=true&hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,source:'%23include+%3Ccstdio%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cprint%3E%0A%0Aint+main()+%7B%0A++printf(%22hola+printf%5Cn%22)%3B%0A++puts(%22hola+puts%22)%3B%0A++std::cout+%3C%3C+%22Hola+%22+%3C%3C+%22iostream%22+%3C%3C+std::endl%3B%0A++std::print(%22Hola+mundo%5Cn%22)%3B%0A%0A++std::print(%0A++++++%22%E2%94%8C%7B0:%E2%94%80%5E%7B2%7D%7D%E2%94%90%5Cn%22%0A++++++%22%E2%94%82%7B1:+%5E%7B2%7D%7D%E2%94%82%5Cn%22%0A++++++%22%E2%94%94%7B0:%E2%94%80%5E%7B2%7D%7D%E2%94%98%5Cn%22,%0A++++++%22%22,+%22Hola+de+nuevo,+mundo%22,+25)%3B%0A%0A++return+0%3B%0A%7D%0A'),l:'5',n:'1',o:'C%2B%2B+source+%231',t:'0')),header:(),k:100,l:'4',m:54.6875,n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:clang_trunk,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B23',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+clang+(trunk)+(C%2B%2B,+Editor+%231)',t:'0')),header:(),l:'4',m:45.3125,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4">
  </iframe>
</div>

## Otros ejemplos

En una publicación anterior está más detallada la funcionalidad de 
[{fmt}]({{ site.baseurl }}{% link _posts/es/intermedio/2021-01-08-fmt-biblioteca-formato-texto.md %})
, y aunque la implementación de `std::print` puede variar dependiendo del
compilador, sus funcionalidades son las mismas. El ejemplo de algunos
especificadores de formato luce así:

```c++
#include <print>

std::print("{0:<6} = {1:#06x} {3:f} {2:} {4:#b} {4:d}\n", 
            "texto", 0xff, 42, 42.0, 0b10);
// Resultado: texto  = 0x00ff 42.000000 42 0b10 2
```

Explicación

- El argumento `0` está alineado a la izquierda con ancho de 6 caracteres, y
  contiene la cadena `texto`
- El argumento `1` es hexadecimal con prefijo (`0x`) de ancho 6 rellenado con
  ceros
- El argumento `3` aparece antes que el argumento 2. Contiene la variable tipo
  `double` con valor 42, con precisión estándar de 6 caracteres, con formato de
  punto flotante
- El argumento `2` aparece después que el argumento 3. Contiene un entero
- El argumento `4` aparece dos veces, la primera es en representación binaria
  con prefijo (`0b`), la segunda es en representación decimal

## Motivación

Hay diversos motivos por los cuales `std::print` llama la atención.
Una de sus características más interesantes es la validación de tipos de datos
en tiempo de compilación, que incluso tiene detección de errores por medio de
análisis estático.

Es fácilmente extensible, es compatible con los contenedores de la STL, con
la biblioteca de rangos, con la biblioteca de fechas `std::chrono`, y con tipos
de datos definidos por usuarios.
Es compatible con Unicode, tiene un gran desempeño según sus pruebas de
referencia _benchmark_[^benchmark], y funciona bien con multi-hilos.
Tiene una sintaxis mucho más sencilla que la de `iostreams`, y cubre con las
falencias de `printf` sin afectar el desempeño.

Algunos temas relacionados que permiten entender un poco más
del funcionamiento de `std::printf` incluyen plantillas variádicas (paquetes de
parámetros)[^3], _perfect forwarding_[^4], y argumentos posicionales.


## Fuentes

- Vitaut blog: [print-in-cpp23](https://vitaut.net/posts/2023/print-in-cpp23/)
- Cppreference: [std::print](https://es.cppreference.com/w/cpp/io/print) 
- Scott Meyers - [Effective Modern C++](https://books.google.com.co/books?id=ZDhIBQAAQBAJ)
- Learn microsoft: [Ellipses and Variadic Templates](https://learn.microsoft.com/en-us/cpp/cpp/ellipses-and-variadic-templates?view=msvc-170)

---

[^1]: Latest [ {fmt} syntax ](https://fmt.dev/latest/syntax.html)
[^2]: Format Specification [Mini-Language](https://fmt.dev/latest/syntax.html#format-specification-mini-language) 
[^3]: [Paquetes de parámetros](https://es.cppreference.com/w/cpp/language/parameter_pack)
[^4]: Effective Modern C++, capítulo 5. R-Value References, Move Semantics And Perfect Forwarding
[^benchmark]: {fmt} [benchmarks](https://github.com/fmtlib/fmt?tab=readme-ov-file#benchmarks)
