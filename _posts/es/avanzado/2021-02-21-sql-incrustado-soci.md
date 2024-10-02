---
title           : "SQL incrustado usando SOCI"
date            : 2021-02-21
last_modified_at: 2021-07-05

categories:
  - Avanzado
tags:
  - C++
  - SOCI
  - DB
  - SQLite3
  - fmt
  - conan

excerpt: "SOCI es una biblioteca de acceso a bases de datos que soporta `SQLite`,
          `MySQL`, `Oracle SQL`, entre otras. El enfoque en esta publicación es 
          usarla para hacer real esa ilusión de sentencias `SQL` incrustadas en 
          `C++` combinando _SOCI_ con `fmt` y algunas características básicas de 
          `C++` moderno."
header:
  og_image      : /assets/images/unsplash-tobias-fischer-database.jpg
  teaser        : /assets/images/unsplash-tobias-fischer-database.jpg
  overlay_image : /assets/images/unsplash-tobias-fischer-database.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Tobias Fischer**](https://unsplash.com/@tofi?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

En una publicación reciente escribía sobre cómo crear una [base de datos ligera
en un proyecto CMake C++ con SQLite3]({{ site.baseurl }}{% link _posts/es/intermedio/2021-01-31-minibase-de-datos-sqlite.md %})
de una forma sencilla con la ayuda de Conan, y teniendo en cuenta que la
biblioteca de `SQLite3` tiene un gran mantenimiento que la conserva actualizada.


## SOCI en Conan (CCI)

Recientemente (enero 2021) fue aceptada una solicitud de incorporación de
cambios a la rama principal del _Conan Center Index_ (CCI) en la cual se incluye
la biblioteca _SOCI_ versión `4.0.1`; esta es una biblioteca de acceso a bases
de datos que soporta `SQLite`, `MySQL`, `Oracle SQL`, entre otras.

Si bien _SOCI_ tiene características de _Object-Relational Mapping_ (ORM)
bastante llamativas[^1], así como soporte de integración con la `STL`, el
enfoque en esta publicación es usarla para hacer real esa ilusión de
sentencias `SQL` incrustadas en `C++` combinando _SOCI_ con `fmt`[^2] y algunas
características básicas de `C++` moderno, tales como tuplas, _structured
binding_, deducción de tipos, y _raw string literals_ con delimitadores
(_multi-line string literals_).

Si adicional a lo anterior se usa un _framework_ de pruebas como _Catch2_
y una IDE como _CLion_, se forma una combinación bastante robusta con la cual se
puede crear, manipular, probar, y analizar una base de datos incrustada de una
manera eficiente.


## Configuración

En este ejemplo se utilizan las siguientes bibliotecas:

|Nombre | Versión |
|--:|:-:|
| soci   |  4.0.1  |
| sqlite |  3.33.0 |
| fmt    |  7.1.3  |
| catch2 |  2.13.4 |

El uso de SOCI está atado a alguno de los _backend_ que soporta, en este
caso `SQLite3`.

_SOCI_ en Conan no tiene paquetes de binarios pre compilados debido a la
cantidad de combinaciones de _backends_ posibles, por lo tanto se requiere
definir las opciones específicas que aplican para `SQLite3`, así como indicarle
a Conan que se debe compilar el paquete.


### Conan

El archivo `conanfile.txt` debe contener los paquetes requeridos y las 
opciones de SOCI:

```text
# conanfile.txt
[requires]
  soci/4.0.1
  fmt/7.1.3
  catch2/2.13.4

[options]
  soci:shared       = True
  soci:with_sqlite3 = True

[generators]
  cmake_find_package
```

Para que Conan compile el paquete SOCI desde el directorio `./build` se le agrega 
el argumento `--build=soci` al comando de instalación, de la siguiente manera:

    conan install .. --build=soci

En general, se puede usar la opción de compilar los paquetes que hagan falta:

    conan install .. --build=missing

La versión `4.0.1` de SOCI maneja internamente la instalación de la dependencia 
`SQLite3` al seleccionar la opción `soci::with_sqlite3=True`.


### CMake

El generador de conan `cmake_find_package` permite la integración con el 
comando `find_package` de cmake, por lo cual enlazar los paquetes de `fmt` 
y `soci` es similar a como se haría si el paquete estuviera instalado en el 
sistema, y no por conan:

```cmake
# CMakeLists.txt
# ...
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_BINARY_DIR})
find_package(fmt REQUIRED)
find_package(soci REQUIRED)
#...
target_link_libraries(${PROJECT_NAME}
  PRIVATE fmt::fmt soci::soci)
#...
```


## Sentencias SQL

Este ejemplo consiste en crear una tabla, insertarle valores, y finalmente
consultarlos. 

```sql
CREATE TABLE tabla1
(
    id      INTEGER,
    nombre  VARCHAR2(100),
    balance REAL
);

INSERT INTO tabla1 (id, nombre, balance)
VALUES (7, 'Juan' , 100.20),
       (9, 'Juana', 200.10);

SELECT id, nombre, balance FROM tabla1 WHERE id = 9;
```


## SQL en C++

Los anteriores son tres pasos sencillos para probar la facilidad del uso de
SOCI. A continuación se aprecian los mismos pasos en el proyecto C++, los cuales
no están optimizados, pero son suficientes para demostrar el objetivo.

Primero se deben incluir los archivos de cabecera requeridos:

```c++
#include <fmt/format.h>
#include <soci/soci.h>
#include <soci/sqlite3/soci-sqlite3.h>
#include <string>
#include <tuple>
```

Ahora se indican el nombre de la base de datos, el nombre de la tabla, se define
el _backend_ y se crea una sesión con este:

```c++
const auto& connectString{"../database1.sqlite3"};
const auto& table1{"tabla1"};
const soci::backend_factory& backEnd = *soci::factory_sqlite3();
soci::session sql(backEnd, connectString);
```

Se define una función para crear la tabla. Asumiendo que se tiene una función de
validación de su existencia, basta con insertar en la sesión de SOCI un flujo
crudo con la sentencia SQL a ejecutar pasándole el nombre de la tabla:

```c++
void createTable(const std::string& tableName)
{
  if (!tableExists(tableName))
  {
    sql << fmt::format(R"(
CREATE TABLE {0} 
(
    id      INTEGER,
    nombre  VARCHAR2(100),
    balance REAL
);)", tableName);
  }
}
```

De manera similar se crea una función para llenar la tabla, en este caso el
flujo crudo está delimitado por `EOL`:

```c++
void insertInto(const std::string& tableName)
{
  if (tableExists(tableName))
  {
    sql << fmt::format(R"EOL(
INSERT INTO {0} (id, nombre, balance)
VALUES (7, 'Juan' , 100.20),
       (9, 'Juana', 200.10);
)EOL", tableName);
  }
}
```

Finalmente, haciendo uso de la funcionalidad _static binding_[^3] de SOCI se
pueden recuperar los valores de una fila que cumpla determinada condición y
almacenarlos directamente en variables de la función:

```c++
std::tuple<int, std::string, double> getValues(const std::string& tableName, int idToFind)
{
  int id = 0;
  std::string name{};
  double balance = 0.0;

  sql << fmt::format("SELECT id, nombre, balance FROM {0} WHERE id = {1}", 
                     tableName, 
                     idToFind),
         soci::into(id), soci::into(name), soci::into(balance);

  return {id, name, balance};
}
```


## Pruebas con Catch2

Catch2 es una biblioteca para pruebas. Hace falta indicarle un punto de entrada
para comenzar a usarlo. Los casos de prueba son: Probar la conexión correcta con
la base de datos, crear la tabla, insertarle dos filas a la tabla, recuperar los
valores para `ID=9`:

```c++
#define CATCH_CONFIG_MAIN 
#include <catch2/catch.hpp>

//... Los demás #include, y funciones auxiliares

TEST_CASE("should be connected")
{
  CHECK( sql.is_connected() ); 
}

TEST_CASE("should create a table")
{
  createTable(table1);
  CHECK( tableExists(table1) );
}

TEST_CASE("should insert two rows into table")
{
  insertInto(table1);
  CHECK( getNumberOfRows(table1) == 2 );
}

TEST_CASE("should get a row given an id value")
{
  const int idToFind = 9;
  const auto [id, name, balance] = getValues(table1, idToFind);

  CHECK( id == 9);
  CHECK( name == "Juana");
  CHECK( balance == 200.10);
}
```


## Fuentes

- Acerca de [Raw strings literals](https://youtu.be/DiZ-az_nJMM)
- Acerca de [Conan](https://docs.conan.io/en/latest/)

---

[^1]: Ver [SOCI](http://soci.sourceforge.net/doc/master/)
[^2]: Ver [Sobre fmt]({{ site.baseurl }}{% link _posts/es/intermedio/2021-01-08-fmt-biblioteca-formato-texto.md %})
[^3]: Ver [Static binding in soci](http://soci.sourceforge.net/doc/master/types/)
