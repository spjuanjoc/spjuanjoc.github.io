---
title: "Hola Mundo C++"
date: 2020-10-25
last_modified_at: 2020-10-29

categories:
  - Principiante
tags:
  - C++

excerpt: "Hola mundo en informática hace referencia al primer ejercicio que se hace para visualizar la sintaxis de un lenguaje de programación."
header:
  teaser  : /assets/images/book.jpg
---

"Hola mundo" en informática hace referencia al primer ejercicio que se hace 
para visualizar la sintaxis de un lenguaje de programación.
Es recomendable buscar el ejemplo de "Hola mundo" de diversos lenguajes, analizar
cuantos resultados aparecen, visualizar su sintaxis, y decidir si es llamativo.

Este ejemplo de Hola Mundo en C++ es el más sencillo que se me ocurrió; 
no requiere instalar ninguna IDE, ni compilador, ni línea de comandos, 
tan solo requiere un navegador de internet.
Existen varios compiladores en línea, entre ellos [godbolt.org](https://godbolt.org/) (por Matt Godbolt),
 el cual se ha convertido en uno de los favoritos de la comunidad de programadores de C++.

## El código

El siguiente código incrustrado es modificable y se compila automáticamente.

<div>
<iframe width="100%" height="400px" 
    src="https://godbolt.org/e?hideEditorToolbars=true#g:!((g:!((g:!((g:!((h:codeEditor,i:(fontScale:10,j:1,lang:c%2B%2B,source:'%23include+%3Ccstdio%3E%0A%0Aint+main()%0A%7B%0A++puts(%22Hola+Mundo%22)%3B%0A%0A++return+0%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.17156286721503,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,fontScale:10,wrap:'1'),l:'5',n:'0',o:'%231+with+x86-64+gcc+(trunk)',t:'0')),header:(),k:45.828437132784956,l:'4',m:100,n:'0',o:'',s:0,t:'0')),l:'2',m:34.40860215053764,n:'0',o:'',t:'0'),(g:!((h:compiler,i:(compiler:gsnapshot,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'1',trim:'1'),fontScale:10,j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',source:1),l:'5',n:'0',o:'x86-64+gcc+(trunk)+(Editor+%231,+Compiler+%231)+C%2B%2B',t:'0')),header:(),l:'4',m:65.59139784946237,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>
</div>

Arriba a la izquierda se puede apreciar el código fuente, a la derecha el resultado de la 
ejecución, en el medio se observa el compilador y las opciones de compilación, 
y en la parte inferior el código ensamblador. 

## Explicación

Todo programa ejecutable en C++ requiere una función principal (`main`) que define el punto
de entrada del programa. Al ejecutarla retorna (`return`) un valor entero (`int`).

Para poder ejecutar operaciones de entrada-salida estándar (_std i-o_), como mostrar un mensaje, 
se requiere incluir (`#include`) el archivo de cabecera (`cstdio`) que contiene esa funcionalidad.
Finalmente se ejecuta la operación de escritura (`puts`) del mensaje deseado "Hola Mundo". 

Este código fuente se convierte a 10 instrucciones de código ensamblador, usa el compilador
`gcc` en la versión más reciente disponible (_trunk_: `gcc >= 10.2`) para la arquitectura `x86_64`, con el 
estándar `C++20`, y su ejecución finaliza exitosamente con el código `0`.

---
## Fuente
- [Conceptos básicos](https://es.cppreference.com/w/cpp/language/basic_concepts)
- Teaser photo by Jan Kahánek on Unsplash