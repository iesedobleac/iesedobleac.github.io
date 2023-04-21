---
layout: post
title: "Aprende a tener mucho más flow en Kotlin"
author: isaac
categories: [Android, Kotlin, Flow, HotFlow, ColdFlow]
image: assets/images/post_images/flow_with_kotlin/flow_with_kotlin.webp
---

Aunque por el título pueda parecer que te voy a enseñar a componer canciones a ritmo de reguetón, nada 
más lejos de la realidad, **hoy vamos a hablar de Flows, o flujos en Kotlin** 🏖️

#### ¿Qué son los flow en Kotlin?

Flow **es un flujo de datos asíncrono**, que generalmente proviene de una tarea, que emite valores a 
un colector y se completa con o sin una excepción.

**Supón que tienes una tarea:** Descargar una imagen, emitir los items (valores) que son el porcentaje 
de descarga de la imagen. **Puede completarse con o sin excepción**. Si todo va bien, la tarea se 
completará sin excepción. Pero, **en caso de fallo de la red, la tarea se completará con una excepción** 🔥

Como resumen, se podría decir que **los flows se dividen en tres partes:** el `flow builder`, que es el 
que comunica o emite los datos. Un `operator`, que es el que **transforma esos datos** y por último un 
`collector`, que sería el que **escucha los datos que han sido emitidos** 👂🏽

<table>
  <tr class="draw-color-column">
    <td class="header-td">Flow builder</td>
    <td>-></td>
    <td>Speaker</td>
  </tr>
  <tr class="draw-color-column">
    <td class="header-td">Operator</td>
    <td>-></td>
    <td>Translator</td>
  </tr>
  <tr class="draw-color-column">
    <td class="header-td">Collector</td>
    <td>-></td>
    <td>Listener</td>
  </tr>
</table>

<h4 id="basic-example-flows">Ejemplo básico de uso de Flows</h4>

```kotlin
private fun main() {
    runBlocking {

        /**
         * Flow Builder o speaker
         */

        flow {
            (0..10).forEach {
                emit(it)
            }
            
            /**
             * Operator o translator
             */

        }.map {
            it * it

            /**
             * Collector o listener
             */

        }.collect {
            println(it.toString())
        }
    }
}
```
- En primer lugar, he creado un `flow builder` con una función `forEach` que está recorriendo un bucle
  del 0 a 10 y **está emitiendo todos los valores.**

- A continuación, hay un `map` que es el operador intermedio, dentro de este map estoy **haciendo una 
  operación de multiplicación del valor actual por si mismo.**

- Finalmente, con un `collect` **obtengo los valores emitidos y los imprimo** en pantalla haciendo uso 
  de la función `println()`.

Cuando conecto el `flow builder` usando el método `collect`, **sólo entonces, empezará a ejecutar el 
bloque flow y a emitir valores** 🔊

### Tipos de flow builders

Hay 4 tipos de `flow builders:`

**flowOf()**

```kotlin
/**
 * Se utiliza para crear flows a partir de un conjunto dado de elementos.
 */

flowOf(4, 2, 5, 1, 7)
.collect {
    println(it.toString())
}
```

**asFlow()**

```kotlin
/**
 * Es una función de extensión que ayuda a convertir tipos en flows.
 */

(1..5).asFlow()
.collect {
    println(it.toString())
}
```

**flow()**

```kotlin
/**
 * Esto es lo que hemos utilizado en el ejemplo de más arriba.
 */

flow {
    (0..10).forEach {
        emit(it)
    }
}
.collect {
    println(it.toString())
}
```

**channelFlow()**

```kotlin
/**
 * Este constructor crea flows con los elementos utilizando envíos 
 * proporcionados por el propio constructor.
 */

channelFlow {
    (0..10).forEach {
        send(it)
    }
}
.collect {
    println(it.toString())
}
```

### ¿Qué son los operadores terminales?

Los operadores terminales son los que realmente inician el `flow` conectando el `flow builder` y los 
operadores con el `collect`. **No confundir los operadores con los operadores terminales.** 

**Hasta que el flow no tiene un operador terminal, este no se va a iniciar,** aunque verás más 
adelante, que esto no siempre se cumple 👀

```kotlin
/**
 * Ejemplo de flow sin operador terminal
 */

flow {
    (0..10).forEach {
        emit(it)
    }

}.map {
    it * it
}
```

#### Algunos ejemplos: `collect` y `reduce`

{% include images.html url="/assets/images/post_images/flow_with_kotlin/speaker_like_emit.webp" %}

**Collect**

Este operador terminal **recoge los datos emitidos en el flow builder y cuando termina continua la 
ejecución** del resto de código 🖥️

**Tomando de ejemplo el código mostrado en el apartado <a href="#basic-example-flows">Ejemplo básico 
de uso de Flows</a>** le he hecho algunas modificaciones para el ejemplo:

```kotlin
private fun helloWorldWithFlows() {
    runBlocking {
        flow {
            (1..10).forEach {
                emit(it)
                delay(1000)
            }

        }.map {
            it * it

        }.collect {
            println("Valor actual: $it")
        }

        println("He terminado el collect")
    }
}
```

**He colocado un delay de 1 segundo para que la salida por consola sea más lenta** y ver que 
está haciendo el código. Después del `collect` hemos colocado otro `println()` que **será 
ejecutado cuando termine el operador terminal.** Lo que obtendrás por consola es:

```bash
Valor actual: 1
Valor actual: 4
Valor actual: 9
Valor actual: 16
Valor actual: 25
Valor actual: 36
Valor actual: 49
Valor actual: 64
Valor actual: 81
Valor actual: 100

He terminado el collect
```

¿Por qué esta explicación? **Collect tiene otras implementaciones dependiendo del tipo 
de flow que uses.** Esta es una de ellas y en otras entradas del blog verás más 💡

**Reduce**

```kotlin
flow {
    (0..10).forEach {
        emit(it)
    }

}.reduce { accumulator, value ->
    accumulator + value
}
```

`Reduce` es otro operador terminal, aunque para que funcione **lleva en su código fuente 
un `collect` que es el que realmente se encarga de comenzar a recolectar** los datos 
emitidos en el `flow builder`

```kotlin
public suspend fun <S, T : S> Flow<T>.reduce(operation: suspend (accumulator: S, value: T) -> S): S {
    var accumulator: Any? = NULL

    collect { value ->
        accumulator = if (accumulator !== NULL) {
            @Suppress("UNCHECKED_CAST")
            operation(accumulator as S, value)
        } else {
            value
        }
    }

    if (accumulator === NULL) throw NoSuchElementException("Empty flow can't be reduced")
    @Suppress("UNCHECKED_CAST")
    return accumulator as S
}
```

De aquí se puede sacar varias cosas: 

- Lo primero es que **se trata, al igual que `collect`, de una función `suspend`**, ya que 
  lo necesita para que `collect` funcione. 

- Lo segundo es que **se trata de una función de extensión de Flow**, por eso se puede usar
  en el `flow builder`. 

- Por último, que **dentro existe un `collect`, que es el encargado de iniciar el flow.**

**Entonces, ¿qué hace `reduce`?** En su comportamiento más básico: **sumar el valor actual
al valor acumulado**, por lo tanto en consola se verá algo como esto:

```bash
Valor acumulado: 0
Valor a sumar:  1
Resultado: 1
Vuelta número 1 

Valor acumulado: 1
Valor a sumar:  2
Resultado: 3
Vuelta número 2 

Valor acumulado: 3
Valor a sumar:  3
Resultado: 6
Vuelta número 3 

Valor acumulado: 6
Valor a sumar:  4
Resultado: 10
Vuelta número 4 

Valor acumulado: 10
Valor a sumar:  5
Resultado: 15
Vuelta número 5

....
```

Esto solo han sido unas pinceladas sobre flows en Kotlin, en una siguiente entrada relacionada
con este tema veremos los **cold flow, hot flow y sus diferencias** 🚀