---
title           : "Conan como administrador de paquetes"
date            : 2020-11-08
last_modified_at: 2021-02-22

categories:
  - Intermedio
tags:
  - paquetes
  - C++
  - CMake
  - conan

excerpt: "Conan es un administrador de paquetes multiplataforma, descentralizado, 
          y de código abierto, que permite crear y compartir paquetes de software 
          para C/C++"
header:
  og_image      : /assets/images/unsplash-curology-dog-package.jpg
  teaser        : /assets/images/unsplash-curology-dog-package.jpg
  overlay_image : /assets/images/unsplash-curology-dog-package.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Curology**](https://unsplash.com/@curology?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Un aspecto clave al trabajar con bibliotecas de terceros en cualquier lenguaje
de programación es la facilidad para administrar paquetes. En cuanto a `C`
y `C++` actualmente no hay un administrador incluido de manera predeterminada,
sin embargo existen varias opciones externas entre las que destacan
_vcpkg_, y _Conan_.

_Conan_ es un administrador de paquetes multiplataforma, descentralizado, y de
código abierto, que permite crear y compartir paquetes de software.

## Usar bibliotecas de terceros

Tradicionalmente al escribir un programa en `C++` que usa una biblioteca de
terceros, por ejemplo Boost, los pasos a seguir serían:

1. Descargar el paquete (o el código fuente y compilar)
2. Vincular el paquete al proyecto (identificar la ruta)
3. Incluir directorios
4. Enlazar la biblioteca con el proyecto
5. Usar la biblioteca en el programa

Estos pasos se repiten para cada biblioteca que se quiera usar en el proyecto.
Para cambiar de versión probablemente habría que desinstalar la anterior. Los
pasos pueden variar dependiendo de la plataforma (Windows, Linux/Ubuntu, MacOS).

Con _Conan_ los pasos son similares pero centralizados en lo que han llamado
"Recetas", y a las cuales se accede por medio de una "referencia a receta", en
donde se puede identificar de manera inmediata: el nombre del paquete, la
versión, el canal y el usuario. La receta puede estar referenciada en un archivo
plano `conanfile.txt`, o en un `conanfile.py` para configuraciones más
avanzadas.

## Ejemplos

Los siguientes ejemplos muestran la configuración requerida por _Conan_
para utilizar algunas bibliotecas de terceros en un proyecto `CMake`:

### googletest

Es el _framework_ de pruebas de google. Es usado para crear pruebas unitarias, 
_fixtures_, _mocks_, entre otros. Tiene dos componentes principales: _gtest_ y 
_gmock_.  
Los pasos a seguir son:

1. Crear el archivo `conanfile.txt`. Se incluye la versión de _gtest_ requerida,
   y el generador con el que se va a compilar.  
    ```text
    # conanfile.txt
    [requires]
    gtest/1.10.0
    
    [generators]
    cmake
    ```

2. Agregar _Conan_ al `CMakeLists.txt` para un proyecto: `project(HolaMundo)`:  
    - Incluir la configuración de _Conan_ para _CMake_:  
    ```cmake
    include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
    ```
   
    - Agregar la configuración básica de Conan:  
    ```cmake
    conan_basic_setup()
   ```

    - Enlazar las bibliotecas de _googletest_: _gtest_ y _gmock_  
    ```cmake
    target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS})
    ```

    También es posible enlazar por separado _gtest_ y _gmock_ en lugar de usar 
    la variable `${CONAN_LIBS}`.  
    ```cmake
    conan_basic_setup(TARGETS)
    target_link_libraries(${PROJECT_NAME} CONAN_PKG::gtest)
    ```

3. Usar en el proyecto incluyendo el archivo de cabecera:  
    ```c++
    #include "gtest/gtest.h"
    
    TEST(NombreDeLaPrueba, FuncionalidadAProbar)
    {
        EXPECT_TRUE(true);
    }
    ```


### Catch2

Es otro _framework_ de pruebas, liviano, nativo de `C++` moderno, con opción de
hacer _micro-benchmarking_, ideal para BDD y TDD.  
Para usar _Catch2_ basta con agregarlo al `conanfile.txt`  

    catch2/2.13.3

Incluir la configuración básica de _Conan_ en el `CMakeLists.txt`:  
```cmake
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()
```
(No hace falta enlazar la biblioteca ya que _Catch2_ es _header-only_)

Finalmente, en el programa incluir el archivo de cabecera:  
```c++
#include "catch2/catch.hpp"

TEST_CASE("foo")
{
  REQUIRE( 1 == 1);
}
```

### Boost Property Tree

Con _Boost_ hay que tener algunas consideraciones adicionales. La primera es que
se pueden usar sub-paquetes de boost por separado gracias a que se encuentran
alojados en el repositorio de _bincrafters_. En este ejemplo se selecciona
únicamente Boost.PropertyTree (incluye sus dependencias)
en la versión de boost `1.69` desde el repositorio _bincrafters_:  
En `conanfile.txt`:  
```text
# conanfile.txt
[requires]
boost_property_tree/1.69.0@bincrafters/stable

[generators]
cmake
```

Para que _Conan_ incluya los paquetes de _bincrafters_ hay que agregar el 
repositorio:  
```shell
conan remote add bincrafters https://api.bintray.com/conan/bincrafters/public-conan
```

La segunda consideración es que Boost es una colección de paquetes, contiene 
paquetes de bibliotecas regulares pero también paquetes que son _header-only_;
para estos últimos no se requieren ajustes.  
Para los demás paquetes CMake necesitará ayuda extra de _Conan_ al enlazar
la biblioteca con el proyecto. Teniendo por ejemplo un `project(HolaMundo)`:  
Ya no se usará directamente:  
```cmake
conan_basic_setup()
target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS})
```

En su lugar se usa el comando de Conan:  
```cmake
conan_basic_setup(TARGETS)
conan_target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS})
```

## Consideraciones

_Conan_ se puede instalar localmente, y también se puede usar directamente en un 
CI, por ejemplo con TravisCI, AppVeyor CI, o Github Actions.

La instalación y configuración básica de _Conan_ en Ubuntu teniendo instalado 
Python3 se realiza por medio de los comandos:  
```shell
pip3 install conan 
conan user
```

Después de tener _Conan_ instalado se puede proceder a instalar los paquetes
para un proyecto que ya tenga la configuración de bibliotecas manejadas por 
_Conan_. Por ejemplo para un proyecto en el que la compilación se hará desde el
directorio `build/` el comando es:  
```shell
conan install ..
```

Con el comando anterior se descargan y configuran los paquetes, y se copia el 
archivo `conanbuildinfo.cmake` al proyecto. Después de este paso se puede 
continuar con los pasos regulares de compilación: 
`cmake ..`, `cmake --build`, etc.  

Los ejemplos mostrados para _googletest_ y _Catch2_ asumen que ya se tiene
configurado el punto de entrada _main_ de los mismos.

Las versiones usadas en los ejemplos son:

|Paquete | Versión |
|--:|:-:|
| CMake | > 3.0     |
| Conan | > 1.28.1  |


## Fuentes
- Documentación oficial [Conan](https://docs.conan.io/en/latest/)
- Buscar recetas de Conan en [Conan center](https://conan.io/center/)
- Buscar recetas de bincrafters en [bintray](https://bintray.com/bincrafters/public-conan)
- Acerca de [googletest](https://github.com/google/googletest)
- Acerca de [catch2](https://github.com/catchorg/Catch2)
