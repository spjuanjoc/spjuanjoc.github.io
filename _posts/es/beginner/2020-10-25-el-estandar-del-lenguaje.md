---
title: "El estándar del lenguaje"
date: 2020-10-25T12:00:00-05:00
last_modified_at: 2020-10-25T12:00:00-05:00
categories:
  - Principiante
tags:
  - C++
---

Un estándar de `C++` hace referencia a una versión aprobada por el comité 
[ISO](https://isocpp.org/). Al 2020 se han aprobado los estándares 
98, 03, 11, 14, 17, y 20; estos números corresponden a los años de su aprobación. Desde 2011
se acordó aprobar un estándar cada tres años lo cual permite que el lenguaje se 
mantenga actualizado con un tiempo suficiente de revisión entre versiones. 


El estándar del lenguaje se divide en dos partes principales: 
1. Las características del lenguaje.
2. Las características de la biblioteca.

## Características del lenguaje

Las características del lenguaje corresponden a las funcionalidades del núcleo del lenguaje,
y su compatibilidad va ligada al compilador usado. Al avanzar la versión del 
estándar saldrán nuevas versiones de un compilador que las contenga. 
Por ejemplo el estándar `C++17` tiene la mayoría de sus características 
cubiertas en:

|Compilador|Versión|Plataforma|
|---|:---:|---|
|GCC|8|Unix|
|clang|6|Unix|
|MSVC|19.0|Windows|


## Características de la biblioteca

Las características de la biblioteca corresponden a todas las funcionalidades 
y estructuras implementadas que se encuentran dentro del espacio de nombres `std` (`namespace std`), 
incluyendo la biblioteca estándar de `C`, y la de Plantillas (`STL`).

Su compatibilidad depende de la plataforma ya que está ligada a la Interfaz 
Binaria de Aplicación ABI.

|Fabricante|Biblioteca|Plataforma|
|---|:---:|---|
|GNU|libstdc++|Unix: Debian, Fedora|
|LLVM|libc++|Unix: MacOS FreeBSD|
|Visual Studio|msvcp, msvcprt|Windows|
|Apache|libstdcxx|Unix|
|Oracle|STLport|Unix|

[comment]: <> (Windows: MFC?)

La biblioteca usualmente es un paquete separado. 
En Ubuntu se puede obtener el número de la versión instalada con el comando `dpkg`: 
 
    $ dpkg -l | grep libstdc++
    libstdc++6:amd64  8.4.0-1ubuntu1~  amd64  GNU Standard C++ Library v3
    libstdc++6:i386   8.4.0-1ubuntu1~  i386   GNU Standard C++ Library v3

## Lista de características

En la página de [Soporte de compiladores de C++](https://es.cppreference.com/w/cpp/compiler_support)
se pueden apreciar todas las características agrupadas de acuerdo a la versión del
estándar.
