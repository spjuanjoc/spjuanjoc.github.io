---
title           : "Complemento a dos: binarios negativos"
date            : 2021-03-26 
last_modified_at: 2021-10-11

categories:
  - Experto 
tags:
  - bitset
  - binarios
  - corriente
  - hexadecimal
  - cero negativo

excerpt: "Hay varias maneras de representar números binarios con signo; una es 
          por medio del Complemento a Dos, con la cual se debe tener en 
          cuenta el número de bits a representar."
header:
  og_image      : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  teaser        : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  overlay_image : /assets/images/unsplash-juliana-barquero-granos-de-cafe.jpg
  overlay_filter: 0.25 
  caption       : "Imagen por: [**Juliana Barquero**](https://unsplash.com/@juliebaa?utm_source=unsplash)
                   en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Hay varias maneras de representar números binarios con signo; una de ellas es
por medio del **Complemento a Dos** (en adelante $C_{2}$). Para ésta se debe
tener en cuenta el número de bits a representar, usualmente agrupados en bytes.

Es importante hacer la distinción entre el _valor_ obtenido del $C_{2}$ de un
número y la _representación_ en $C_{2}$ de un número.  
Por un lado está la _representación_ en $C_{2}$, la cual es la
visualización con signo de un entero sin signo, y se refiere a que al número
observado está en $C_{2}$.  
Por otro lado está el _valor_ del $C_{2}$ de un número $N$
que con una cantidad de bits $n$ se obtiene de la ecuación[^wiki]:

$$ C_{2}^{N} = 2^{n} - N $$

## Con signo y sin signo

En `C++` el número de bits de un número entero (`int`) depende del modelo de
datos[^data_model], en un sistema puede ser 32 bits y en otro 64. Sin embargo
hay tipos de enteros de tamaño explícito con lo cual se evita la dependencia en
la implementación: hay enteros de 32 bits con signo (`int32_t`) y sin
signo (`uint32_t`), como también hay de 8, 16 y 64.

Un ejemplo del uso de la representación en $C_{2}$ es en la comunicación con un
circuito externo que retorne su valor de corriente. Si ésta tiene un valor
negativo, por ejemplo `-200 mA`, entonces una buena forma de representación es
en $C_{2}$: en 16 bits en binario es `11111111 00111000`, en hexadecimal
`0xFF38`, en decimal sin signo `65336` (para `uint16_t`); a su vez `-200`
corresponde al _valor_ del $C_{2}$ de `200`.

$$ C_{2}^{200} = 2^{16} - 200 = 65336 $$

Representación:  

|$N$  |$C_{2}$ sin signo|$C_{2}$ con signo|Binario            |Hex     | bits($n$)|
|:--- |     :---        |       :---      |        :---       | :---   | :---:  |
|200  |`65'336`         |`-200`           |`11111111 00111000`|`0xFF38`|   16   |

## Rango de números

La cantidad de números a representar depende de los bits disponibles:
es menor para 8 bits que para 16. En números sin signo el rango para 8 bits es
de 0 a 255 ($2^{8}=256$ números), para 16 bits es de 0 a 65535 ($2^{16} =
65536$). Si se sobrepasan estos rangos entonces su representación queda
truncada.

```c++
const uint8_t dentro8 = 255;
const uint8_t fuera8  = 256;
fmt::print("Dentro: {}, fuera: {}\n", dentro8, fuera8);
//  Dentro: 255, fuera: 0

const uint16_t dentro16 = 65'535;
const uint16_t fuera16  = -1;
fmt::print("Dentro: {}, fuera: {}\n", dentro16, fuera16); 
//  Dentro: 65535, fuera: 65535
```

Para números con signo el rango en 8 bits va de `-128` a `127`, en 16 de
`-32768` a `32767`.  

```c++
  const int8_t  dentro8  = -128;
  const int8_t  fuera8   = -129;
  const int16_t dentro16 = -32'767;
  const int16_t fuera16  = -32'769;
  fmt::print("Dentro: {}, fuera: {}\n", dentro8, fuera8);
  fmt::print("Dentro: {}, fuera: {}\n", dentro16, fuera16);
//  Dentro: -128,   fuera: 127
//  Dentro: -32767, fuera: 32767
```

## Binarios con signo y magnitud

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

Lo anterior trae un inconveniente: la existencia de `+0` y `-0`. Adicionalmente, 
el rango de valores va desde `-127` hasta `127`.

```text
 0000 0000 =  0
 1000 0000 = -0
```

Una solución para este inconveniente es el $C_{2}$, y para comprenderlo hay que
ver primero el **Complemento a Uno**.

## Complemento a uno

La negación o el complemento a uno $C_{1}$ de un número binario es una operación que
consiste en invertir los unos por ceros y viceversa. Se representa con el
símbolo ${\sim}$ (virgulilla en español, _tilde_ en inglés)  

```text
        ~1 = 0
        ~0 = 1
 ~0111 111 = 100 000
```

Para representar números negativos con el $C_{1}$ usando cuatro bits (
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

Lo cual presenta el mismo inconveniente anterior: tener `+0` y `-0`. También el
rango va desde `-127` hasta `127`.  
No obstante, este es el primer paso para llegar al $C_{2}$.

## $C_{2}$ en términos de $C_{1}$

En términos del complemento a uno es:

$$ C_{2}^{N} = C_{1}^{N} + 1 $$

En binario:

$$ C_{2}^{b} = {\sim}(b) + 1 $$

El rango para 8 bits ahora va desde `-128` hasta `127` asegurando que haya
solamente un cero. En general:

$$(-2^{(n-1)}) \leq rango \leq (2^{(n-1)} - 1)$$

```text
  Binario   | Decimal    | Hex
    0101    =   5        | 5    | N
    1010    =  10 (-6)   | A    | complemento a uno
    +  1
    ----
    1011    =  -5        | B    | complemento a dos
  
  Binario   | Decimal    | Hex 
  00110010  =   50       | 0x32 | N
  11001101  =  205 (-51) | 0xCD | complemento a uno
  +      1
  --------
  11001110  =  -50       | 0xCE | complemento a dos
```

## Valor del $C_{2}$ en C++

Una manera de manipular bits en `C++` es por medio de `std::bitset` y usando
operadores de bits (`& | ^ ~`). Así, el $C_{2}$ expresado en términos 
del $C_{1}$ en `C++` corresponde a la ecuación:  

```c++
#include <fmt/core.h>
#include <bitset>

template<typename T>
auto complementoADos(const T& valor)
{
  auto valor_bits = std::bitset<8 * sizeof(valor)>(valor);
  const auto c2   = ~(valor_bits).to_ulong() + 1; 
  // c = ~(b) + 1

  return c2;
}

int main()
{
  int16_t resultado = complementoADos(200);
  fmt::print("{}\n", resultado); // -200
}
```

El valor del resultado depende del tipo de la variable en la que se almacena.
Si se almacena en un entero con signo, en este caso un entero de 16 bits con
signo (`int16_t`), su representación será el $C_{2}$.

## Representación en $C_{2}$ en C++

También depende del tipo de dato en el que se almacena el número. Como `-200` es
el $C_{2}$ de `200`, para obtener sus representaciones en hexadecimal y binario
hace falta tratar el valor original como un entero sin signo de tamaño explícito
de 16 bits:

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

Lo cual es corresponde a la representación vista al principio, y los bytes que
el circuito externo debe enviar como repuesta al preguntarle por su corriente
cuando es `-200 mA`.  

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

---

## Fuentes

- Computerphile: [Why we use Two's Complement](https://youtu.be/lKTsv6iVxV4)
- Jason Turner: [C++ Weekly-243-The magic of Two's Complement binary math](https://youtu.be/s89pOAC_X08)
- Electronics tutorials: [Signed binary numbers](https://www.electronics-tutorials.ws/binary/signed-binary-numbers.html)

[^wiki]: Ver [Complemento a dos](https://es.wikipedia.org/wiki/Complemento_a_dos)
[^data_model]: Ver [Modelos de datos](https://es.cppreference.com/w/cpp/language/types)
