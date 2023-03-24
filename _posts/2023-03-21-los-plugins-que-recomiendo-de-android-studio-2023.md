---
layout: post
title: "Plugins que uso y recomiendo de Android Studio en 2023"
author: isaac
categories: [Android, Plugins]
image: assets/images/post_images/android_studio_plugins/android_studio_plugins.webp
---

Cuando programo **uso herramientas a las que estoy tan acostumbrado que las considero como parte de Android Studio.** 

Esto ha cambiado cuando la semana pasada tuve que formatear mi ordenador y al comenzar a escribir código, notaba cosas que no funcionaban como siempre, y es 
cuando me di cuenta que **muchas de estas utilidades se tratan de plugins de terceros** que podemos instalar en el `IDE` 💡

#### ¿Qué es un plugin?

Antes de entrar en materia, quizás no sepas ni de lo que te estoy hablando cuando te nombro la palabra `plugin`. 

> En Android Studio y en muchos otros IDE, un `plugin` es una extensión que se instala dentro del propio programa que le dota de nuevas características o 
> automatiza otras de manera que puedas hacer más con menos ⚙️ 🚀

**Imagínate unas gafas graduadas** en un día que hace mucho sol, ¿qué haces? O bien tienes unas gafas de sol graduadas o bien usas tu propia mano como
si fuera una visera para que no te de el sol en la cara. 

{% include images.html url="/assets/images/post_images/android_studio_plugins/dazzle_light.gif" %}

Hay gafas que incorporan unos imanes a los que le puedes acoplar unos cristales con protección contra el sol. En este ejemplo **esos cristales serían el 
`plugin`** que se acopla a las gafas graduadas 🕶️

<!-- Imagen acorde -->

#### Plugins recomendados

**Existen un sinfin de `plugins` en Android Studio** y los que te voy a recomendar son **los que yo encuentro más útiles en mi día a día.** Son muy conocidos 
por la mayoría de programadores, pero **hoy te los quiero mostrar** por si no sabías de su existencia 😊

##### Rainbow Brackets

{% include images.html url="/assets/images/post_images/android_studio_plugins/rainbow_brackets.webp" description="Ejemplo de uso de Rainbow Brackets" %}

Cuando trabajas en una aplicación de gran tamaño, **hay veces que te puedes encontrar con funciones o fragmentos de código demasiado grandes** donde se hace 
tedioso saber donde comienza y donde termina. **Rainbow Brackets viene a solucionar esto** 🤯

Se trata de un `plugin` que **asigna un color a cada uno de los paréntesis, llaves, o corchetes** de una función, de tal forma que rápidamente veas el 
elemento de cierre que corresponde a ese mismo. 

{% include images.html url="/assets/images/post_images/android_studio_plugins/rainbow_brackets_xml.webp" description="Ejemplo de uso en archivos XML" %}

Como se puede apreciar en la imagen **también han añadido soporte para hacer algo similar en los archivos XML,** solo que aquí no solo colorea los elementos
de cierre y apertura sino que también lo hace con cada componente de la vista. **Esto es gracias a su compatibilidad con un amplio abanico de lenguajes** 
de programación:

> Java, Scala, Clojure, Kotlin, Python, Haskell, Agda, Rust, JavaScript, TypeScript, Erlang, Go, Groovy, Ruby, Swift, Elixir, ObjectiveC, PHP, HTML, XML, 
> SQL, Apex language, C#, Dart, Pug/Jade, Bash, Vue.js, C# Razor Pages, GLSL(the OpenGL Shading Language), Go Template, C++, C, Solidity

**Puedes encontrarlo en la store** de Android Studio como `Rainbow Brackets` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/10080-rainbow-brackets">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/rainbow_brackets_store.webp" %}

##### Atom Material Icons

{% include images.html url="/assets/images/post_images/android_studio_plugins/atom_icons_idea.webp" description="Previsualización de iconos con Atom Material Icons" %}

**¿Te gusta el estilo Material Design?** ¿Cansado de que tu Android Studio se vea soso y muy simple? ¿Estás leyendo esto como si fuera un anuncio de televisión? 

Con este `plugin` vas a poder **aplicar una serie de iconos, tanto a la interfaz de Android Studio como a las clases y archivos, con un estilo muy minimalista.**

Ten en cuenta que como **todo elemento extra que añades a Android Studio, esto consume más memoria**, por lo que este `plugin` es totalmente opcional. **Si notas 
que el rendimiento es malo o peor que antes, desinstálalo,** ya que **no aporta otra mejora más allá de lo visual** 📈

**Puedes encontrarlo en la store** de Android Studio como `Atom Material Icons` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/10044-atom-material-icons">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/atom_material_store.webp" %}

##### Code Glance Pro

{% include images.html url="/assets/images/post_images/android_studio_plugins/code_glance_pro.webp" description="Ejemplo de visualización de Code Glance en una clase Kotlin" %}

Si antes de Kotlin habías utilizado otro lenguaje de programación **es bastante probable que hayas usado Visual Studio Code** y eches de menos el método de 
desplazamiento que incorporaba. **Este `plugin` nos trae la misma funcionalidad a Android Studio** 👏🏼

Es quizás uno de los `plugins` que considero más útiles a la hora de movernos por nuestros archivos, pues **tendremos de un solo vistazo un minimapa con todo 
el código de tu clase** y si sabes en que punto se encuentra el método que quieres encontrar, **con un solo clic en el espacio dedicado a Code Glance puedes 
desplazarte hacía el** 💨

Además en el minimapa que genera `Code Glance` **podrás ver los mismos colores que tienen nuestras funciones** e incluso en que línea estamos apuntando nuestro 
cursor en ese momento para tener bien localizado nuestro código y hacer que seamos más ágiles programando 🖥️

**Puedes encontrarlo en la store** de Android Studio como `Code Glance Pro` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/18824-codeglance-pro">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/code_glance_store.webp" %}

##### ADB Idea

{% include images.html url="/assets/images/post_images/android_studio_plugins/adb_idea_plugin.webp" description="Ejemplo de visualización de Code Glance en una clase Kotlin" %}

Seguro que alguna vez te has topado desarrollando **una aplicación en los que los tiempos de compilación son largos** y necesitas probar una casuística en 
la que para ello necesitas tener la aplicación como si de un `first install` se tratara. 

Te quedan dos opciones, o bien desinstalas la aplicación y esperas que vuelva a compilar o te vas al almacenamiento del dispositivo y borras los datos manualmente.

**Con el `plugin` de ADB Idea tienes un abanico de posibilidades entre las que se encuentran:**

* Borrar todos los datos de la aplicación.
* Reiniciar la app con el debugger activado.
* Revocar los permisos.
* Desinstalar la aplicación.
* Detener el proceso.

Para usarlo solo debes pulsar **dos veces la tecla** `shift` y buscar la acción que quieres realizar **o desde la barra de herramientas de Android Studio**, en el
apartado Tools -> ADB Idea.

**Puedes encontrarlo en la store** de Android Studio como `ADB Idea` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/7380-adb-idea">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/adb_idea_store.webp" %}

##### Key Promoter X

{% include images.html url="/assets/images/post_images/android_studio_plugins/key_promoter_x.webp" description="Ejemplo de funcionamiento de Key Promoter X" %}

Es indiscutible que **si aprendes a usar los atajos de teclado que nos ofrece el IDE, serás más ágil programando,** pero a veces es imposible aprender todos y cada uno
de ellos. **Este plugin** lo que hace es **cada vez que ejecutamos una acción de Android Studio, si existe un atajo que haga lo mismo, te avisará.**

Puede ser que te pase como a mi y que aun asi no te acuerdes y sigas sin usar los atajos que te recomienda el `plugin`, pero para ello **también tiene un contador que
te da información acerca de cuantas veces has usado la función en vez del atajo de teclado,** hasta que a base de insistir, comiences a usar los atajos 🏞️

**Puedes encontrarlo en la store** de Android Studio como `Key Promoter X` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/9792-key-promoter-x">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/key_promoter_store.webp" %}

##### String Manipulation

{% include images.html url="/assets/images/post_images/android_studio_plugins/string_manipulation.webp" description="Ejemplo de uso de String Manipulation" %}

**El `plugin` amigo de los refactor.** Créeme, te vas a ahorrar horas de trabajo con este `plugin` que como su nombre indica **ofrece diversas opciones para darle formato
a los `strings` que uses en tu proyecto.**

Personalmente la que mas uso es la de cambiar el formato de un `string` en los diferentes recomendados que existen a la hora de programar, pero **el listado de
posibilidades que nos ofrece es bastante amplío:**

* Convertir textos o nombre de métodos a camel case o snake case.
* Codificar y decodificar con diversos sistemas.
* Invertir cadenas de texto y cambiarles el orden.

{% include images.html url="/assets/images/post_images/android_studio_plugins/string_manipulation.gif" description="Ejemplo de uso de String Manipulation" %}

**Puedes encontrarlo en la store** de Android Studio como `String Manipulation` o pulsando sobre el <a href="https://plugins.jetbrains.com/plugin/2162-string-manipulation">enlace resaltado</a>

{% include images.html url="/assets/images/post_images/android_studio_plugins/string_manipulation_store.webp" %}