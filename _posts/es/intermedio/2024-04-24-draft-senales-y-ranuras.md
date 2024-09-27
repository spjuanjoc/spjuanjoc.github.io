---
title           : "Señales y Ranuras"
date            : 2024-04-22
last_modified_at: 2024-09-24

categories:
  - Intermedio
tags:
  - Señales
  - Ranuras
  - Observable
  - publisher / subscriber

excerpt: "Objetos observables."

header:
  og_image      : /assets/images/unsplash-chad-peltola-mailbox.jpg
  teaser        : /assets/images/unsplash-chad-peltola-mailbox.jpg
  overlay_image : /assets/images/unsplash-chad-peltola-mailbox.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Chad Peltola**](https://unsplash.com/@chadpeltola?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

Antes de comenzar, es preciso aclarar que las Señales y Ranuras de las que se
habla en esta publicación se refieren a las creadas por usuarios, que son
distintas a las señales del sistema operativo incluidas en `std::signal`.
[README.md](../../../../../hub/ohmyzsh-config/README.md)
## Qué son

Señales y Ranuras se refiere a una funcionalidad para comunicación entre
objetos. En C++ es un término acuñado a Qt[^1] enfocado a la comunicación en
interfaces gráficas.
También pueden verse como una funcionalidad para desacoplar un remitente (la
señal) de sus destinatarios (las ranuras).

Una señal es un objeto que puede informar a otros cuando algo en su estado es
modificado. De manera más general, se refiere a la notificación de que algo
ocurrió en el sistema, representa la ocurrencia de alguna acción
definida.

Una característica importante de las señales es que pueden ser rastreables,
es decir, que otros objetos pueden enterarse cuando una señal es emitida siempre
y cuando la estén observando. Pueden publicar una información determinada.

Por otro lado, una ranura es una funcionalidad que puede ejecutarse cuando una
señal se conecta a ella. Puede ser un método ordinario, una función libre, una
función lambda. 

Otro concepto relevante es el de Activación, que se refiere a cuando una señal
es emitida, y la ranura conectada es invocada.
Una conexión es la asociación entre una señal y una ranura; puede haber
múltiples conexiónes entre señales y ranuras.

La señal es observable, la ranura es el observador. La señal gestiona la
conexión, la ranura ejecuta la funcionalidad requerida.

## Para qué sirven 

Las Señales y Ranuras se usan comúnmente como concepto para formar el patrón
Observador, para comunicación asíncrona de entrada-salida (_io_). Este patrón es
ampliamente usado en la comunicación entre interfaces gráficas y sus
funcionalidades, por ejemplo, lo que debería ejecutarse al presionar un botón,
sin que se bloquee el procesamiento de la interfaz gráfica, ni la interacción
del usuario.
Suponiendo que se tiene una pantalla con un botón para guardar un documento,
al presionar el botón de Guardar se emite una señal; esa señal está previamente
conectada a una ranura que ejecuta los procesos requeridos para que el documento
sea almacenado.

## Bibliotecas para manejo de señales

Algunas de las bibliotecas destacadas para el manejo de señales en C++ son:

| Biblioteca     | Paquete       |
|:---------------|:--------------|
| libsigc++      | sigc++-3      |
| Boost.Signals2 | Boost         |
| signals-light  | signals-light |

En Boost, la biblioteca de señales (_Signals2_) es una implementación de un
sistema de señales y ranuras administradas. Las señales representan los objetos 
de devolución de llamadas (_callbacks_), editores (_publishers_), o eventos.
Las ranuras representan los _callback receivers_, subscriptores 
(_subscribers_), u objetivos de los eventos, los cuales son invocados cuando la
señal es emitida. La precede _Signals1_ que fue marcada como obsoleta, ya que no
funciona correctamente en sistemas multi-hilos-

---

## Ejemplo

patrón Observer

---

[std::signal](https://es.cppreference.com/w/cpp/utility/program/signal)

[CopperSpice CsSignal](https://www.copperspice.com/)

[Boost.Signals2](https://www.boost.org/doc/libs/1_86_0/doc/html/signals2.html#id-1.3.35.3.5)

[Qt](https://doc.qt.io/qt-6/signalsandslots.html)

[signals-light](https://github.com/a-n-t-h-o-n-y/signals-light/)

[libsigcpp](https://github.com/libsigcplusplus/libsigcplusplus)
