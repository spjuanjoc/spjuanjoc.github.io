---
title: "Hola Mundo"
date: 2020-10-25T12:00:00-05:00
last_modified_at: 2020-10-26T20:00:00-05:00
categories:
  - Principiante
tags:
  - C++
---

"Hola mundo" en informática hace referencia al primer ejercicio a realizar a la hora
de querer visualizar la sintaxis de un lenguaje de programación.
Mi recomendación es buscar el ejemplo de "Hola mundo" de diversos lenguajes, analizar
cuantos resultados aparecen, visualizar su sintaxis y decidir si es llamativo o no.

Este ejemplo de Hola Mundo en C++ no requiere instalar ninguna IDE, ni compilador, ni línea de comandos; 
tan solo usando un navegador de internet, y gracias a Matt Godbolt, es posible
tener una herramienta para compilar en línea. Aunque existen varias, [godbolt.org](https://godbolt.org/) se
ha convertido en una de las favoritas de la comunidad.

<iframe width="100%" height="400px" src="https://godbolt.org/e?readOnly=false&hideEditorToolbars=true#g:!((g:!((g:!((g:!((h:codeEditor,i:(fontScale:10,j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:'%23include+%3Ccstdio%3E%0A%0Aint+main()%0A%7B%0A++puts(%22Hola+Mundo%22)%3B%0A%0A++return+0%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.17156286721503,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,fontScale:10,wrap:'1'),l:'5',n:'0',o:'%231+with+x86-64+gcc+(trunk)',t:'0')),header:(),k:45.828437132784956,l:'4',m:100,n:'0',o:'',s:0,t:'0')),l:'2',m:34.40860215053764,n:'0',o:'',t:'0'),(g:!((h:compiler,i:(compiler:gsnapshot,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'1',trim:'1'),fontScale:10,j:1,lang:c%2B%2B,libs:!(),options:'-std%3Dc%2B%2B20',selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'x86-64+gcc+(trunk)+(Editor+%231,+Compiler+%231)+C%2B%2B',t:'0')),header:(),l:'4',m:65.59139784946237,n:'0',o:'',s:0,t:'0')),l:'3',n:'0',o:'',t:'0')),version:4"></iframe>

Arriba a la izquierda se puede apreciar el código fuente, a la derecha el resultado de la 
ejecución, en el medio se observa el compilador y las opciones de compilación, 
y en la parte inferior el código ensamblador.
