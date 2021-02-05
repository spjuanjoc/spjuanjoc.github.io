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

## conan
Como suelo decir, personalmente creo que la manera más fácil de 
usar una biblioteca de terceros es por medio de conan, siempre y cuando
esté disponible (ver post de conan)
[Conan como administrador de paquetes]({{ site.baseurl }}{% link _posts/es/intermedio/2020-11-08-conan-administrador-de-paquetes.md %}),

usar también fmt

## SQL
```sql
DROP TABLE  dual;

CREATE TABLE dual (
Dummy varchar2(255),
Value int
);

INSERT INTO "dual" VALUES('hello',10);
INSERT INTO "dual" VALUES('goodbye',20);

select * from dual;
```
Por un lado se crea el archivo de la base de datos. A esta base hay que crearle
tablas (con sus respectivas relaciones)
Pero suponiendo que se va a crear solamente una tabla "dual" 
primero hay que asegurarse que no exista: DROP
Luego se crea
Se le insertan valores
se consulta

## c++
Para tener los mismos pasos anteriores en C++

singleton para tener una sola instancia de la BD  
open  que crea la BD si no existe, inicia la conexión  
create table  intenta drop, si no existe continúa, crea  
runquery ejecuta sentencia/transacción, crear, insertar  
free  si ocurre algún error  
close  finaliza la conexión  
check the result, etc  

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
