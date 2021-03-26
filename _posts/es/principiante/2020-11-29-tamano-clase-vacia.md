---
title           : "Tamaño de una clase vacía"
date            : 2020-11-29
last_modified_at: 2020-11-29

categories:
  - Principiante
tags:
  - C++

excerpt: "Una clase vacía es aquella que no contiene variables miembro, y contiene
          por lo menos una función miembro."
header:
  og_image      : /assets/images/unsplash-kaleb-nimz-empty-chair.jpg
  teaser        : /assets/images/unsplash-kaleb-nimz-empty-chair.jpg
  overlay_image : /assets/images/unsplash-kaleb-nimz-empty-chair.jpg
  overlay_filter: 0.5
  caption       : "Imagen por: [**Kaleb Nimz**](https://unsplash.com/@kalebnimz?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Una clase vacía es aquella que no contiene variables miembro, y contiene
por lo menos una función miembro. Su tamaño mínimo es:
- Si ninguno de sus métodos es virtual: 
    - `1 byte`.
- Si contiene al menos un método virtual: El tamaño de un puntero virtual
    - `4 bytes` para `x86-64`.
    - `8 bytes` para  `x64`.
- En ningún caso: 
    - `0 bytes`. 

La razón de que no pueda ser cero es que lo mínimo que debe tener un objeto de 
una clase es su dirección de memoria, así se pueden distinguir varios objetos 
de la misma clase.

## Sin métodos virtuales

El tamaño mínimo para una clase/estructura vacía que no tenga ningún método virtual
será de `1 byte` para:

1. Una clase sin miembros, que no tiene sentido aunque se podría definir como:
    ```c++
    struct SinMiembros {};
    ```

2. Una clase con uno o varios métodos no virtuales:
    ```c++
    struct SinVirtual {
      SinVirtual()  {std::cout << "constructor\n";}
      ~SinVirtual() {std::cout << "destructor\n";}
      void foo()    {std::cout << "foo\n";}
      int barfoo()  {std::cout << "bar\n"; return 0;}
    };
    ```
   Así, teniendo varias instancias de esa clase, cada instancia tendrá su 
   identificador único:
    ```c++
    SinVirtual a, b, c;
    std::cout << "Tamaño de a:" << sizeof(a) << '\n';
    std::cout << "a:" << &a << "\n b:" << &b << "\n c:" << &c;
    ```
   El resultado será: 
    ```
    Tamaño de a: 1
    a: 0x7ffcecfe0d2d 
    b: 0x7ffcecfe0d2e 
    c: 0x7ffcecfe0d2f 
    ```

## Con métodos virtuales

Cuando una clase tiene por lo menos un método virtual, puro o no, automáticamente 
se le adicionará un puntero virtual (`v-pointer`) que apunta a la tabla virtual 
(`v-table`) donde se almacenan referencias a estos métodos. Por lo tanto el 
tamaño mínimo de la clase será el de un puntero, y dependerá de la arquitectura 
usada: de 32 bits (`4 bytes`) o de 64 bits (`8 bytes`).

```c++
struct ConVirtualPuro {
  virtual void foo() = 0;
};

struct ConVirtual {
  ConVirtual() {std::cout << "constructor\n";}
  virtual ~ConVirtual() = default;
};
int main(){
  std::cout << "Clase con un método virtual puro: " 
            << sizeof(ConVirtualPuro) << '\n';
  std::cout << "Clase con un método virtual     : " 
            << sizeof(ConVirtual) << '\n';
}
```

El resultado será:

    Clase con un método virtual puro: 8
    Clase con un método virtual     : 8

---

## Fuentes
- Rakesh Singh: [What is size of an Empty Class in C++?](https://www.interviewsansar.com/size-of-an-empty-class/)
