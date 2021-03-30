---
title           : "Complemento a dos: binarios negativos"
date            : 2021-03-26 
last_modified_at: 2021-03-28

categories:
  - Experto 
tags:
  - C++
  - Bitset

excerpt: "Una de las maneras de representar números binarios con signo es por 
          medio del Complemento a dos. Usar esta representación permite 
          convertir una resta en una suma."
header:
  og_image      : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  teaser        : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  overlay_image : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  overlay_filter: 0.25 
  caption       : "Imagen por: [**Juliana Barquero**](https://unsplash.com/@juliebaa?utm_source=unsplash)
                   en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Hay varias maneras de representar números binarios con signo; una de ellas es
por medio del **Complemento a Dos**. Para esta forma se debe tener en cuenta el
número de bits a representar, usualmente agrupados en bytes.  
En `C++` el número de bits de un entero depende del modelo de datos [^1], en
sistema puede ser 32 bits y en otro 64. Sin embargo hay tipos de entero
explícitos para no depender de la implementación: hay enteros de 32 bits con
signo (`int32_t`) y sin signo (`uint32_t`), como también hay de 8, 16 y 64.

Un ejemplo del uso del complemento a dos es la comunicación con un circuito
externo que retorne su valor de corriente. Si ésta tiene un valor negativo, por
ejemplo `-200 mA`, entonces una buena forma de representación es por medio del
complemento a dos: en binario `11111111 00111000`, o en hexadecimal `0xFF38`.

## Rango de números

El rango de números a representar depende del número de bits disponibles:
no es lo mismo un número entero de 8 bits que de 16. Para números sin signo el
rango para 8 bits es de 0 a 255 (`2^8 = 256` números), para 16 bits es de de 0 a
65535 (`2^16 = 65536`).  
Si se intenta sobrepasar estos rangos entonces su representación será truncada.

```c++
  const uint8_t unsigned8bit = 255;
  fmt::print("{}\n", unsigned8bit); // 255 ok
  const uint8_t unsigned8bit1 = 256;
  fmt::print("{}\n", unsigned8bit1); // 0 truncado
  const uint8_t unsigned8bit2 = -1;
  fmt::print("{}\n", unsigned8bit2); // 255 truncado
  const uint16_t unsigned16bit = 65'537;
  fmt::print("{}\n", unsigned16bit); // 1 truncado
  const uint16_t unsigned16bit2 = -1;
  fmt::print("{}\n", unsigned16bit2); // 65535 truncado
```

El compilador se da cuenta de ésto y advierte que hay un
desbordamiento (`[-Woverflow]`).

Para números con signo el rango en 8 bits va de -128 a 127, en 16 de
-32768 a 32767.  

```c++
  const int8_t signed8bit = -128;
  fmt::print("{}\n", signed8bit); // -128 ok
  const int8_t signed8bit1 = -129;
  fmt::print("{}\n", signed8bit1); // 127 truncado
  const int16_t signed16bit = -32'767;
  fmt::print("{}\n", signed16bit); // -32767 ok
  const int16_t signed16bit1 = -32'769;
  fmt::print("{}\n", signed16bit1); // 32767 truncado
```

## Binarios con signo

Una manera de representar números binarios con signo es tomando un bit como el
indicador del signo, en este caso el bit número 8 a la izquierda, el bit más
significativo (MSB), donde cero indica que es positivo, uno indica que es
negativo:

```text
 8765 4321 Bit
      8421 n
 0111 1111 =  127
 1111 1111 = -127
 0000 0101 =  5
 1000 0101 = -5
```

Lo anterior trae un inconveniente: la existencia de +0 y -0. Adicionalmente, 
el rango de valores va desde -127 hasta 127.

```text
 0000 0000 =  0
 1000 0000 = -0
```

La solución para este inconveniente es precisamente el Complemento a dos, y para
entenderlo hace falta ver antes el complemento a uno.

## Complemento a uno

La negación o el complemento a uno de un número binario es una operación que
consiste en invertir los unos por ceros y viceversa. Se representa con el
símbolo `~` (virgulilla en español, tilde en inglés)  

```text
        ~1 = 0
        ~0 = 1
 ~0111 111 = 100 000
```

Para representar números negativos con el complemento a uno con cuatro bits (
`-7...+7`) se tiene:

```text
 4321 Bit
 8421 n
 0111 =  7
 0001 =  1
 0000 =  0
 1111 = -0
 1110 = -1 
 1000 = -7 
```

Lo cual presenta el mismo inconveniente anterior: tener +0 y -0. También el
rango va desde -127 hasta 127. Sin embargo este es el primer paso para llegar al
complemento a dos.

## Complemento a dos

Es el resultado del complemento a uno, más una unidad.

```text
b = 0000 0001
c = ~(b) + 1
```

El rango para 8 bits ahora va desde -128 hasta 127 ( `-2^(n-1)` hasta `2^(n-1) - 1` )
asegurando que haya solamente un cero.

```text
  Binario | Decimal
    0101  =   5      | Valor inicial
    1010  =  10 (-6) | complemento a uno
    +  1  
    ----  
    1011  =  -5      | complemento a dos
  
  Binario   | Hex  | Dec  
  00110010  = 0x32 |   50      | Valor inicial
  11001101  = 0xCD | 205 (-51) | complemento a uno
  +      1  
  --------  
  11001110  = 0xCE |  -50      | complemento a dos
```

## std::bitset

Una manera de manipular bits en `C++` es por medio de `std::bitset` y usando
operadores de bits (`& | ^ ~`). Así, el complemento a dos será la misma fórmula
ya vista:  
`c = ~(b) + 1`

```c++
#include <fmt/core.h>
#include <bitset>

template<typename T>
auto complementoADos(const T& valor)
{
  auto valor_bits = std::bitset<8 * sizeof(valor)>(valor);
  auto resultado  = ~(valor_bits).to_ulong() + 1;

  return resultado;
}

int main()
{
  int16_t resultado = complementoADos(200);
  fmt::print("{}\n", resultado); // -200
}
```

El valor del resultado depende del tipo de la variable en la que se almacena.
Si se almacena en un entero con signo, en este caso un entero de 16 bits con
signo (`int16_t`), su representación será el complemento a dos siempre y cuando
se encuentre dentro del rango.

## Representaciones

Finalmente, es importante entender que el complemento a dos también es una
representación, por lo cual también viene al caso observar la representación del
valor obtenido en el sistema hexadecimal y binario además del decimal. `-200` es
el complemento a dos de `200`, y para obtener sus representaciones en
hexadecimal y binario hace falta tratar el valor original como un entero sin
signo:  

```c++
  int16_t  con_signo = -200;
  uint16_t sin_signo = con_signo;
  fmt::print("ori: {}\n",   con_signo);
  fmt::print("dec: {}\n",   sin_signo);
  fmt::print("hex: {:x}\n", sin_signo);
  fmt::print("bin: {:b}\n", sin_signo);
```

Resulta en:  

```c++
ori: -200
dec: 65336
hex: ff38
bin: 1111111100111000
```

Lo cual es precisamente la representación vista al principio, y los bytes que
el circuito externo debe enviar como repuesta al preguntarle por su corriente
cuando esta es `-200 mA`.  

```c++
uint16_t sin_signo = 0xFF38;
int16_t  con_signo = sin_signo;
fmt::print("ori : {}\n",   sin_signo);
fmt::print("hex : {:x}\n", sin_signo);
fmt::print("bin : {:b}\n", sin_signo);
fmt::print("udec: {}\n",   con_signo);
```

Resulta en:  

```c++
ori : 65336
hex : ff38
bin : 1111111100111000
udec: -200
```

## Operaciones aritméticas

Usar el complemento a dos permite convertir una resta de binarios en una suma:

```text
x = a - b
c = ~(b) + 1 // complemento a dos de b
x = a + c
```

Lo cual tiene implicaciones importantes ya que en algunos computadores se
implementa la resta de esta manera.

## Consideraciones

Aunque programáticamente no es complejo implementar el complemento a dos, el
concepto puede ser lento de asimilar ya que visualizar correctamente las 
representaciones depende de los tipos de datos.


---

## Fuentes

- Computerphile: [Why we use Two's Complement](https://youtu.be/lKTsv6iVxV4)
- Jason Turner: [C++ Weekly-243-The magic of Two's Complement binary math](https://youtu.be/s89pOAC_X08)
- Electronics tutorials: [Signed binary numbers](https://www.electronics-tutorials.ws/binary/signed-binary-numbers.html)

[^1]: Ver [Modelos de datos](https://es.cppreference.com/w/cpp/language/types)
