---
title: "Interfaces Gráficas de Usuario"
date: 2021-12-21
last_modified_at: 2024-04-19

categories:
  - Avanzado
tags:
  - C++
  - GUI
  - conan
  - Qt6
  - Gtk4
  - Dear ImGUI
  - SFML
  - wxWidgets

excerpt: "En C++ no hay una biblioteca de gráficos dentro del estándar por lo 
          cual es necesario recurrir a bibliotecas de terceros, como Gtk, Qt,
          o SFML. Es bastante común usar estas bibliotecas de gráficos para 
          escribir videojuegos."
header:
  og_image: /assets/images/unsplash-sebastian-staines-window.jpg
  teaser: /assets/images/unsplash-sebastian-staines-window.jpg
  overlay_image: /assets/images/unsplash-sebastian-staines-window.jpg
  overlay_filter: 0.5
  caption: "Imagen por: [**Sebastian Staines**](https://unsplash.com/@seabas?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---


Escribir programas con Interfaces Gráficas de Usuario (GUI) puede ser un reto
grande y complejo de realizar en C++. Se complica todavía más cuando estas deben
ser multiplataforma.

Es bastante común usar bibliotecas de gráficos para escribir videojuegos.
Algunas de las más populares son _Gtk_ y _Qt_, la cuales son compatibles con los
lenguajes C y C++, y también tienen _bindings_ para otros lenguajes, lo que
significa que hay una especie de envoltorio que permite usar las
funcionalidades de una biblioteca escrita en C o C++ desde un programa escrito
en Python, Java, C#, etc. A partir de _Gtk_ aparece _Gtkmm_ que es la interfaz
de _Gtk_ para C++.

En C++ no hay una biblioteca de gráficos dentro del estándar por lo cual es
necesario recurrir a estas bibliotecas de terceros.

## Dependencias

El manejo de dependencias suele ser uno de los mayores inconvenientes. Por medio
de Conan se pueden encontrar recetas para SFML, ImGui, Gtk, Qt, y raylib, entre
otras. Adicionalmente, en el _artifactory_ Bincrafters de Conan se encuentran
los paquetes para wxWidgets.

## Conan y CMake

Con la ayuda de Conan se pueden instalar las dependencias
requeridas para enlazar algunas de estas bibliotecas de manera simple usando
CMake y `find_package`.

El archivo conanfile (para versión menor a 2.0) que las incluye puede verse así:

```text
# conanfile.txt
[requires]
  qt/6.1.1
  gtk/4.1.2
  raylib/3.5.0
  imgui/1.90.5
  sfml/2.6.1
  wxwidgets/3.1.5@bincrafters/stable
  
[generators]
  cmake_find_package

[options]
  qt:shared=True
  gtk:shared=True
```

Y el CMakeLists del proyecto que las enlaza debe incluir las siguientes
instrucciones:

```cmake
## CMakeLists.txt
#...

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})

find_package(Qt6        REQUIRED)
find_package(gtk        REQUIRED)
find_package(sfml       REQUIRED)
find_package(raylib     REQUIRED)
find_package(wxwidgets  REQUIRED)
find_package(imgui      REQUIRED)


target_link_libraries(${PROJECT_NAME}
  PRIVATE
  Qt6::Qt6
  gtk::gtk
  sfml::sfml
  raylib::raylib
  wxwidgets::wxwidgets
  imgui::imgui
)
# ...
```

Lo anterior puede ser suficiente para escribir un "Hola Mundo" usando cada una
de las bibliotecas incluidas, dejando que Conan se encargue de su instalación.

## Comentarios

Las sintaxis de Qt y wxWidgets son muy particulares, y están poco orientadas al
lenguaje C++, por lo cual es trabajoso acostumbrarse a usar sus funcionalidades.
Raylib y Gtk son bastante simples y fáciles de usar, pero no aprovechan algunas
ventajas de C++, no usan _namespaces_ y abusan de los macros.

Considero que SFML y Dear ImGui son un poco más sencillas de entender, pueden
ofrecer un algo más de flexibilidad, e interactúan bien entre ellas.

En mi concepto, la biblioteca más intuitiva de usar es Dear ImGui, la cual puede
usar como _backend_ a SFML; para este fin existe el paquete ImGui-SFML el cual
las incluye a ambas, y agrega unas cuantas mejoras que permiten usarla con
C++ moderno, y facilitan la integración con la STL.

En general, considero que la documentación existente para todas estas
bibliotecas es escasa, el código suele ser complejo de leer, y es complicado
encontrar ejemplos en español. Me he decantado por esta mezcla de ImGui+SFML
porque lo veo como una sintaxis familiar para mí, y no suele tener tanto código
_boilerplate_, que se refiere a las secciones que se copian y pegan casi sin
modificaciones para comenzar a usar una funcionalidad o biblioteca.


## Videojuegos en C++

Escribir videojuegos en cualquier lenguaje de programación suele ser bastante
llamativo. Lidiar con la instalación de dependencias es frustrante y desestimula
el uso del lenguaje, que ya de por sí es bastante complejo. Con los pasos
anteriores se puede tomar fácilmente un juego ejemplo y compilarlo localmente.

Este corto video de [Kofybrek](https://www.youtube.com/c/Kofybrek/about) ilustra
cómo escribir un juego en C++ no tiene que ser algo terriblemente
complejo: [Super Mario (NES) en C++ con SFML](https://youtu.be/7D4uoSoQsjw).
Y si se le añade el manejo de la dependencia SFML usando Conan es
posible tomar el código fuente y compilarlo. Así, aprender C++ puede hacerse
más entretenido.

## Fuentes

- [Gtk hello world](https://www.gtk.org/docs/getting-started/hello-world)
- [Hello Qt](https://doc.qt.io/archives/3.3/tutorial1-01.html)
- [SFML](https://www.sfml-dev.org/)
- [SFML: Draw text](https://www.sfml-dev.org/tutorials/2.5/graphics-text.php)
- [wxWidgets: Hello World](https://docs.wxwidgets.org/trunk/overview_helloworld.html)
- [ImGui-SFML](https://github.com/SFML/imgui-sfml)
- [RayLib](https://www.raylib.com/index.html)
