---
title           : "Compilación simplificada con CMake"
date            : 2020-11-15
last_modified_at: 2020-11-15

categories:
  - Principiante
tags:
  - C++
  - CMake

excerpt: "CMake es una herramienta para controlar el proceso de compilación 
          de software de una manera simple, independiente de la plataforma."
header:
  teaser        : /assets/images/unsplash-fiona-feng-gears.jpg
  overlay_image : /assets/images/unsplash-fiona-feng-gears.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Fiona Feng**](https://unsplash.com/@moonai?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

`CMake` se refiere a _cross-platform make_, su uso principal es el de controlar el 
proceso de compilación de software sin depender de la plataforma. 
Actualmente abarca múltiples 
herramientas para administrar la compilación, pruebas, y empaquetado de software.
Con `CMake` es posible generar archivos de compilación `Make` (Unix), `Xcode` (Apple), 
`Visual Studio` (Microsoft), `Ninja`, y `Qt`, entre otros, para proyectos `C` y `C++`,
 también se puede compilar, lanzar la ejecución de pruebas unitarias (`ctest`), y 
generar paquetes (`cpack`).

Conforme han ido evolucionando los estándares para `C` y `C++` también lo ha hecho
`CMake`, facilitando la forma de crear un proyecto. Desde 2014 con la liberación de
la versión 3.0 se considera como _CMake moderno_, en el cual se prioriza el uso de 
_targets_ y _properties_ sobre las tradicionales variables.

## Hola CMake

El primer paso para crear un proyecto `CMake C++` es crear el archivo `CMakeLists.txt`
con las instrucciones mínimas requeridas:

```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.0...3.15)
project(hola_cmake VERSION 0.0.1 LANGUAGES CXX)

add_executable(${PROJECT_NAME} main.cpp)
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)
```

La primera línea es un comentario.  
La segunda línea valida que el proyecto sea compatible con la versión de `CMake` 
instalada, donde la versión mínima es la `3.0` y la máxima es la `3.15`.  
La tercera línea define el nombre del proyecto, su versión, y lenguaje.  
La siguiente define el Binario Objetivo (_target_) y los archivos a incluir en éste.  
La última línea define el estándar de `C++` que el proyecto va a usar.  

Con esas instrucciones ya se tienen los mínimos requerimientos para el proyecto
`hola_cmake` que contendrá el siguiente código fuente:

```c++
// main.cpp
#include <iostream>

int main()
{
  std::cout << "Hola CMake\n";

  return 0;
}
```

## Binarios objetivo: Targets

Un proyecto `CMake` puede tener el objetivo de crear uno o varios Ejecutables, y Bibliotecas.
Los archivos binarios generados tendrán diferentes sufijos o prefijos dependiendo
de la plataforma destino. 
Por ejemplo para los ejecutables en Windows, `CMake` agregará el sufijo `.exe`.
Para las bibliotecas se podrá configurar que sea estática (`.lib` ), o dinámica 
(compartida `.dll`, `.so`, `.dylib`).

El nombre del _target_ debe ser único en el proyecto, pero pueden existir varios 
_targets_ con diferente nombre en un mismo proyecto. Desde la versión `3.1` de `CMake`
está disponible el comando `target_sources()`.  
En las versiones más recientes (`>3.15`) los archivos de código fuente se pueden 
definir por separado de los _targets_ por medio del comando `target_sources()`:

```cmake
# CMakeLists.txt
#...
add_executable("hola_cmake")
add_library("hola_cmake_bib_estatica" STATIC)
add_library("hola_cmake_bib_compartida" SHARED)

target_sources("hola_cmake" PUBLIC main.cpp)
target_sources("hola_cmake_bib_estatica" PUBLIC foo.cpp)
target_sources("hola_cmake_bib_compartida" PUBLIC bar.cpp)
#...
```

## Opciones

Las opciones son variables personalizadas para el proyecto que el usuario puede
modificar sin necesidad de cambiar el archivo `CMakeLists.txt`:

```cmake
# CMakeLists.txt
#...
# Options
option(ENABLE_TESTS       "Habilitar compilación de pruebas" OFF)
option(ENABLE_CONAN       "Habilitar Conan" OFF)
#...
```

Así el usuario podrá ejecutar los comandos de generación con valores diferentes 
dependiendo de la configuración requerida. Por ejemplo para una configuración en modo _release_
se podría dejar sin habilitar la compilación de pruebas, pero para una configuración
en modo _debug_ habilitarlas:

    cmake .. -DENABLE_TESTS=ON


## Subdirectorios

Por medio del comando `add_subdirectory()` es posible indicarle al proyecto que 
un subdirectorio habrá otro archivo `CMakeLists.txt` que también hace parte del 
proyecto.
Así por ejemplo, teniendo un directorio exclusivo para pruebas _test_ con su 
propio archivo de `cmake`, el archivo principal podrá agregarlo al proyecto:

```cmake
# CMakeLists.txt
#...
if(ENABLE_TESTS)
  add_subdirectory(test)
elseif()
  message("Compilación de pruebas deshabilitada")
endif()
```

## Ejecución de CMake

La ejecución de los comandos `CMake` se divide en dos pasos: generación y compilación.
`CMake` incluye integración con múltiples `IDEs` en las cuales se podrán personalizar 
estos dos pasos.  

La generación corresponde a crear los archivos con las instrucciones de compilación
de acuerdo al _generador_ seleccionado. Si no se selecciona uno, `CMake` podrá elegir
el predeterminado para la plataforma en la cual se está ejecutando. 
Es recomendable que la generación y compilación se hagan en un directorio diferente
al directorio raíz del proyecto, y dentro de éste, pero que sea excluido del
sistema de control de versiones.

Para un sistema GNU/Linux con Ubuntu, para el proyecto `hola_cmake` se puede crear el
directorio `./cmake-build/`  desde el cual se ejecuta el comando de generación:

    cmake .. -G "Unix Makefiles" -DENABLE_TESTS=ON 

Donde `"Unix Makefiles"` es el generador elegido.  


La compilación desde `CMake` creará los archivos binarios para los _targets_
definidos en el proyecto. Desde un terminal Unix el comando de compilación
desde el directorio `cmake-build/` es:

    cmake --build ./

Si los archivos de compilación fueron generados de manera satisfactoria, y
la compilación fue exitosa, se habrán creado los objetivos:

    hola_cmake 
    libhola_cmake_bib_estatica.a
    libhola_cmake_bib_compartida.so

El ejecutable se podrá correr con el resultado esperado:

    $ ./hola_cmake 
    Hola CMake

---

## Consideraciones
- Para versiones de cmake menores a 3.15 pueden aparecer warnings relacionados
con el comando `target_sources()`.

## Fuentes
- Documentación oficial de [CMake](https://cmake.org/cmake/help/latest/index.html)
- Henry Schreiner: [Introducción a CMake moderno](https://cliutils.gitlab.io/modern-cmake/) - Gitbook.
- Pablo Arias: [It's time to do cmake right](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/) - Blog.
- Bill Hoffman: [Open Source Tools to Build Test and Deploy C++ Software](https://youtu.be/8Ut9o4OdSC0) - Google Tech Talk 2007.
- Mateusz Pusz: [Git, CMake, Conan - How to ship and reuse our C++ projects](https://youtu.be/S4QSKLXdTtA) - CppCon 2018.

