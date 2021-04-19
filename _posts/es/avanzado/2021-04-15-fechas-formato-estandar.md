---
title           : "Fechas en formato estándar"
date            : 2021-04-15
last_modified_at: 2021-04-18

categories:
  - Avanzado
tags:
  - C++
  - chrono
  - fmt
  - conan
  - spdlog

excerpt: "Una forma sencilla de representar la fecha-hora en el formato estándar
          **ISO 8601** es usando `std::chrono` y la biblioteca `{fmt}`."
header:
  og_image      : /assets/images/unsplash-tzenik-stonehenge.jpg
  teaser        : /assets/images/unsplash-tzenik-stonehenge.jpg
  overlay_image : /assets/images/unsplash-tzenik-stonehenge.jpg
  overlay_filter: 0.125
  caption       : "Imagen por: [**Tzenik**](https://unsplash.com/@tzenik?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Puede haber tantas maneras de representar fechas como el número de países o de
idiomas. Algunas de las abreviaciones más ampliamente usadas son
`dd-mm-yy`, `mm-dd-yy`, `yy-mm-dd`.  

Hay varios ámbitos en los cuales es importante que se puedan interpretar fechas
y horas sin ambigüedad; para este fin existe el estándar ISO 8601[^1] con
formato `YYYY-MM-DD`, aplicable para representar fechas del calendario
gregoriano. Este estándar también incluye la representación de hora,
extendiéndola a `YYYY-MM-DD HH:mm:ss`. También se puede usar la representación
de hora local poniendo la letra `T` entre la fecha y la hora. Adicionalmente se
puede representar la zona horaria UTC: hora local relativa a la UTC (Tiempo
Universal Coordinado).  

En total `YYYY-MM-DDTHH:mm:ss Z` corresponde a `2021-04-15T18:20:28-0500` donde
año: 2021, mes: 04, día: 15, T: hora local, 18 horas (6 p.m.), 20 minutos,
28 segundos, con UTC: -0500.  

Este formato incluye otros campos y aspectos adicionales no analizados acá.

## Usando iostreams

Como casi todo en C++, hay múltiples maneras de resolver una tarea.
La forma tradicional es usar `iostreams` con `std::put_time` para la 
visualización.  
Para obtener los valores con `std::time` al estilo C:  
```c++
#include <ctime>   // Para std::time
#include <iomanip> // Para std::put_time
#include <iostream>

int main()
{
  const time_t now = std::time(nullptr);
  const std::tm now_tm = *localtime(&now);
  std::cout << std::put_time(&now_tm, "%Y-%m-%dT%T%z");
  return 0;
}
```

O con `std::chrono::system_clock` al estilo C++:  
```c++
#include <chrono>
#include <iomanip>
#include <iostream>

using std::chrono::system_clock;

int main()
{
  const std::time_t now = system_clock::to_time_t(system_clock::now());
  const std::tm     tm  = *localtime(&now);
  std::cout << std::put_time(&tm, "%Y-%m-%dT%T%z");
  return 0;
}
```

Donde la representación `"%Y-%m-%d T %T%z"` corresponde a `YYYY-MM-DD T HH:mm:ss Z`
según los especificadores de `std::put_time`[^2].

|std::put_time|ISO 8601|
|:---:|---|
|%Y|  YYYY|
|%m|  MM|
|%d|  DD|
|%T|  HH:mm:ss|
|%z|  Z|

## Usando la biblioteca {fmt}

Una forma interesante de hacer esta misma representación es usar la biblioteca 
[fmt]({{ site.baseurl }}{% link _posts/es/intermedio/2021-01-08-fmt-biblioteca-formato-texto.md %})
(usando [Conan como administrador de paquetes]({{ site.baseurl }}{% link _posts/es/intermedio/2020-11-08-conan-administrador-de-paquetes.md %})
) para poder hacer una manipulación `thread-safe` y más robusta.  
Con `std::time`:  
```c++
#include <fmt/chrono.h>

int main()
{
  const std::time_t now = std::time(nullptr);
  fmt::print("{:%Y-%m-%dT%H:%M:%S%z}\n", fmt::localtime(now));

  return 0;
}
```

Con `std::chrono::system_clock` y fmt se puede usar directamente 
`std::chrono::time_point` por lo cual no hace falta convertir el objeto a `std::time_t`
y que incluso genera un binario de menor tamaño.

```c++
#include <fmt/chrono.h>

using std::chrono::system_clock;

int main()
{
  fmt::print("{0:%Y-%m-%d}T{0:%H:%M:%S%z}\n", 
             fmt::localtime(system_clock::now()));

  return 0;
}
```

En todos los casos el posible resultado, teniendo en cuenta la zona horaria
(UTC-5), es:  

    2021-04-15T18:20:28-0500

## Hora con mili segundos

Obtener los mili segundos con `std::time_t` es un complejo, sin embargo 
con `std::chrono::time_point` es un poco más sencillo:  

```c++
#include <fmt/chrono.h>

using std::chrono::duration_cast;
using std::chrono::milliseconds;
using std::chrono::system_clock;
using std::chrono::time_point;

int main()
{
  const time_point<system_clock> time_now = system_clock::now();
  const auto now_ms = (duration_cast<milliseconds>(time_now.time_since_epoch()) % 1000).count();
  fmt::print("{0:%Y-%m-%dT%H:%M:%S}.{1}{0:%z}\n", fmt::localtime(time_now), now_ms);

  return 0;
}
```

Obteniendo:

    2021-04-15T18:20:28.157-0500


## La biblioteca spdlog

Es una biblioteca de registro de mensajes con un desempeño sobresaliente.
Internamente usa a `fmt` y por defecto muestra los mensajes de registro con
la fecha-hora en el formato ISO_8601. En este ejemplo se usa la referencia a la
receta de conan `spdlog/1.8.2`, y su uso es el siguiente:  

```c++
#include "spdlog/spdlog.h"

int main() 
{
  spdlog::info("Hola spdlog");

  return 0;
}
```

Con lo cual se obtiene:

    [2021-04-15 18:20:28.157] [info] Hola spdlog


## Referencias
- Representaciones de fechas por países: [Date and time representations](https://en.wikipedia.org/wiki/Date_format_by_country)
- Sobre el estándar [ISO_8601](https://es.wikipedia.org/wiki/ISO_8601)
- Sobre [milisegundos en C++](https://www.delftstack.com/es/howto/cpp/how-to-get-time-in-milliseconds-cpp/)
- Sobre [chrono](https://es.cppreference.com/w/cpp/chrono)
- Sobre [spdlog](https://github.com/gabime/spdlog/wiki/1.-QuickStart)

[^1]: Ver [ISO 8601 w3 Date and time formats](https://www.w3.org/TR/NOTE-datetime)
[^2]: Ver [std::put_time](https://es.cppreference.com/w/cpp/io/manip/put_time)
