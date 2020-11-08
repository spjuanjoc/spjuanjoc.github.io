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

excerpt: "Conan es un administrador de paquetes multiplataforma, descentralizado, y de código abierto, que permite crear y compartir paquetes de software para C/C++"
header:
  teaser  : /assets/images/conan-el-barbaro.jpg

---

Un aspecto clave al trabajar con bibliotecas de terceros en cualquer lenguaje 
de programación es la facilidad para administrar paquetes.
En cuanto a `C` y `C++` actualmente no hay un administrador incluido en el 
estándar, sin embargo existen varias opciones externas entre las que destacan 
_vcpkg_, y _conan_.

_Conan_ es un administrador de paquetes multiplataforma, descentralizado, 
y de código abierto, que permite crear y compartir paquetes de software.

Tradicionalmente al escribir un programa en `C++` que usa una biblioteca de
terceros, digamos Boost, los pasos a seguir serían: 
1. Descargar boost
2. Vincular el paquete al proyecto
3. Incluir directorios
4. Enlazar la biblioteca
5. Usar la biblioteca en el programa

Estos pasos se repetirían para cada biblioteca que se quiera usar en el proyecto. 
Cabe notar que los pasos pueden variar de acuerdo a la plataforma, este ejemplo aplica para Ubuntu.

Con _conan_ los pasos serían similares pero sin depender de la 
plataforma, y de una manera un poco más automatizada. Solo se requiere crear un
archivo `conanfile.txt` con los paquetes a utilizar en el cual se le indica 
la versión a usar.

Los siguientes ejemplos muestran cómo usar _conan_ para utilizar bibliotecas de terceros en un proyecto `CMake`:

## googletest

Es el _framework_ de pruebas de google. Es usado para crear 
pruebas unitarias, _fixtures_, _mocks_, entre otros. Tiene dos 
componentes principales: _gtest_ y _gmock_. 
Los pasos a seguir son:

1. Crear el archivo `conanfile.txt`. Se incluye la versión de _gtest_ requerida,
y el generador con el que se va a compilar. 

    ````
    [requires]
    gtest/1.10.0
    
    [generators]
    cmake
    ````

2. Agregar _conan_ al `CMakeLists.txt` para un proyecto: `project(HolaMundo)`
    - Incluir la configuración de _conan_ para _CMake_: 

    ````
    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    ````
   
    - Agregar la configuración básica de conan

    ````
        conan_basic_setup()
   ````

    - Enlazar las bibliotecas de _googletest_: _gtest_ y _gmock_

    ````
        target_link_libraries(HolaMundo ${CONAN_LIBS})
    ````

    También es posible enlazar por separado _gtest_ y _gmock_ en lugar de usar la variable `${CONAN_LIBS}`. 

    ````
        conan_basic_setup(TARGETS)
        target_link_libraries(HolaMundo CONAN_PKG::gtest)
    ````

3. Usar en el proyecto incluyendo el archivo de cabecera:

    ````
    #include "gtest/gtest.h"
    
    TEST(NombreDeLaPrueba, FuncionalidadAProbar)
    {
        EXPECT_TRUE(true);
    }
    ````


## Catch2

Es otro _framework_ de pruebas, liviano, nativo de 
`C++` moderno, con opción de hacer _micro-bemchmarking_,
 ideal para BDD y TDD. 
Para usar _Catch2_ basta con agregarlo al `conanfile.txt`

    catch2/2.13.3

Incluir la configuración básica de _conan_ en el `CMakeLists.txt`:

    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    conan_basic_setup()

(No hace falta enlazar una biblioteca ya que _Catch2_ es _header-only_)

Finalmente, en el programa incluir el archivo de cabecera:

    #include "catch2/catch.hpp"

    TEST_CASE("foo")
    {
      REQUIRE( 1 == 1);
    }

## Boost Property Tree

Con _Boost_ hay que tener algunas consideraciones adicionales. 
La primera es que se pueden usar sub-paquetes de boost por separado gracias a 
que se encuentran alojados en el repositorio de _bincrafters_.
En este ejemplo se selecciona únicamente Boost.PropertyTree (incluye sus dependencias)
en la versión de boost `1.69` desde el repositorio _bincrafters_:

En `conanfile.txt`
````
[requires]
boost_property_tree/1.69.0@bincrafters/stable

[generators]
cmake
````

Para que _conan_ incluya los paquetes de _bincrafters_ hay que agregar el repositorio: 

    conan remote add bincrafters https://api.bintray.com/conan/bincrafters/public-conan


La segunda consideración es que Boost es una colección de paquetes, contiene 
paquetes de bibliotecas regulares pero también paquetes que son _header-only_;
para estos últimos no se requieren ajustes.
Para los demás paquetes CMake necesitará ayuda extra de _conan_ al enlazar
la biblioteca con el proyecto. Teniendo por ejemplo un `project(HolaMundo)`:

Ya no se usará directamente:

    conan_basic_setup()
    target_link_libraries(HolaMundo ${CONAN_LIBS})

En su lugar se usa el comando de conan: 

    conan_basic_setup(TARGETS)
    conan_target_link_libraries(HolaMundo)


## Consideraciones

_Conan_ se puede instalar localmente, o simplemente usar en CI, por ejemplo con TravisCI.

La instalación y configuración básica de _conan_ en Ubuntu teniendo instalado Python 
se realiza por medio de los comandos:

    pip install conan 
    conan user

Después de tener _conan_ instalado se puede proceder a instalar los paquetes para
un proyecto que ya tenga la configuración de bibliotecas manejadas por _conan_. 
Por ejemplo para un proyecto en el que la compilación se hará desde el directorio `build/`
el comando es: 

    conan install ..

Con el comando anterior se descargan y configuran los paquetes, y se copia el archivo `conanbuildinfo.cmake`
al proyecto. Después de este paso se puede continuar con los pasos regulares de
compilación: `cmake ..`, `cmake --build`, etc.

- Los ejemplos mostrados para _googletest_ y _Catch2_ asumen que ya se tiene configurado
el punto de entrada _main_ de los mismos.
- Versión CMake > 3.0
- Versión Conan > 1.28.1

## Fuentes
- Documentación oficial [Conan](https://docs.conan.io/en/latest/)
- Buscar recetas de conan en [conan center](https://conan.io/center/)
- Buscar recetas de bincrafters en [bintray](https://bintray.com/bincrafters/public-conan)
- Acerca de [googletest](https://github.com/google/googletest)
- Acerca de [catch2](https://github.com/catchorg/Catch2)
