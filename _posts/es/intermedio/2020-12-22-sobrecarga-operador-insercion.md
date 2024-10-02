---
title           : "Sobrecarga del operador de inserción"
date            : 2020-12-29
last_modified_at: 2020-12-29

categories:
  - Intermedio
tags:
  - C++

excerpt: "Un _stream_ es un canal de flujo de datos; el operador de inserción 
          `<<` se encarga de insertar datos en un flujo de salida `std::ostream`, 
          como lo es `std::cout`."
header:
  og_image      : /assets/images/unsplash-vlad-tchompalov-stream.jpg
  teaser        : /assets/images/unsplash-vlad-tchompalov-stream.jpg
  overlay_image : /assets/images/unsplash-vlad-tchompalov-stream.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Vlad Tchompalov**](https://unsplash.com/@tchompalov?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Un _stream_ es un canal de flujo de datos; en _C++_ existen flujos de lectura
(entrada), escritura (salida), lectura y escritura (entrada y salida), en modo
binario o texto, y la combinación de ellos (lectura-binario, escritura-texto).

Hay dos operadores principales encargados de ser el canal de flujo de datos: el
primero es el de inserción `<<`, y el segundo el de extracción `>>`. El operador
de inserción `<<` se encarga de insertar datos en un flujo de salida
`std::ostream`, como lo es `std::cout`.

## Sobrecarga

Para una estructura personalizada es necesario sobrecargar este operador con el
fin de enviar el flujo a un archivo, a un _log_, al _stdout_, a una variable, o
a derivados de flujos; en general a objetos `stream`.

Dentro de la estructura se sobrecarga como una función _no-miembro_, y _friend_
para poder acceder a sus miembros protegidos y privados. Retorna una referencia
a `std::ostream` ya que se inserta a un flujo de salida, y tiene como parámetros
el objeto de flujo y el objeto de la estructura, el cual debe ser una referencia
constante:

```c++
#include <iostream>

struct Persona
{
  unsigned    edad;
  std::string nombre;

  friend std::ostream& operator<<(
          std::ostream& os, 
          const Persona& p)
  {
    os << p.nombre << " tiene " << p.edad << " años.";
    return os;
  }
};

int main()
{
  Persona persona{20, "Juan"};
  
  std::cout << persona << "\n";

  return 0;
}
```

Produce la salida

    Juan tiene 20 años.

## Extracción

De manera similar se puede sobrecargar el operador de extracción pero con un
flujo de lectura `std::istream`, y así extraer el flujo por ejemplo
desde `std::cin`.

## Comentarios

Está pendiente de validación, y por ahora está como una consideración personal
de una lectura de la cual no recuerdo exactamente la fuente, sin embargo
recuerdo que sería posible considerar esta sobrecarga del operador de
inserción, _friend_ y _no-miembro_, como una implementación del patrón 
_intrusive visitor_.

## Fuentes

- CppReference: [Biblioteca de entrada/salida](https://es.cppreference.com/w/cpp/io)
- Geeks for geeks: [Overloading stream operators](https://www.geeksforgeeks.org/overloading-stream-insertion-operators-c/)
