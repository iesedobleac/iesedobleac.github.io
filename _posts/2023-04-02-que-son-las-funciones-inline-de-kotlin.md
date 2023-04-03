---
layout: post
title: "¿Qué son las funciones inline de Kotlin?"
author: isaac
categories: [Android, Kotlin, Inline]
image: assets/images/post_images/inline_functions_kotlin/inline_functions_kotlin.webp
---

Estos días mientras **aprendía a hacer test unitarios y de integración** en mis proyectos, me he topado con un
concepto: las funciones `inline`.

Nunca me había parado a ver para que servían o que ventajas podía ofrecer, ya que con la documentación no deja 
claro **que puede aportar o cuando es recomendable usarlas.**

#### ¿Qué son las funciones inline?

Se trata de un tipo de función que ofrece Kotlin en las que **el cuerpo de esta se copia en el lugar donde 
se llama, en lugar de hacer una llamada a la función.**

Esto es especialmente útil para **aumentar el rendimiento y optimizar nuestro código** al utilizar _funciones de
alto nivel_ 🔝

##### Funciones de alto nivel

Las funciones de alto nivel **son las que reciben como parámetro otra función o la devuelve.** Pueden recibir una
o varias funciones como parámetros. También hay gente que las llama **funciones lambda,** por lo que puedes oírlas
de las dos formas.

```kotlin
/**
 * Este es un ejemplo de una función de alto nivel
 */

private fun nonInlineFunctionExample(success: () -> Unit, failure: () -> Unit) {
    success()
    failure()
    println("Estoy después de ejecutar la lambda")
}
```

#### Como se usan las funciones inline

Para que una función sea `inline`, **tienes que añadirle la palabra clave `inline` antes de declarar la función**
y después del parámetro de visibilidad. Después de usa como cualquier otra función de alto nivel.

```kotlin
/**
 * Este es un ejemplo de una función inline en Kotlin
 */

private inline fun inlineFunctionExample(success: () -> Unit, failure: () -> Unit) {
    success()
    failure()
    println("Estoy después de ejecutar la lambda")
}

/**
 * Este es un ejemplo de uso de una función inline en Kotlin
 */

private fun main() {
    inlineFunctionExample(success = {
        println("Estoy dentro de success")
    }, failure = {
        println("Estoy dentro de failure")
    })
}
```

#### ¿Por qué usar funciones inline?

**Algo que hace muy legible a Kotlin es el `syntax sugar` que añade**. Esto tiene un problema, ya que a veces 
no nos damos cuenta **como está funcionando nuestro código bajo el capó** 🧐

Cuando creas una **función de alto nivel**, la máquina virtual traduce esto **como una implementación anónima** 
de una interfaz llamada `Function`. **Creará tantas <a href="https://kotlinlang.org/docs/object-declarations.html#inheriting-anonymous-objects-from-supertypes">implementaciones anónimas</a> como funciones le pases** por 
parámetro a tu función que no es de tipo `inline` 🛼

```java
public static final void main(String[] var0) {
    inlineFunctionExample(new Function0() {
        @Override
        public Object invoke() {
            return null;
        }
    }, new Function0() {
        @Override
        public Object invoke() {
            return null;
        }
    });
}

private static void inlineFunctionExample(
        Function0 success,
        Function0 failure
) {
    success.invoke();
    failure.invoke();
    String var1 = "Estoy después de ejecutar la lambda";
    System.out.println(var1);
}
```

**Cuando solo tienes una o dos no vas a notar diferencia,** pero si tu proyecto esta lleno de este tipo de 
funciones y no estás haciendo uso de las funciones `inline`, **estarás haciendo un uso excesivo innecesario de 
la memoria.**

Sin embargo, cuando usas una función de tipo `inline`, el compilador lo que hace es colocar el cuerpo de esa 
función en el lugar donde ha sido llamada.

```kotlin
private inline fun inlineFunctionExample(
    numberOne: Int,
    numberTwo: Int,
    success: () -> Unit,
    failure: () -> Unit
) {
    if (numberOne > numberTwo) {
        success()

    } else {
        failure()
    }
    println("Estoy después de ejecutar la lambda")
}
```

Dentro de la función `inline` tienes dos funciones que son recibidas mediante parámetros. La condición hace que 
la función `inline` **sustituya el cuerpo de aquella a la que tenga acceso a través del condicional** 🤯

```kotlin

/**
 * Ejemplo de uso de una función inline con varios parámetros
 */

private fun main() {
    inlineFunctionExample(1, 4, success = {
        println("El primer número es mayor")
    }, failure = {
        println("El segundo número es mayor")
    })
}
```

```java

/**
 * Resultado al decompilar el bytecode de Kotlin
 */

public static final void main(String[] var0) {
    String var2 = "El segundo número es mayor";
    System.out.println(var2);
    var2 = "Estoy después de ejecutar la lambda";
    System.out.println(var2);
}
```

Como los números que le he pasado a la función son el 1 y el 4, dado el condicional, el primer número no es 
mayor que el segundo, por lo que el compilador **coge lo que haya dentro del `failure()` y lo añade al cuerpo 
de la función** junto con el resto de la función 💡

#### ¿Aporta alguna ventaja?

**La respuesta es un si rotundo,** las funciones `inline` son más rápidas de ejecutar ya que el compilador lo 
construye como si de un solo cuerpo se tratara y **no tiene que llamar a una implementación anónima.**

He creado una función que lo que hará será medir cuanto tarda en ejecutarse algo, así **te voy a mostrar los 
tiempos de ejecución al usar una función de orden superior `inline` o una no `inline`.**

{% include images.html url="https://media2.giphy.com/media/CWjEJRyu1HJYqJD9RI/giphy.gif?cid=ecf05e478jdegpt
6pfzptezgml1llie7lw650muufntl7k9e&rid=giphy.gif&ct=ghttps://giphy.com/embed/CWjEJRyu1HJYqJD9RI" %}


##### Función no inline

```kotlin
private fun main() {
    measureTime {
        inlineFunctionExample {
            println("El primer número es mayor")
        }
    }
}

private fun inlineFunctionExample(
    success: () -> Unit
) {
    success()
    println("Estoy después de ejecutar la lambda")
}
```

{% include images.html url="/assets/images/post_images/inline_functions_kotlin/test_without_inline.webp"
 description="Prueba con una función non inline" %}

Para tener una prueba fiable **he lanzado la función unas diez veces** y la media de tiempo de ejecución ha 
sido ido oscilando **entre los 670 y los 730 ms.**

##### Función inline

```kotlin
private fun main() {
    measureTime {
        inlineFunctionExample {
            println("El primer número es mayor")
        }
    }
}

private inline fun inlineFunctionExample(
    success: () -> Unit
) {
    success()
    println("Estoy después de ejecutar la lambda")
}
```

{% include images.html url="/assets/images/post_images/inline_functions_kotlin/test_with_inline.webp" 
description="Prueba con una función inline" %}

En este caso he probado las mismas veces que el caso anterior y **la media en esta ocasión ha sido entre 
los 110 y los 150 ms.**

Como se puede observar, **una función de tipo `inline` es bastante más rápida que una no `inline`.** En este 
caso la diferencia ha sido en microsegundos y puede que apenas se note, pero esto solo es una función. 
**Un proyecto puede tener cientos de estas funciones y marcar la diferencia** para que un usuario se tire 
de los pelos esperando a que cargue 🔥

##### Otras ventajas

El motivo por el que me puse a indagar sobre las funciones `inline` fue al toparme con algo muy extraño: 
**tenía una función de orden superior, la cual tenía una función pasada por parámetros,** hasta aquí 
todo normal, era una función para capturar errores en las llamadas a una API y personalizarlos.

¿Lo extraño? **Cuando usaba esta función, me permitía usar funciones de suspensión dentro del cuerpo de 
la lambda** sin necesidad de hacerla también suspendible 👀

```kotlin
inline fun <T> wrapRemoteErrors(block: () -> T): T {
    return try {
        block()
    } catch (e: Throwable) {
        throw Throwable()
    }
}
````

Partiendo de esta función con tipos genéricos que lo único que hace es **ejecutar la función `block()` 
dentro del `try/catch` y si lo que se ejecute da algún tipo de excepción, capturarlo** en el `catch`, así 
nos ahorramos andar escribiendo esta comprobación en todos sitios.

```kotlin
suspend fun simulatedNetworkCall(): String {
    return "Llamada suspendible de prueba"
}

suspend fun getSomethingFromNetwork(): String {
    return wrapRemoteErrors { 
        simulatedNetworkCall()
    }
}
````

Me he creado una función que simula la llamada a una API. Para la prueba he hecho que una función de 
tipo `suspend` devuelva un string.

En el método `getSomethingFromNetwork()` es donde está la magia. **Si no se tratara de una función `inline` 
recibirías el siguiente error: `Suspension functions can be called only within coroutine body`,** pero 
¿qué está ocurriendo aquí?

Recordemos que una función `inline` lo que hace es copiar el cuerpo de la función dentro del lugar desde 
donde la hemos llamado, en este caso quedaría algo similar a esto:

```kotlin
suspend fun getSomethingFromNetwork(): String {
    return try {
        simulatedNetworkCall()
    } catch (e: Throwable) {
        throw Throwable()
    }
}
````

Lo que está pasando es que **Android Studio ha sido capaz de detectar, incluso antes de compilar,** el 
funcionamiento de la función `inline`, por lo que al copiar el cuerpo dentro de `getSomethingFromNetwork()`, 
detecta que se trata de una función suspendible y se lo traga. **Si nuestra función no fuera `inline`, 
tendríamos que declarar como suspend la que le pasamos por parámetros** 🤨

```kotlin
suspend fun <T> wrapRemoteErrors(block: suspend () -> T): T {
    return try {
        block()
    } catch (e: Throwable) {
        throw Throwable()
    }
}
````

De esta manera **nos obligaría a tener que usar la función siempre dentro de otras que también fuera 
suspendibles** y por supuesto, **perdiendo algo de rendimiento** al no aprovechar las bondades que el 
`inline` nos da.
