---
title: "Acrónimos"
layout: home
permalink: /acronyms/
entries_layout: grid

---

## Acrónimos, siglas, modismos, términos usados frecuentemente

Una de las dificultades con las que me suelo encontrar cuando llego a un nuevo
grupo o equipo de desarrollo de software es adaptarme a la terminología que usan
la mayoría de personas.

Cuando uno es el nuevo del grupo y las demás personas ya llevan tiempo en él
se pueden identificar patrones en la manera en la que se comunican. Es muy común
que se usen siglas, modismos, palabras clave, términos de los cuales se puede
pensar que son de conocimiento global. No siempre es intuitivo saber su
significado, con frecuencia son en múltiples idiomas (por lo menos en inglés y
español) y la mayoría de las veces es complejo adivinar los significados.

Recuerdo cuando llegué a un equipo que llevaba cierto tiempo de estar conformado
y hablaban de los objetos _callables_ con mucha naturalidad. Yo no entendía bien
de lo que estaban hablando. Aunque sí comprendía el contexto, se me dificultó un
montón entender esa palabra. La raíz de mis inconvenientes para entenderla fue
que al escucharla yo solamente entendía una palabra que no conocía, en
_spanglish_ el equipo la pronunciaba algo así como _coleibol_.
Al final entendí que se referían a _callable_, discutimos su pronunciación y
todo fue más sencillo.

Esa anécdota me inspiró a crear un _vademecum_ con algunos de los términos que
veía o escuchaba con frecuencia, y que quizás no me parecían tan obvios,
intuitivos, o globales en su momento.

Me parece relevante también identificar que esos términos pueden ser manejados
más fácilmente dependiendo del contexto en el que se tratan.

En el ámbito de programación en general:

| Sigla       | Descripción                                        | Significado                                    |
|:------------|:---------------------------------------------------|:-----------------------------------------------|
| ABI         | Application Binary Interface                       | Interfaz binaria de una aplicación             |
| API         | Application Program Interface                      | Interfaz del programa de una aplicación        |
| Boilerplate | Boilerplate code                                   | Código repetitivo que se debe copiar y pegar   |
| c14n        | canonicalization                                   | Usar la forma estándar/ canónica               |
| i18n        | internationalization                               | for string translations to different languages |
| IIFE        | Immediately Invoked Functional Expression: Lambdas | Expresión Funcional Inmediatamente Invocada    |
| KISS        | Keep It Simple                                     | Principio de diseño: Mantener simplicidad      |
| LOC         | Lines Of Code                                      | Número de líneas de código                     |
| NVI         | Non-Virtual Interface                              | Patrón de diseño de interfaces                 |
| RAII        | Resource Acquisition Is Initialization             | Tiempo de vida determinístico para objetos     |

---

En el ámbito más cercano a C++ y lenguajes similares:

| Sigla             | Descripción                                         | Significado                                                       |
|:------------------|:----------------------------------------------------|:------------------------------------------------------------------|
| ODR               | One-Definition Rule                                 | Regla de Una Sola definición                                      |
| PIMPL             | Pointer to implementation                           | Puntero a implementación                                          |
| SFINAE            | Substitution Failure Is Not An Error. For templates | Falla en sustitución no es un error. Para plantillas              |
| SIOF              | Static initialization Order Fiasco                  | Fiasco en el orden de la inicialización estática                  |
| SSO               | Small String Optimization                           | Optimización de pequeñas cadenas de caracteres                    |
| State-less lambda | A lambda with no parameters                         | Lambda sin estado, sin parámetros, sin variables miembro          |
| TU                | Translation Unit                                    | Unidad de traducción a lenguaje de máquina. Archivos Objeto _*.o_ |
| UB                | Undefined Behavior                                  | Comportamiento indefinido                                         |

---

En el ámbito general de terminología de software, como en chats o solicitudes de
incorporación de cambios:

| Sigla | Descripción              | Significado                                |
|:------|:-------------------------|:-------------------------------------------|
| AFAIK | As Far As I Know         | Hasta donde llega mi conocimiento          |
| DRY   | Don’t Repeat Yourself    | No repitas código                          |
| IMO   | In My Opinion            | En mi opinión                              |
| IMHO  | In My Humble Opinion     | En mi humilde opinión                      |
| LGTM  | Looks Good To Me         | Se ve bien (el MR/PR/Cambio) para mí       |
| MR    | Merge Request            | Solicitud de incorporación de cambios      |
| PR    | Pull Request             | Solicitud de incorporación de cambios      |
| PTAL  | Please Take Another Look | Revisa de nuevo (al hacer ajustes a MR/PR) |
| YAGNI | You Ain't Gonna Need It  | No lo vas a necesitar                      |

No siempre he encontrado el significado al hacer una búsqueda simple, así que
encontré algunos recursos donde se pueden desambiguar acrónimos: 
[All acronyms](https://www.allacronyms.com/)

---
