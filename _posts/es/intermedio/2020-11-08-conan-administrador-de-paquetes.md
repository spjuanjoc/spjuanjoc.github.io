---
title           : "Conan como administrador de paquetes"
date            : 2020-11-08
last_modified_at: 2021-04-29

categories:
  - Intermedio
tags:
  - paquetes
  - C++
  - CMake
  - conan

excerpt: "Conan es un administrador de paquetes multiplataforma, descentralizado, 
          y de código abierto, que permite crear y compartir paquetes de software 
          para C++"
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
terceros, por ejemplo Boost, los pasos a seguir son:

1. Descargar el paquete (precompilado, o el código fuente)
2. Vincular el paquete al proyecto (identificar las rutas)
3. Vincular directorios requeridos
4. Enlazar las bibliotecas a usar en el proyecto
5. Incluir archivos de cabecera
6. Usar la biblioteca en el programa
7. Indicar las instrucciones de compilación requeridas.

Estos pasos se repiten para cada biblioteca que se quiera usar en el proyecto.
Para cambiar de versión probablemente hace que desinstalar la anterior. Los
pasos pueden variar dependiendo de la plataforma (Windows, Linux/Ubuntu, MacOS).
Las instrucciones de compilación pueden ser complejas de entender. Si se 
descarga el código fuente del paquete es probable que tenga su propio sistema
de compilación. Hay varias desventajas de que este proceso sea manual.

Con _Conan_ los pasos son similares pero automatizados, centralizados en lo que
han llamado *Recetas* a las cuales se accede por medio de una *referencia a
receta* en donde se puede identificar de manera inmediata el nombre del paquete
y la versión. La receta puede estar referenciada en un archivo
plano `conanfile.txt` para mayor simplicidad. Por defecto _Conan_ busca primero
la existencia del archivo `conanfile.py` y luego del `.txt` pero se le puede
indicar cuál es el archivo que contiene las recetas a usar.

## Generadores de Conan

Son componentes que permiten identificar información del paquete para 
interactuar con el sistema de compilación. En el archivo plano se configuran
en la sección `[generators]`, y puede haber uno o varios generadores. 
Hay bastantes ejemplos que usan el generador `cmake`, sin embargo, desde _Conan_
y desde la comunidad se está estimulando el uso del generador 
`cmake_find_package` que se integra directamente con la instrucción de CMake 
ampliamente conocida: `find_package()`.


## Ejemplos

Los siguientes ejemplos muestran la configuración requerida por _Conan_
para utilizar algunas bibliotecas de terceros en un proyecto `CMake`:

### googletest

Es el _framework_ de pruebas de google. Es usado para crear pruebas unitarias, 
_fixtures_, _mocks_, entre otros. Tiene dos componentes principales: _gtest_ y 
_gmock_. Los pasos a seguir son:

1. Crear el archivo `conanfile.txt`. Se incluye la versión de _gtest_ requerida,
   y el generador a usar.  

    ```text
    # conanfile.txt
    [requires]
      gtest/1.10.0
    
    [generators]
      cmake_find_package
    ```

2. Ajustar el archivo `CMakeLists.txt` para buscar el paquete y enlazar las
   bibliotecas:

     ```cmake
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})
    find_package(GTest REQUIRED)
    target_link_libraries(${PROJECT_NAME} pthread GTest::gtest)
     ```

3. Usar en el proyecto incluyendo el archivo de cabecera:  

    ```c++
    #include "gtest/gtest.h"
    
    TEST(NombreDeLaPrueba, FuncionalidadAProbar)
    {
      EXPECT_TRUE(true);
    }
    
    int main(int argc, char* argv[])
    {
      testing::InitGoogleTest(&argc, argv);
      return RUN_ALL_TESTS();
    }
    ```

### Catch2

Es otro _framework_ de pruebas, liviano, nativo de `C++` moderno, con opción de
hacer _micro-benchmarking_, ideal para BDD y TDD. No hace falta enlazar la
biblioteca ya que _Catch2_ es _solo-cabecera_. Los pasos para usar _Catch2_ son:

1. Agregar la *referencia a la receta* de Catch2 al `conanfile.txt`
    
    ```text
    # conanfile.txt
    [requires]
      catch2/2.13.3
    
    [generators]
      cmake_find_package
    ```

2. Ajustar el `CMakeLists.txt` para que encuentre el paquete, y vincular los
   directorios _include_ del paquete al proyecto:
    
    ```cmake
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})
    find_package(Catch2 REQUIRED)
    target_include_directories(${PROJECT_NAME} PRIVATE ${Catch2_INCLUDE_DIRS})
    ```

3. En el programa, incluir el archivo de cabecera a usar:  

    ```c++
    #define CATCH_CONFIG_MAIN
    #include "catch2/catch.hpp"
    
    TEST_CASE("foo")
    {
      REQUIRE( 1 == 1);
    }
    ```

### Boost: Property Tree y Regex

Con _Boost_ hay que tener algunas consideraciones adicionales ya que contiene
bibliotecas _solo-cabecera_, por lo cual hay que identificar cuáles se deben
enlazar y cuáles no. Por ejemplo al usar `Boost.PropertyTree` la cual es
_solo-cabecera_ no se debe enlazar sino solamente vincular sus directorios
_include_; para `Boost.Regex` sí se debe enlazar la biblioteca correspondiente:

1. Agregar `Boost 1.69` al `conanfile.txt`:

    ```text
    [requires]
      boost/1.69.0
    
    [generators]
      cmake_find_package
    ```

2. Ajustar el `CMakeLists.txt` para que encuentre el paquete, enlazar
   Boost::regex al proyecto, y vincular los directorios _include_ de Boost
   al proyecto:

    ```cmake
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})
    find_package(Boost COMPONENTS regex REQUIRED)
    target_link_libraries(${PROJECT_NAME} Boost::regex)
    target_include_directories(${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS})
    ```

3. Incluir los archivos de cabecera requeridos:

    ```c++
    #include "boost/property_tree/json_parser.hpp"
    #include "boost/property_tree/ptree.hpp"
    #include "boost/regex.h"
    ``` 

## Consideraciones

* Recordar que _find_package_ distingue mayúsculas y minúsculas.
  
* _Conan_ se puede instalar localmente, y también se puede usar directamente en
  un CI, por ejemplo con TravisCI, AppVeyor CI, o Github Actions. Solo requiere
  que se soporte Python.

* La instalación y configuración básica de _Conan_ teniendo instalado
  `python3` y `pip3` se realiza por medio de los comandos:

```shell
pip3 install conan 
conan user
```

  Nota: Es posible que en Travis CI se deba usar `pip` en lugar de `pip3`, de
  acuerdo a la versión de python que esté instalada en el job.

* Después de tener _Conan_ instalado se puede proceder a instalar los paquetes
  para un proyecto que ya tenga la configuración de bibliotecas manejadas por
  _Conan_. Por ejemplo para un proyecto en el que la compilación se hace desde
  el directorio `build/` el comando es:

```shell
conan install ../conanfile.txt
```

Con el comando anterior se descargan y configuran los paquetes, y se copia el
archivo `conanbuildinfo.cmake` al proyecto. Al usar el generador
`cmake_find_package` se obtienen los archivos `Find*.cmake` de cada paquete.
Después de este paso se puede continuar con los pasos regulares de
compilación: `cmake ..`, `cmake --build`, etc, teniendo en cuenta que se le debe
indicar la ruta de `CMAKE_MODULE_PATH` que usualmente es el directorio de
compilación.

* _Catch2_ y _googletest_ deben tener configurado el punto de entrada _main_.

* Las versiones usadas en los ejemplos son:

| Paquete |  Versión  |
|--------:|:---------:|
|   CMake |  \> 3.0   |
|   Conan | \> 1.28.1 |


## Fuentes

- Documentación oficial de [Conan](https://docs.conan.io/en/latest/)
- Buscar recetas de Conan en [Conan center](https://conan.io/center/)
- Acerca de [googletest](https://github.com/google/googletest)
- Acerca de [catch2](https://github.com/catchorg/Catch2)
- Acerca de [Boost](https://www.boost.org/doc/libs/)
