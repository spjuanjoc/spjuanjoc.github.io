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

excerpt: "Señales y Ranuras se refiere a una funcionalidad para comunicación 
          entre objetos. En `C++` es un término acuñado a `Qt` enfocado en la 
          comunicación en interfaces gráficas. También es visto como una
          funcionalidad para desacoplar un remitente (la señal) de sus 
          destinatarios (las ranuras)"

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
distintas a las señales del sistema operativo incluidas en `std::signal`[^1],
tales como `SIGSEGV` (Acceso de memoria inválido; como fallo de segmentación) o
`SIGFPE` (Operación aritmética errónea; como división por cero).

## Qué son

Señales y Ranuras se refiere a una funcionalidad para comunicación entre
objetos. En C++ es un término acuñado a Qt[^2] enfocado en la comunicación en
interfaces gráficas.
También es visto como una funcionalidad para desacoplar un remitente (la
señal) de sus destinatarios (las ranuras).

Una señal es un objeto que puede informar a otros cuando algo en su estado
interno es modificado. De manera más general, se refiere a la notificación de
que algo ocurrió en el sistema, representa la ocurrencia de alguna acción
definida.

Una característica importante de las señales es que pueden ser rastreables,
es decir, que otros objetos pueden enterarse cuando una señal es emitida siempre
y cuando la estén observando. Pueden publicar una información determinada.

Por otro lado, una ranura es una funcionalidad que puede ejecutarse cuando una
señal se conecta a ella. Puede ser un método ordinario, una función libre, una
expresión lambda. 

Otros conceptos relevantes son los de Activación y Conexión; activación se
refiere a cuando una señal es emitida y la ranura conectada es invocada; una
conexión es la asociación entre una señal y una ranura; puede haber múltiples
conexiones entre señales y ranuras, es decir, varias ranuras pueden estar
conectadas a una señal, y varias señales pueden tener conexión a una ranura, 
teniendo en cuenta que cada conexión es independiente.

La señal es observable, la ranura es el observador. La señal gestiona la
conexión, la ranura ejecuta la funcionalidad requerida.

## Para qué sirven

Las Señales y Ranuras se usan comúnmente como concepto para formar el patrón
Observador, para comunicación asíncrona de entrada-salida (_io_). Este patrón es
ampliamente usado en la comunicación entre interfaces gráficas y sus
funcionalidades; por ejemplo, el procesamiento que debería ejecutarse al 
presionar un botón en la interfaz gráfica.

Suponiendo que se tiene una pantalla con un botón para guardar un documento,
al presionar el botón de Guardar se emite una señal; esa señal está previamente
conectada a una ranura que ejecuta los procesos requeridos para tomar el 
documento, elegir la forma de almacenamiento, y realizar el guardado.

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
funciona correctamente en aplicaciones multi-hilo.

## Ejemplo

Un ejemplo simple que funciona con las tres bibliotecas mencionadas es una 
conexión sencilla de una señal con dos ranuras:

```c++
#include <boost/signals2.hpp>
#include <print>

static void onPrint(const std::string& message) { 
  std::print("{}\n", message);
}

int main() {
  boost::signals2::signal<void(const std::string&)> printTextSignal;

  const auto slot1 = printTextSignal.connect([](const auto& name) { onPrint(name); });
  const auto slot2 = printTextSignal.connect(&onPrint);

  printTextSignal("Hola Boost.Signals2");

  slot1.disconnect();
  slot2.disconnect();
  
  printTextSignal("Adiós Boost.Signals2");

  return 0;
}
```

El resultado es la ejecución de las dos ranuras cuando se emite la señal:

```text
Hola Boost.Signals2
Hola Boost.Signals2
```

La función `onPrint` es la ranura; por sí sola es una función común.  
`printTextSignal` es un objeto invocable tipo señal (en este caso de la biblioteca
de Boost.Signals2) que acepta como argumento una `std::string` y retorna
`void`.  
`slot1` y `slot2` son las conexiones de la señal a dos ranuras que invocan la
función `onPrint`.  
El llamado a `printTextSignal` con el argumento de texto emite la señal, por lo
que las dos ranuras se enteran y ejecutan `std::print` para el mensaje
contenido.  
Finalmente, las dos conexiones son finalizadas; futuros llamados a
`printTextSignal` no ejecutan la funcionalidad dado que ya no hay conexión, por
lo cual no se imprime el segundo mensaje `Adiós Boost.Signals2`.

Con `libsigcpp` es bastante similar; la emisión de la señal es
explícitamente nombrada:

```c++
#include <sigc++/sigc++.h>
//...
  sigc::signal<void(const std::string&)> printTextSignal;
  const auto slot1 = printTextSignal.connect(sigc::ptr_fun(&onPrint));
  printTextSignal.emit("Hola sigc++-3");
//...
```

```text
Hola sigc++-3
```

Con `signals-light` se puede hacer un conteo de las ranuras conectadas

```c++
#include <signals_light/signal.hpp>
//...

  sl::Signal<void(const std::string&)> printTextSignal;
  const auto slot1 = printTextSignal.connect([](const auto& name){onPrint(name);});
  const auto slot2 = printTextSignal.connect(&onPrint);
  printTextSignal("Hola signals-light");
  std::print("Ranuras conectadas: {}", printTextSignal.slot_count());
//...
```

```text
Hola signals-light
Hola signals-light
Ranuras conectadas: 2
```

---

[^1]: [std::signal](https://es.cppreference.com/w/cpp/utility/program/signal)
[^2]: [Qt](https://doc.qt.io/qt-6/signalsandslots.html)

- [Boost.Signals2](https://www.boost.org/doc/libs/1_86_0/doc/html/signals2.html#id-1.3.35.3.5)
- [CopperSpice CsSignal](https://www.copperspice.com/)
- [libsigcpp](https://github.com/libsigcplusplus/libsigcplusplus)
- [signals-light](https://github.com/a-n-t-h-o-n-y/signals-light/)
