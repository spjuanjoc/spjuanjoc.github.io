---
title           : "Orden de construcción y destrucción en herencia"
date            : 2021-08-22
last_modified_at: 2021-08-22

categories:
  - Principiante
tags:
  - C++
  - herencia
  - destructor
  - virtual

excerpt: "El orden de construcción es `A-B-C`. El orden de
          destrucción es el inverso: `C-B-A`."

header:
  og_image      : /assets/images/unsplash-zhivko-minkov-bricks-hd.jpg
  teaser        : /assets/images/unsplash-zhivko-minkov-bricks-hd.jpg
  overlay_image : /assets/images/unsplash-zhivko-minkov-bricks-hd.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Zhivko Minkov**](https://unsplash.com/@lazywhiskey?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

## Herencia

Es una característica del lenguaje que permite definir a una clase (Derivada) en
términos de otra (Base). En este caso Derivada hereda de Base, lo que significa
que Derivada está definida en términos de Base. Hay mucho más sobre el tema, 
pero por ahora es conveniente dejarlo en una herencia pública y sencilla 
(no múltiple).


## Construcción y destrucción

Teniendo varios niveles de herencia, y asumiendo la siguiente jerarquía:
_A_ es la clase base, _B_ hereda de _A_, _C_ hereda de _B_. El orden de construcción de un
objeto es `A-B-C`. El orden de destrucción es el inverso: `C-B-A`. El primer
sub-objeto en construirse es de la clase base que está más arriba _A_, a
partir de este se construye el de la siguiente en jerarquía _B_, y finalmente
el de la última clase derivada _C_.


## Código

Para ilustrarlo en código `C++`, se puede apreciar la construcción y destrucción
de los sub-objetos de una herencia pública simple en la cola (_stack_).

<iframe width="100%" height="400px"
  src="https://godbolt.org/e?hideEditorToolbars=true#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:12,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:'%23include+%3Cfmt/core.h%3E%0A%0Astruct+Abue+%7B%0A++Abue()++%7B+fmt::print(%22constructor+abue%5Cn%22)%3B+%7D%0A++~Abue()+%7B+fmt::print(%22destructor++abue%5Cn%22)%3B+%7D%0A%7D%3B%0A%0Astruct+Mama+:+public+Abue+%7B%0A++Mama()++%7B+fmt::print(%22++constructor+mama%5Cn%22)%3B+%7D%0A++~Mama()++%7B+fmt::print(%22++destructor++mama%5Cn%22)%3B+%7D%0A%7D%3B%0A%0Astruct+Yo+:+public+Mama+%7B%0A++Yo()++%7B+fmt::print(%22++++constructor+yo%5Cn%22)%3B+%7D%0A++~Yo()++%7B+fmt::print(%22++++destructor++yo%5Cn%22)%3B+%7D%0A%7D%3B%0A%0Aint+main()+%7B%0A++fmt::print(%22Hola+mundo%5Cn%22)%3B%0A++%7B%0A++++Yo+yo%3B%0A++%7D%0A++fmt::print(%22Adi%C3%B3s+mundo%5Cn%22)%3B%0A%0A++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:69.377990430622,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:gsnapshot,compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:16,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!((name:fmt,ver:trunk)),options:'-std%3Dc%2B%2B14',source:1,stdinPanelShown:'1',tree:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+(trunk)+(C%2B%2B,Editor+%231)',t:'0')),k:30.622009569377994,l:'4',m:100,n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4">
</iframe>


## Destructor virtual

De manera similar, se puede crear un objeto polimórfico alojado en la pila
(_heap_); sin embargo, para que la memoria sea liberada correctamente es
requerido que los destructores sean virtuales, de lo contrario se produce una
fuga de memoria pues solamente es invocado el destructor de la clase base.

En el ejemplo anterior se pueden realizar los ajustes:

```c++
struct Abue{
  virtual ~Abue() {/*...*/};
};
struct Mama : public Abue{
  ~Mama() override {/*...*/};
};
struct Yo : public Mama{
  ~Yo() override {/*...*/};
};
```

Ahora se puede crear un puntero que libera la memoria correctamente:

```c++
Abue* unYo = new Yo();
delete unYo;

std::unique_ptr<Abue> otroYo = std::make_unique<Yo>();
```

La recomendación es siempre usar destructores virtuales cuando se usa
herencia, teniendo en cuenta la [Regla de 5](https://es.cppreference.com/w/cpp/language/rule_of_three).  
Para `std::shared_ptr` el manejo es diferente.  
Para profundizar sobre el tema ver [^1] y [^2].


## Fuentes

- [Constructor](http://eel.is/c++draft/class.base.init)
- [Destructor](http://eel.is/c++draft/class.dtor)
- ISO Cpp [Multiple inheritance](https://isocpp.org/wiki/faq/multiple-inheritance#mi-vi-ctor-order)

---

[^1]: Ver [Understanding virtual tables](https://pabloariasal.github.io/2017/06/10/understanding-virtual-tables/)
[^2]: The Cherno [Virtual destructors in C++](https://youtu.be/jELbKhGkEi0) 
