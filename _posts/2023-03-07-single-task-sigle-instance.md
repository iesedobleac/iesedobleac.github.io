---
layout: post
title: "¿SingleTask y SingleInstance? Aprende a diferenciarlos"
author: isaac
categories: [Android, Manifest]
image: assets/images/post_images/launch_mode_task.webp
---

El otro día **estaba trabajando con los DynamicLinks de Firebase.** Para probar la funcionalidad lo que hacía era abrir de notas de Google, añadir el link
que había configurado y abrirlo, viendo si navegaba correctamente a mi aplicación 🚢

Todo parecía correcto hasta que **fui a la pila de tareas en segundo plano y vi que mi aplicación se había lanzado sobre la propia de Google** en vez
de lanzarse en su propia tarea. La solución se encontraba en declarar la propiedad `launchMode` 🚀

#### ¿Qué es launchMode?

> Instrucción para el inicio de una actividad. Existen cuatro modos que funcionan junto a los marcadores de actividad en objetos Intent para determinar
> lo que debe ocurrir cuando se llama a la actividad para controlar un intent. Son las siguientes:
>
> - standard
> - singleTop
> - singleTask
> - singleInstance
> - singleInstancePerTask
>
> Por defecto si no sobreescribimos esta propiedad, funciona como standard

Depende del uso que le quieras dar a tu aplicación puedes usar uno u otro, **pero hay dos en concreto que de primeras puede parecer que hacen lo mismo,
pero tienen ligeras diferencias** 🤔

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    ...

        <activity
            android:name=".MainActivity"
            android:launchMode="singleTask"
            android:exported="true">

            ...

        </activity>

    </application>

</manifest>
```

<h4 id="launch-mode-on-new-intent"> Antes de entrar en materia</h4>

Durante la explicación voy a mencionar varias veces el método `onNewIntent()`. **Para que te quede claro voy a explicarte de forma breve cuál es su función:**

> El método onNewIntent() está pensado como punto de entrada para actividades que ya se están ejecutando en algún otro lugar de la pila y, por lo tanto,
> no pueden llamar a onCreate(). Desde el punto de vista del ciclo de vida de las actividades es necesario llamar a onPause() antes de onNewIntent().

#### El modo singleTask

El activity configurado como `singleTask` **tendrá diferentes comportamientos en función del estado de la pila de actividades en el momento en el que lanzamos
la nueva actividad.** Para los ejemplos, todos los activities están configurados de este modo.

{% include images.html url="https://i.ibb.co/BL6SphJ/launch-mode-01.png" description="Ejemplo sobre singleTask. Lanzas un activity configurado de esta forma" %}

Tienes en tu pila de activities: D, C, B, A. **El activity D se encuentra en primer lugar y lanzas C.** Ahora, **gracias a `singleTask`, el activity C
pasa a ser el primero** en la pila, el cual es llamado a través del método <a href="#launch-mode-on-new-intent">onNewIntent()</a>, **llevándose por delante
todos aquellos que hubiera en su camino** 🤯 Te doy otro ejemplo:

{% include images.html url="https://i.ibb.co/26Y2VCx/launch-mode-03.png" description="Lanzas el mismo activity que ya se encontraba al principio en la pila" %}

**Tu pila de activities es: C, B, A y lanzas de nuevo el activity C**, ¿qué ocurrirá? Como C ya se encuentra en la primera posición, **la pila se queda como
estaba.**

{% include images.html url="https://i.ibb.co/26Y2VCx/launch-mode-03.png" description="Lanzas el activity que se encuentra al final de la pila" %}

Para verlo en un ejemplo más drástico, **vamos a lanzar el activity A, el cual se encuentra el último en la pila.** Verás que ahora el único activity que ha
quedado es el A, ya que **al estar el último, ha machacado el resto dejando solamente este** 💣

#### El modo singleInstance

Para explicarte este modo **imagínate una carretera con varios carriles donde cada carril será una pila de activities y cada coche es un activity.** Un
activity configurado en el modo `singleInstance` quiere ir solo por su carril, es decir:

{% include images.html url="https://i.ibb.co/vVJTRjh/launch-mode-02.png" description="Ejemplo sobre singleInstance. Lanzas un activity configurado de esta forma" %}

**Nuestra pila está formada por tres activities: C, B, A.** Ahora tienes un activity D el cual está configurado como `singleInstance`, pues **en vez de sumarse
a la misma pila de activities, se crea una segunda pila donde estará este activity que acabas de añadir** 💡

{% include images.html url="https://i.ibb.co/vhnFwHx/launch-mode-05.png" description="Al lanzar un activity no configurado como singleInstance, se añade a la pila principal" %}

¿Qué pasa si añades un **activity** que **no está configurado como `singleInstance`?** Como ves en la imagen, **se añade a la pila principal en primera posición.**

{% include images.html url="https://i.ibb.co/ct1s8wV/launch-mode-06.png" description="Lanzas el activity D que ya estaba instanciado" %}

**Si lanzas el mismo activity que tenías configurado como `singleInstance`,** este no se va a añadir a la pila principal, sino que en este caso, al ya estar creado,
**se va a volver a instanciar llamando al método <a href="#launch-mode-on-new-intent">onNewIntent()</a>** 🪄

#### En resumen

Un activity con **el modo `singleTask` machacará aquellos que se encuentre en su camino por encima de la pila si ya se encontraba instanciado**, de lo contrario se
instanciará uno nuevo en la primera posición.

El modo **`singleInstance` creará una nueva pila donde estará solo ese activity. Los activities que no estén así configurados, se añadirán a la pila principal**. No
tienes que preocuparte por la gestión de las diferentes pilas, pues **de esto se encarga el sistema** 👽
