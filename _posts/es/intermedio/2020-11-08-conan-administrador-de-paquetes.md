---
title: "Conan como administrador de paquetes"
date: 2020-11-08
last_modified_at: 2020-11-08

categories:
  - Intermedio
tags:
  - administración de paquetes
  - C++
  - CMake

excerpt: "Conan: el administrador de paquetes para C/C++"
header:
  teaser  : https://upload.wikimedia.org/wikipedia/commons/b/b5/Conan_package_manager_logo.png

---

Un aspecto clave al trabajar con bibliotecas de terceros en cualquer lenguaje 
de programación es la facilidad para administrar paquetes.
En cuanto a `C` y `C++` actualmente no hay un administrador incluido en el 
estándar, sin embargo existen varias opciones externas entre las que destacan 
VCPKG, y CONAN.

Conan es un administrador multiplataforma, descentralizado, y de código libre,
que permite crear y compartir bibliotecas.

open source: código libre? abierto?

Tradicionalmente al escribir un programa en c++ que usa una biblioteca de
terceros, digamos Boost, los pasos a seguir serían: 
1. Descargar boost
2. Vincular el paquete al proyecto
3. Incluir directorios
4. Enlazar la biblioteca
5. Usar en el programa

Estos pasos se repetirían para cada biblioteca que se quiera usar en el proyecto, 
y dependen de la plataforma.

Con conan los pasos serían prácticamente los mismos, pero independientes de la 
plataforma, y de una manera un poco más automatizada. Solo se requiere crear un
archivo conanfile con los paquetes a utilizar.

## googletest

Para usar el framework de pruebas de google en un proyecto cmake los pasos son 
los siguientes:
1. Crear el archivo `conanfile.txt`. Se incluye la versión de gtest a usar, y el 
generador con el que se va a compilar. 

    ````
    [requires]
    gtest/1.10.0
    
    [generators]
    cmake
    ````

2. Agregar conan al CMakeLists.txt para el proyecto `project(HolaMundo)`
    - Incluir la configuración de cmake de conan al proyecto 

    ````
    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    ````
   
    - Agregar la configuración básica de conan

    ````
        conan_basic_setup()
   ````

    - Enlazar las bibliotecas 

    ````
        target_link_libraries(HolaMundo ${CONAN_LIBS})
    ````

3. Usar en el proyecto incluyendo el archivo de cabecera:

    ````
    #include "gtest/gtest.h"
    
    TEST(NombreDeLaPrueba, FuncionalidadAProbar)
    {
        EXPECT_TRUE(true);
    }
    ````


## catch2

Para usar el _framework_ de pruebas Catch2 basta con agregarlo al `conanfile.txt`

    catch2/2.13.3

Y en el programa incluir el archivo de cabecera:

    #include "catch2/catch.hpp"

    TEST_CASE("foo")
    {
      REQUIRE( 1 == 1);
    }

## Boost Property Tree

Con Boost hay que tener algunas consideraciones extra. 
La primera es que se pueden usar sub paquetes de boost por separado gracias a 
que se encuentran alojados en el repositorio de _bincrafters_.
En este ejemplo se selecciona únicamente Boost.PropertyTree (incluye sus dependencias)
en la versión de boost 1.69 desde el repositorio bincrafters

En `conanfile.txt`
````
[requires]
boost_property_tree/1.69.0@bincrafters/stable

[generators]
cmake
````

Para que conan incluya los paquetes de bincrafters hay que agregarle el repositorio: 

    conan remote add bincrafters https://api.bintray.com/conan/bincrafters/public-conan


La segunda consideración es que Boost es una colección de bibliotecas, contiene 
paquetes de bibliotecas regulares pero también paquetes que son _header-only_;
para estos últimos no se requieren ajustes.
Para los demás CMake necesitará ayuda extra de conan al enlazar
la biblioteca con el proyect. Teniendo por ejemplo un `project(HolaMundo)`:

Ya no se usará directamente:

    conan_basic_setup()
    target_link_libraries(HolaMundo ${CONAN_LIBS})

En su lugar se usa el comando de conan: 

    conan_basic_setup(TARGETS)
    conan_target_link_libraries(HolaMundo)

Así también podrían enlazarse por separado gtest y gmock para googletest: 

    conan_basic_setup(TARGETS)
    conan_target_link_libraries(HolaMundo CONAN_PKG::gtest)

En lugar de usar la variable `${CONAN_LIBS}`.

## Consideraciones

Conan se puede instalar localmente, o simplemente usar en CI, por ejemplo con TravisCI.
La instalación y configuración básica de conan en Ubuntu teniendo instalado Python 
se realiza por medio de los comandos:

    pip install conan 
    conan user

Los ejemplos mostrados para _googletest_ y _catch2_ asumen que ya se tiene configurado
el punto de entrada _main_.

- Versión CMake > 2.8
- Versión Conan > 1.28.1

## Fuentes
- Documentación oficial [Conan](https://docs.conan.io/en/latest/)
- Buscar recetas de conan en [conan center](https://conan.io/center/)
- Buscar recetas de bincrafters en [bintray](https://bintray.com/bincrafters/public-conan)
- Acerca de [googletest](https://github.com/google/googletest)
- Acerca de [catch2](https://github.com/catchorg/Catch2)
