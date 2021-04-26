---
title           : "Creación de bibliotecas en proyectos CMake"
date            : 2020-12-08
last_modified_at: 2021-04-26

categories:
  - Intermedio
tags:
  - C++
  - CMake

excerpt: "Los dos tipos de bibliotecas usualmente creadas en `C++` son: 
          estáticas y dinámicas.  
          A diferencia de los binarios ejecutables, estas no tienen punto de 
          entrada `main` definido."
header:
  og_image      : /assets/images/unsplash-jez-timms-library.jpg
  teaser        : /assets/images/unsplash-jez-timms-library.jpg
  overlay_image : /assets/images/unsplash-jez-timms-library.jpg
  overlay_filter: 0.0
  caption       : "Imagen por: [**Jez Timms**](https://unsplash.com/@jeztimms?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Una biblioteca en `C++` es una colección de funcionalidades cuyas declaraciones
suelen estar agrupadas en archivos de cabecera; una biblioteca puede estar
compuesta de múltiples archivos de cabecera.  
Las implementaciones suelen estar en archivos de código fuente separados del
archivo de cabecera; cuando la implementación se encuentra adentro se conoce
como solo-cabecera o _header-only_ e implica que toda la lógica está expuesta y
no se genera una unidad de compilación; este suele ser el caso de
funcionalidades definidas en plantillas.  
Aunque al ser _header-only_ también es una reunión de funcionalidades que se
considera como una biblioteca, esta definición se encuentra en un área gris
puesto que no genera un fichero de biblioteca: `.lib`, `.dll`, `.a`, `.dylib`,
o `.so`.

Excluyendo las _header-only_, los dos tipos de bibliotecas usualmente creadas
en `C++` son: estáticas y dinámicas (compartidas). A diferencia de los binarios
ejecutables, estas no tienen punto de entrada `main` definido.

Es posible crear un proyecto de `CMake` para que contenga como objetivos:
- Solamente una biblioteca.
- Un ejecutable que use una o varias bibliotecas internas.
- Un ejecutable que use una o varias bibliotecas externas.

## Sólo biblioteca

No tiene un punto de entrada definido, sin embargo, en caso de tener pruebas
unitarias se podrá definir un punto de entrada en éstas.  
Para establecer que un objetivo (_target_) es una biblioteca en un proyecto
con `CMake` se usa el comando de agregar biblioteca, como se puede ver en
[Compilación simplificada con CMake]({{ site.baseurl }}{% link _posts/es/principiante/2020-11-15-compilacion-simplificada-cmake.md %}),
y posteriormente se le indican los archivos de código fuente que debe incluir:

```cmake
# CMakeLists.txt
# ...
project(solo_biblioteca)
# ...
# Crear el objetivo como biblioteca dinámica
add_library("${PROJECT_NAME}_dinamica" SHARED )

# Crear el objetivo como biblioteca estática
add_library("${PROJECT_NAME}_estatica" STATIC )

# Incluir los archivos en el objetivo
target_sources("${PROJECT_NAME}_dinamica" PUBLIC foo.cpp)
target_sources("${PROJECT_NAME}_estatica" PUBLIC bar.cpp)
# ...
```

En un sistema Linux se generan las bibliotecas `libsolo_biblioteca_dinamica.so`
y `libsolo_biblioteca_estatica.a`.


## Dentro del mismo proyecto

Un proyecto puede tener tanto un objetivo ejecutable, como múltiples objetivos
de bibliotecas enlazadas a éste:

```cmake
# CMakeLists.txt
# ...
project(biblioteca_adentro)
# ...
# Crear el objetivo de la biblioteca 
add_library("${PROJECT_NAME}" SHARED foo.cpp )

# Crear el objetivo del ejecutable
add_executable("${PROJECT_NAME}_runner" main.cpp )
# ...

# Enlazar la biblioteca al objetivo ejecutable
target_link_libraries("${PROJECT_NAME}_runner" biblioteca_adentro)
# ...
```

El objetivo `biblioteca_adentro_runner` que contiene el punto de entrada
consume las funcionalidades de la biblioteca incluida en el proyecto.

En un sistema Linux se generan la biblioteca `libbiblioteca_adentro.so` y 
el ejecutable `biblioteca_adentro_runner`.


## Fuera del proyecto

Si se requiere usar una biblioteca que ya está creada, ya sea propia o por
terceros, hay varias opciones para incluirla en el proyecto; la manera
tradicional es tener el paquete de la biblioteca descargado y compilado, y
buscarlo desde el proyecto para enlazarlo; una manera moderna es usar un
administrador de paquetes como `Conan` o `vcpkg`.
Usando `Conan`, como se puede ver en:
[Conan como administrador de paquetes]({{ site.baseurl }}{% link _posts/es/intermedio/2020-11-08-conan-administrador-de-paquetes.md %}),
basta con agregar la configuración básica de conan y enlazar las bibliotecas 
a usar:

```cmake
# CMakeLists.txt
# ...
# Agregar configuración de conan
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()
# Enlazar
target_link_libraries(${PROJECT_NAME} ${CONAN_LIBS})
# ...
```

## Consideraciones

- Además de las opciones `STATIC` (para estática) y `SHARED` (para dinámica)
  existe la opción `INTERFACE` pero está fuera del alcance de esta publicación.
- Versión CMake > 3.0

## Recursos
- Documentación oficial CMake: [Add library](https://cmake.org/cmake/help/latest/command/add_library.html)
- Documentación oficial CMake: [Link libraries](https://cmake.org/cmake/help/v3.0/command/target_link_libraries.html)
