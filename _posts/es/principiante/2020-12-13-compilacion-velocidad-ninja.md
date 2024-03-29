---
title           : "Compilación a velocidad Ninja"
date            : 2020-12-13
last_modified_at: 2020-12-14

categories:
  - Principiante
tags:
  - C++
  - CMake

excerpt: "Uno de los generadores `CMake` disponibles se denomina `Ninja`, y 
          genera los archivos requeridos para el sistema de compilación con el 
          mismo nombre."

header:
  og_image      : /assets/images/unsplash-marilia-castelli-ninja.jpg
  teaser        : /assets/images/unsplash-marilia-castelli-ninja.jpg
  overlay_image : /assets/images/unsplash-marilia-castelli-ninja.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Marília Castelli**](https://unsplash.com/@liacastelli?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

## Compilación regular

La compilación manual en un sistema Unix se hace especificando el compilador
y las opciones de compilación por medio de la línea de comandos:

    g++-10  main.cpp -std=c++17 -Wall -Werror -O3 -o main.out
    clang++ main.cpp -std=c++17 -Wall -Werror -O3 -o main.out

Este tipo de compilación tienen ciertas condiciones, por ejemplo la dependencia
de la plataforma. Si se desea compilar una biblioteca hay que tener en cuenta 
aún más argumentos.  

`CMake` fue creado con el fin de facilitar la compilación multiplataforma; 
usualmente identifica automáticamente los generadores requeridos para la 
plataforma en la cual se ejecuta, por ejemplo `"Unix"`, `"MinGW"`, o `"NMake"`, 
o los soportados por IDE específicas como `"Visual Studio 16 2019"`, y 
`"Xcode"`, entre otros.

## Ninja

Uno de los generadores disponibles se denomina `"Ninja"`, y genera los archivos
requeridos para el sistema de compilación con el mismo nombre. 
Ninja está enfocado en optimizar la velocidad de compilación usando todos los 
recursos disponibles.

Se puede instalar desde su fuente, o con apt en linux:

```bash
sudo apt install -y ninja-build
```

## ccmake: CMake con interfaz 

`ccmake` es una mini interfaz gráfica para `CMake` la cual permite establecer
opciones de configuración antes de generar las instrucciones de compilación.
Para ingresar a la interfaz desde la consola se ejecuta el comando `ccmake` 
apuntando al archivo `CMakeLists.txt`.

![ccmake](/assets/images/ccmake-ninja.png "Opciones de configuración en ccmake")

Se puede instalar usando apt en linux:

```bash
sudo apt install -y cmake-courses-gui
```

## Compilación Ninja

Usualmente, se elige un directorio específico para la compilación, ya que no
es recomendable compilar en el directorio raíz del proyecto.  
Creando por ejemplo el directorio `build/`, para compilar desde este se siguen 
los pasos:

1. Generar con `ccmake`

    ```bash
    ccmake .. -G "Ninja"
    ```
    `c` para configurar.  
    `t` para entrar al modo avanzado de configuración.  
    `g` para generar.  

2. Compilar con ninja

    ```bash
    ninja -j8
    ```

3. Ejecutar

    ```bash
    ./a.out
    ```

## Uso óptimo de recursos

Ninja optimiza el uso de los recursos disponibles, llegando hasta al `100 %` del
uso de CPU.

![Ninja](/assets/images/ninja-uso-cpu.png "Uso de CPU al compilar con Ninja")

---

## Fuentes
- [CMake generators](https://cmake.org/cmake/help/latest/manual/cmake-generators.7.html)
- [Ninja](https://ninja-build.org/)
