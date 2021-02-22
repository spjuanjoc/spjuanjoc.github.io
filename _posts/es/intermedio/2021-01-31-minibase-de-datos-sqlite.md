---
title           : "Mini base de datos con SQLite3"
date            : 2021-01-31
last_modified_at: 2021-03-21

categories:
  - Intermedio
tags:
  - C++
  - SQLite3
  - DB

excerpt: "SQLite es una biblioteca que implementa un motor de bases de datos 
          pequeño, rápido, auto-contenido, de alta fiabilidad, y de 
          funcionalidad SQL completa."
header:
  teaser        : /assets/images/unsplash-jan-antonin-kolar-database.jpg
  overlay_image : /assets/images/unsplash-jan-antonin-kolar-database.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Jan Antonin Kolar**](https://unsplash.com/@jankolar?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

`SQLite` es una biblioteca `ANSI C` que implementa un motor de bases de datos
pequeño, rápido, auto contenido, de alta fiabilidad, y de
funcionalidad SQL completa.  
La base de datos `SQLite` es un archivo de formato estable, multi plataforma, y retro 
compatible, usada comúnmente como contenedor para para transferir contenido
entre sistemas. No requiere configuración adicional ni servidores dedicados.  
`SQLite` es uno de los motores de bases de datos relacionales más conocidos; 
su compatibilidad y portabilidad la hacen una herramienta ampliamente usada.
Un archivo de base de datos `SQLite` se puede copiar libremente entre sistemas
de 32 y 64 bits, así como entre arquitecturas _big-endian_ y _little-endian_.


## SQLite con Conan

Como suelo decir, personalmente creo que la manera más fácil de 
usar una biblioteca de terceros es por medio de `Conan`, siempre y cuando
esté disponible. En esta publicación sobre
[Conan como administrador de paquetes]({{ site.baseurl }}{% link _posts/es/intermedio/2020-11-08-conan-administrador-de-paquetes.md %})
se puede ver más sobre cómo usar `Conan`.

Para incluir `SQLite` versión `3.33` en un proyecto `CMake`:

```text
# conanfile.txt
[requires]
sqlite3/3.33.0

[generators]
cmake
```

`Conan` se encarga de la descarga e instalación, por lo cual los esfuerzos se pueden 
enfocar en el proyecto. Un ejemplo básico de su uso es crear una tabla, poblarla, y
consultarla.

## Sentencias SQL

El primer paso es crear la base de datos lo cual se puede hacer desde un terminal
o consola ejecutando:

    sqlite3 database.sqlite3

Con el comando anterior se crea el archivo `database.sqlite3`.
A continuación se crea una tabla, después se le insertan valores,
y finalmente se consultan esos valores.

```sql
DROP TABLE dual;

CREATE TABLE dual 
(
    Col1 varchar2(255),
    Col2 int
);

INSERT INTO "dual" VALUES('valor',10);

SELECT * FROM dual;
```

Suponiendo que se va a crear solamente una tabla llamada "dual" 
primero hay que asegurarse de que no exista: `DROP` se encarga de borrarla si existe.  
Luego se crea la tabla _dual_ con las columnas _Col1_ y _Col2_.  
Se le insertan los valores.  
Finalmente se consultan.


## SQLite en C++

Para seguir los mismos pasos anteriores en C++:

1. Se crea la base de datos indicándole un nombre y un _handler_. Si ya existe 
la reabre:

    ```c++
    sqlite3* dbHandler = nullptr;
    auto result = sqlite3_open("database.sqlite3", &dbHandler);
    ```

    Se puede crear una función auxiliar para ejecutar las sentencias

    ```c++
    int runStatement(const char* statement)
    {
      return sqlite3_exec(dbHandler, statement, printCallback, static_cast<void*>(data), &errorMessage);
    }
    ```

2. Se verifica que la tabla _dual_ no exista:

    ```c++
    if (runStatement("DROP TABLE dual") != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage << '\n';
      sqlite3_free(errorMessage);
    }
    ```

3. Se crea la tabla:

    ```c++
    const auto create = "CREATE TABLE dual (Col1 varchar2(255), Col2 int);";
    
    if (runStatement(create) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage << '\n';
      sqlite3_free(errorMessage);
    }
    ```

4. Se le insertan valores:

    ```c++
    const auto insert = "INSERT INTO dual VALUES('valor',10);";

    if (runStatement(insert) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage << '\n';
      sqlite3_free(errorMessage);
    }
   ```

5. Se consulta el contenido de la tabla:

    ```c++
    const auto select = "SELECT * FROM dual;";

    if (runStatement(select) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage << '\n';
      sqlite3_free(errorMessage);
    }
   ```

6. Finalmente se cierra la base de datos:

    ```c++
    sqlite3_close(dbHandler);
    ```

## La base de datos

Al crear la base de datos `database.sqlite3` se genera el esquema _main_ con la tabla
_sqlite_master_ de manera automática.

Al crear la tabla _dual_ queda dentro de ese mismo esquema.

![sqlite3db](/assets/screenshots/sqlite3db.png)


## Conclusiones

Es bastante útil y sencillo usar `sqlite3` para crear una base de datos, 
y vincularla al proyecto es fácil usando Conan; no obstante, 
el código es más `C` que `C++` y podría
tornarse particularmente complejo en sentencias más elaboradas,
por ejemplo al intentar recuperar valores individuales de una columna en
particular.  
Por fortuna existe una biblioteca llamada _SOCI_ la cual envuelve _sqlite3_
para hacer más sencilla e intuitiva la programación de `SQL` en `C++`
dando la ilusión de que el código queda incrustado, y de la cual se puede 
ver un ejemplo en la publicación 
[SQL incrustado usando SOCI]({{ site.baseurl }}{% link _posts/es/avanzado/2021-02-21-sql-incrustado-soci.md %})

## Fuentes

- Acerca de [SQLite:](https://www.sqlite.org/about.html)
  Small. Fast. Reliable. Choose any three.
- Acerca de [SQLite3:](https://sqlite.org/version3.html)
Version 3 overview.
