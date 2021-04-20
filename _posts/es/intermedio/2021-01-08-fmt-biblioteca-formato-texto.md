---
title           : "fmt: Una biblioteca de formato de texto"
date            : 2021-01-08
last_modified_at: 2021-01-11

categories:
  - Intermedio
tags:
  - C++20
  - conan
  - fmt

excerpt: "`{fmt}` es una biblioteca de código abierto, definida como una 
          alternativa rápida y segura frente a `stdio` (de C) y `iostreams` 
          (de C++) para dar formato. `std::format` de `C++20` surge a partir de 
          `fmt::format`."
header:
  og_image      : /assets/images/unsplash-denise-jans-typewriter.jpg
  teaser        : /assets/images/unsplash-denise-jans-typewriter.jpg
  overlay_image : /assets/images/unsplash-denise-jans-typewriter.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Denise Jans**](https://unsplash.com/@dmjdenise?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Las bibliotecas de formato de texto contienen funcionalidades para mostrar
datos en ciertas representaciones de texto.  
En el estándar de C++ se incluyen `printf` para la biblioteca `libc`, 
y `std::ostream` para `libc++`. Fuera del estándar existen otras cuantas
bibliotecas ampliamente usadas, como _Boost Format_, _Fast Format_, y _**fmt**_.

La sintaxis para mostrar en pantalla el valor del entero `x` con un ancho de `4`
en algunas de las bibliotecas nombradas es la siguiente:
```c++
printf("%4d\n", x);                       // libc
std::cout << std::setw(4) << x << '\n';   // libc++
std::cout << boost::format("%|4|\n") % x; // Boost Format
ff::fmtln(std::cout, "{0,4}\n", x);       // Fast Format
std::cout << folly::format("{:4}\n", x);  // Folly Format
fmt::print("{:4}\n", x);                  // fmt
```

## La biblioteca fmt

`{fmt}` es una biblioteca de formato, de código abierto, una alternativa rápida 
y segura frente a `stdio` (de C) y `iostreams` (de C++). [^1]

Esta biblioteca pretende acercarse a la funcionalidad de Python `str.format`,
como una forma expresiva, rápida, segura, e intuitiva de procesar texto con formato.

```c++
fmt::format("La respuesta es {}.", 42);
fmt::print(stderr, "Código de error = {}\n", errno);
```

`fmt` se define como: más segura, simple, y expresiva que `printf`, pues no usa 
punteros, y tiene más validaciones de tipos; más legible que `iostreams`, pues 
su sintaxis es más intuitiva; sin los inconvenientes que ambos tiene en cuanto 
a traducciones; y de desempeño sobresaliente usando _variadic templates_ 
(_parameter packs_), y no _varargs_ (que genera binarios de mayor tamaño, 
no tiene acceso aleatorio, y hace más complejo lidiar con argumentos posicionales). 

## Ejemplos de la sintaxis

1. Argumentos posicionales: Los argumentos pueden ser accedidos de acuerdo a su 
posición.

    ```c++
fmt::format("{}, {}, {}", 'a', 'b', 'c');    // "a, b, c"
fmt::format("{0}, {1}, {2}", 'a', 'b', 'c'); // "a, b, c"
fmt::format("{2}, {1}, {0}", 'a', 'b', 'c'); // "c, b, a"
fmt::format("{0}{1}{0}", "abra", "cad");     // "abracadabra"
    ```

2. Alineamiento del texto: izquierda, derecha, centro, con ancho fijo o dinámico.

    ```c++
fmt::format("{:<30}", "alineado a la izquierda"); // fijo
// "alineado a la izquierda       "

fmt::format("{:<{}}", "alineado a la izquierda", 30); // dinámico
// "alineado a la izquierda       "
    ```

3. Precisión en flotantes: fija o dinámica

    ```c++
fmt::print("{:.1f}", 3.14159265359); // fija
// "3.1"

fmt::print("{:.{}f}", 3.14159265359, 4); // dinámica
// "3.1416"
    ```

4. Fechas: chrono

    ```c++
#include <fmt/chrono.h>

int main() 
{
  auto t = tm();
  
  t.tm_year = 2021 - 1900;
  t.tm_mon  = 1;
  t.tm_mday = 10;
  t.tm_hour = 12;
  t.tm_min  = 15;
  t.tm_sec  = 30;
  
  fmt::print("{:%Y-%m-%d %H:%M:%S}", t);
  
  return 0;
}
    ```

    Resultado: `2021-02-10 12:15:30`


## std::format

La biblioteca estándar de formato de texto está disponible desde `C++20`.
`std::format` surge a partir de `fmt::format`, por lo cual su sintaxis es la misma:

```c++
std::string mensaje = std::format("La respuesta es {}.", 42);
```

Es compatible con `iostreams` y tiene un desempeño muy superior al de
_Boost Format_.

## fmt en Conan

La forma más sencilla (para mí) de usar la biblioteca de `fmt` en un proyecto
`cmake` es por medio de Conan:

Primero, se elige la receta de la versión de fmt a usar:

```
# conanfile.txt
[requires]
fmt/7.1.2

[generators]
cmake
```

Segundo, se enlaza la biblioteca al proyecto:

```cmake
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

target_link_libraries(${PROJECT_NAME} PRIVATE pthread CONAN_PKG::fmt) # Opción 1
# conan_target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS} ) # Opción 2
```

Tercero, se incluyen los archivos de encabezado que se requieran (core, format, color)
en el código fuente:

```c++
#include <fmt/core.h>

int main() 
{
  fmt::print("La respuesta es {}.", 42);
  return 0;
}
```

## fmt en spdlog

`spdlog` es una biblioteca de _logging_ que usa `fmt` de manera interna 
(empaquetado), por lo cual hay que tener consideraciones extra si se quieren 
usar ambos con Conan.  
De todos modos es posible usar `spdlog` con `{fmt}` de manera externa
por medio de las variables de `cmake`:

```
SPDLOG_FMT_EXTERNAL: "Use external fmt library instead of bundled"
SPDLOG_FMT_EXTERNAL_HO: "Use external fmt header-only library instead of bundled"
```

## fmt en Godbolt

También es posible usar `fmt` directamente desde [godbolt.org](https://godbolt.org/z/Eq5763) 
seleccionándola de la lista de bibliotecas disponibles.


## Benchmarks para fmt

Algunas pruebas de referencia comparando la velocidad muestran que su desempeño
es cercano al de `printf` (y en algunos casos superior a este [^2]), y muy
superior a los demás:

| Método | Tiempo de ejecución (segundos)|
|---|:-:|
| printf         | 1.3  |
| **fmt::print** | 1.46 |
| folly format   | 2.54 |
| std::ostream   | 3.35 |
| tinyformat     | 3.69 |
| boost::format  | 8.4  |

Benchmark de TinyFormat[^3].


## Fuentes
- Victor Zverovich: [std::format in C++20](https://www.zverovich.net/2019/07/23/std-format-cpp20.html)  
- {fmt}: [A modern formatting library](https://fmt.dev/latest/index.html)  

[^1]: Ver [fmt overview](https://fmt.dev/7.1.2/#overview)
[^2]: Ver [fmt benchmarks](https://github.com/fmtlib/fmt#benchmarks)
[^3]: Ver [fmt performance](https://youtu.be/ptba_AqFYCM?t=2837) en CppCon 2017.