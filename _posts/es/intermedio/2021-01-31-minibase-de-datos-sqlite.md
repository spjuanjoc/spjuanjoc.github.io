---
title           : "Mini base de datos con SQLite3"
date            : 2021-01-31
last_modified_at: 2021-01-31

categories:
  - Intermedio
tags:
  - C++
  - SQLite3
  - DB

excerpt: "."
header:
  teaser        : /assets/images/unsplash-jan-antonin-kolar-database.jpg
  overlay_image : /assets/images/unsplash-jan-antonin-kolar-database.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Jan Antonin Kolar**](https://unsplash.com/@jankolar?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Uno de los motores de bases de datos relacionales más conocido es SQLite, la cual
es una biblioteca `ANSI C` para crear bases de datos de tamaño reducido, que puedan
ser usadas por ejemplo en dispositivos móviles.
Su compatibilidad y portabilidad la hacen una herramienta ampliamente usada.

## SQLite con Conan
Como suelo decir, personalmente creo que la manera más fácil de 
usar una biblioteca de terceros es por medio de conan, siempre y cuando
esté disponible. En esta 
[publicación]({{ site.baseurl }}{% link _posts/es/intermedio/2020-11-08-conan-administrador-de-paquetes.md %})
se puede ver más sobre cómo usar conan.

Para incluir SQLite:

```text
# conanfile.txt
[requires]
sqlite3/3.33.0
fmt/7.1.2

[generators]
cmake
```

También está incluido `fmt`con el fin de insertarle _raw strings_ a las sentencias.

## Sentencias SQL
El primer paso es crear la base de datos. A continuación se crean las tablas, después 
se poblan, y finalmente se consultan.

```sql
DROP TABLE dual;

CREATE TABLE dual (
Dummy varchar2(255),
Value int
);

INSERT INTO "dual" VALUES('valor',10);

select * from dual;
```

Suponiendo que se va a crear solamente una tabla llamada "dual" 
primero hay que asegurarse que no exista: `DROP` se encarga de borrarla si existe.  
Luego se crea la tabla _dual_ con las columnas _Dummy_ y _Value_.  
Se le insertan los valores.  
Finalmente se consultan todos los valores de la tabla.

## c++
Para seguir los mismos pasos anteriores en C++:

1. Se crea la base de datos indicándole un nombre y un _handler_. Si ya existe 
la reabre:

    ```c++
    sqlite3* dbHandler_ = nullptr;
    auto result = sqlite3_open("database.sqlite3", &dbHandler_);
    ```

    Se puede crear una función auxiliar para ejecutar las sentencias

    ```c++
    int runStatement(const char * statement)
    {
      return sqlite3_exec(dbHandler_, statement, printCallback, static_cast<void*>(data_), &errorMessage_);
    }
    ```

2. Se verifica que la tabla _dual_ no exista:
    
    ```c++
    if (runStatement("DROP TABLE dual") != SQLITE_OK)
    {
      sqlite3_free("la tabla no existe");
    }
    ```
3. Se crea la tabla

    ```c++
    const auto create = "CREATE TABLE dual (Col1 varchar2(255), Col2 int);";
    
    if (runStatement(create) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage_ << '\n';
      sqlite3_free(errorMessage_);
    }
    ```

4. Se le insertan valores

    ```c++
    const auto insert = "INSERT INTO dual VALUES('valor',10);";

    if (runStatement(insert) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage_ << '\n';
      sqlite3_free(errorMessage_);
    }
   ```

5. Se consulta el contenido de la tabla

    ```c++
    const auto select = "SELECT * FROM dual;";

    if (runStatement(select) != SQLITE_OK)
    {
      std::cerr << "SQL error: " << errorMessage_ << '\n';
      sqlite3_free(errorMessage_);
    }
   ```

6. Finalmente se cierra la base de datos:

    ```c++
    sqlite3_close(dbHandler_);
    ```

## ..
Crea un archivo .sqlite3  
ver DB, tabla, columnas  

## Conclusiones
Es util pero muy verbose, no tan intuitiva  
Una forma interesante: usando SOCI


## Fuentes
- [SQLite](https://www.sqlite.org/index.html)
- [SQLite3](https://sqlite.org/version3.html)

[^1]: Ver [fmt overview](https://fmt.dev/7.1.2/#overview)
