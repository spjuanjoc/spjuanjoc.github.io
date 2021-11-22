---
title           : "Depuración de scripts Python en CLion"
date            : 2021-11-20
last_modified_at: 2021-11-21

categories:
  - Otros
tags:
  - python
  - CLion
  - depurar

excerpt: "No es sorpresa ver que Jetbrains tiene muy bien documentada la manera 
          de configurar un intérprete de python en CLion, y que bastan solamente 
          dos sencillos pasos para lograr finalizar las configuraciones de
          ejecución y depuración."

header:
  og_image      : /assets/images/unsplash-leandro-fregoni-snake.jpg
  teaser        : /assets/images/unsplash-leandro-fregoni-snake.jpg
  overlay_image : /assets/images/unsplash-leandro-fregoni-snake.jpg
  overlay_filter: 0.25
  caption       : "Imagen por: [**Leandro Fregoni**](https://unsplash.com/@okcapturas?utm_source=unsplash) 
                  en [**Unsplash**](https://unsplash.com/?utm_source=unsplash)"

---

CLion: El Entorno Integrado para Desarrollo de software enfocado en el lenguaje
C++ es un producto de Jetbrains. 

Cuando una persona que escribe software se acostumbra a una IDE es dificil
que la cambie o que pruebe otras, a menos que tenga los mismos atajos de
teclado, que los configure manualmente, o que decida darse el tiempo de
aprenderlos y acostumbrarse a los nuevos.

Algo similar me pasó cuando comencé a usar CLion por recomendación de un colega,
al principio no estaba convencido de querer cambiar porque ya tenía claros los
atajos de la IDE anterior. Después de acostumbrarme, ahora la uso incluso para
escribir en otros lenguajes, como markdown, typescript, y hasta python.

En algún programa en python que estaba escribiendo quise probar
cuidadosamente unas cuantas líneas; usualmente lanzaba el programa a correr por
la terminal, pero en esta ocasión requería depurarlo. No fue sorpresa ver que
Jetbrains tiene muy bien documentada la manera de configurar un intérprete de
python en CLion, y que bastan solamente dos sencillos pasos para lograr
finalizar las configuraciones de ejecución y depuración: Configurar un
intérprete de Python en CLion para depurar en un ambiente virtual.[^jetbrains]

## Configuración inicial

El primer paso es agregar un intérprete (o reusar uno existente). Se puede
configurar uno por proyecto, aunque es muy útil crear uno general y reusarlo en
otros proyectos. Se configura por:

    Settings > Build > Python interpreter

Allí aparece un engranaje en la esquina derecha superior, y al desplegar la
lista aparece la opción de añadir, seleccionando uno nuevo en la ubicación
actual, o uno existente buscando su ruta.  
Para reusarlo es conveniente marcar la casilla de _Make available to all 
projects_.

El segundo y último paso es agregar una plantilla de _ejecución / depuración_.
Desde `Run > Edit configurations` se selecciona una configuración de Python y se
llenan los campos, uno de ellos es el intérprete del paso anterior:

```shell
Add Run/Debug configuration

+: Add new configuration: Python

Name: nombre del programa
Script path: /ruta/al/programa
Parameters: ... 
Interpreter: el recién creado / existente
```


## Crear un programa 

Un "hola mundo" en python puede ser tan sencillo como una sola línea; este
ejemplo contiene unas cuantas líneas de más para probar la depuración:

```python
#!/usr/bin/env python3
"""
description : ¿?
author      : yo
date        : hoy
"""
# coding: utf-8

import os

def main():
    try:
        cwd = os.getcwd()
        print(f"Hola mundo desde {cwd}")

    except KeyboardInterrupt:
        pass

if __name__ == "__main__":
    main()
```

El resultado de la ejecución es algo similar a lo siguiente (en Ubuntu):

```pycon
/home/user/code/hello_py/venv/bin/python /home/user/code/hello_py/hello_world.py
Hola mundo desde /home/user/code/hello_py/

Process finished with exit code 0
```


## Depurar

Teniendo configurados el intérprete y la plantilla de _Ejecución / Depuración_
ya se pueden usar los atajos de la IDE: `Shift+F10` para ejecutar, `Shift+F9`
para depurar, `Ctrl+F2` para interrumpir la depuración.  


## Fuentes

- [CLion: A cross-platform IDE for C and C++](https://www.jetbrains.com/clion/)
- [Configure Python interpreters in CLion](https://www.jetbrains.com/help/clion/configuring-available-python-interpreters.html)

---
[^jetbrains]: [Python interpreters](https://www.jetbrains.com/help/clion/configuring-available-python-interpreters.html)
