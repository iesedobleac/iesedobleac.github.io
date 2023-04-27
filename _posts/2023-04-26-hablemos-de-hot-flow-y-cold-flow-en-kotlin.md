---
layout: post
title: "Hablemos de Hot Flow y Cold Flow en Kotlin"
author: isaac
categories: [Android, Kotlin, Flow, HotFlow, ColdFlow]
image: assets/images/post_images/hot_flow_cold_flow/hot_flow_cold_flow.webp
---

Si recuerdas, **en <a href="http://queridoprogramador.com/aprende-a-tener-mas-flow-en-kotlin/">el artículo 
anterior</a> te introducía** a los flows de Kotlin. **En este nuevo articulo quiero profundizar un poco más** 
hablándote de la diferencia entre los Hot Flow y los Cold Flow.

#### ¿En qué se diferencia uno de otro?

A grandes rasgos, te dejo una tabla comparativa donde te voy a indicar cuales son **las principales diferencias
antes de entrar en detalle:**

<table>
  <tr class="draw-color-row">
    <th>Cold Flow</th>
    <th>Hot Flow</th>
  </tr>
  <tr class="draw-color-row">
    <td>Sólo emite datos cuando hay un collect</td>
    <td>Emite datos aunque no haya collect</td>
  </tr>
  <tr class="draw-color-row">
    <td>No almacena datos</td>
    <td>Puede almacenar datos</td>
  </tr>
  <tr class="draw-color-row">
    <td>No puede tener múltiples collect</td>
    <td>Admite múltiples collect</td>
  </tr>
</table>

## El flujo frío o cold flow

Anteriormente ya te he enseñado el funcionamiento de un `cold flow`, aunque no te lo nombré específicamente.
Se trata del flow builder `flow()` y estas son alguna de sus características:

* El bloque de código dentro de `flow()` **no está activo hasta que se realiza una llamada de un operador 
  de terminal** (por ejemplo, `collect`). 

* **Sólo puede tener un suscriptor.** Cualquier nuevo abonado crearía una nueva ejecución de `flow()`.

* Debido a que el `flow()` no realiza nada, **regresa inmediatamente (no se suspende) y se inicia sólo cuando 
se le indica que lo haga,** las funciones que devuelven el flujo no tienen la palabra clave `suspend`.

Otra cosa que debemos tener en cuenta es que  `flow()` **no sobrevive a cambios de estado,** como la rotación 
de la pantalla, **ya que no almacena ningún valor,** solo se ejecuta y cuando termina muere.

```kotlin
public fun <T> flow(@BuilderInference block: suspend FlowCollector<T>.() -> Unit): Flow<T> = SafeFlow(block)
```

Observa que el bloque **es `suspend`, lo que significa que podemos utilizar `delay()` dentro** del bloque 
aunque la función `startFlow` no sea suspendible

```kotlin
fun startFlow() : Flow<Int> = flow{
    repeat(12) { 
        delay(1000)
        emit(i)
    }
}

viewModelScope.launch {  
    startFlow().collect { value -> 
        println(value)
    }
}
```

En resumen, **cold flow emite datos sólo cuando hay un collect**. La función `flow()` no puede contener datos. 
Imagina una tubería en la que fluye el agua, los datos en el `flow()` sólo fluyen, **no se almacenan**, no 
existe función `.value`.

## El flujo caliente o hot flow

Al contrario que el `cold flow`, este si **se encuentra activo incluso sin haber llamado a ningún operador de 
terminal**, es decir, aunque no estemos recolectando datos. Esto quiere decir que **vamos a poder emitir 
valores aunque no se haya llamado a un `collect`.**

Dentro de los `hot flow` podrás encontrar dos tipos: **los StateFlow y los SharedFlow**. Comparten similitudes
aunque cada uno se usa de una forma.

#### SharedFlow

**`SharedFlow`** es una clase que representa un flow que **puede tener múltiples emisores**, y que notifica a 
sus observadores cada vez que se emite un nuevo valor. Es decir, cuando un valor nuevo se emite a través de 
un **`SharedFlow`, todos los observadores registrados recibirán ese nuevo valor.**

Al contrario que StateFlow, al crear un SharedFlow no tenemos que darle un valor por defecto, ya que **SharedFlow 
no almacena datos, solo los emite.**

```kotlin
private fun shareFlowExample() {
    runBlocking {
        val sharedFlow = MutableSharedFlow<Int>()

        launch {
            delay(1000)
            sharedFlow.emit(1)
            delay(1000)
            sharedFlow.emit(2)
            delay(1000)
            sharedFlow.emit(3)
        }

        launch {
            sharedFlow.collect {
                println("Observer 1: $it")
            }
        }

        launch {
            sharedFlow.collect {
                println("Observer 2: $it")
            }
        }

        launch {
            sharedFlow.collect {
                println("Observer 3: $it")
            }
        }
    }
}
```

**Para crear el `SharedFlow` se hace uso de la clase `MutableSharedFlow`.** La forma en la que se emiten los datos 
es la misma que cuando usas el constructor `flow` en un `cold flow`, ya que hace uso de la función `emit`.

**SharedFlow puede tener más de un collect al mismo tiempo**, y cuando se emita un valor nuevo, todos los `collect` 
serán notificados.

De esta forma, tenemos tres `collect` para probar este caso y cuando ejecuto el código, la salida por consola 
es la siguiente:

```bash
Observer 1: 1
Observer 2: 1
Observer 3: 1
Observer 1: 2
Observer 2: 2
Observer 3: 2
Observer 1: 3
Observer 2: 3
Observer 3: 3
```

#### StateFlow

StateFlow **hace cosas similares a `LiveData` pero esta hecho usando flow** y la única diferencia comparado con 
`LiveData` es que **no conoce el ciclo de vida de la aplicación** pero esto ha sido resuelto usando la API de 
corrutinas y método como `repeatOnLifecycle`, asi que **lo que `LiveData` puede hacer `StateFlow` puede hacerlo 
mucho mejor con el poder del api de flow.**

Al almacenar valores, con `StateFlow`, **se pueden usar múltiples collect, ya que siempre tiene almacenado el 
último valor que ha emitido,** aunque esto tiene algo de trampa:

```kotlin
val stateFlow = MutableStateFlow(0)

private fun stateFlowWithContinueCollect() {
    runBlocking {

        launch {
            repeat(10) {
                delay(1000)
                stateFlow.value = stateFlow.value + 1
            }
        }

        stateFlow.collect { value ->
            println("El valor del estado es: $value")

            if (value == 7) {
                continueInSameFlow()
            }
        }
    }
}

private suspend fun continueInSameFlow() {
    stateFlow.collect {
        println("Ahora el valor es: $it")
    }
}
```

Para este ejemplo he creado un `StateFlow` el cual he iniciado en 0. **Lanzo una corrutina con una repetición de 
10 veces con un segundo de espera** entre cada una, al cual le estaremos actualizando el valor del `StateFlow` 
cada vez. 

**Para actualizar el estado de un StateFlow usamos la función `value` al igual que se haría en un LiveData.**

> **Aunque no haya un collect observando, el StateFlow ya está emitiendo datos**, por lo tanto, imagina que 
> no iniciamos el `collect` hasta 5 segundos después. En este caso nuestro `StateFlow` ya tendría un valor de 6, 
> por lo que el primer valor que vamos a recolectar es 6, **por lo que aquí se ve claramente que un hot flow, 
> emite datos aunque no exista collect.**

Seguidamente **he iniciado el primer collect, el cual comienza a recolectar los valores que están siendo emitidos** 
cuando se llama al set del value dentro de la corrutina.

Una vez que `StateFlow` toma como valor el 7, pasará a un segundo `collect`. Como **`collect` es una función 
de suspensión infinita** y lo estoy ejecutando en la misma corrutina, nunca volverá a retornar valores el 
primer collect, ya que la ejecución ha quedado “atrapada” en el segundo collect. **Como el valor en un StateFlow 
es almacenado, verás que comienza directamente por el número 7 hasta finalizar.**

##### StateFlow es una especialización de SharedFlow

Puedes convertir un `SharedFlow` en un `StateFlow` porque este es una especialización del primero, para conseguir 
esto:

```kotlin
val sharedFlow = MutableSharedFlow<Int>(
    replay = 1,
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)
sharedFlow.emit(0) // initial value
val stateFlow = sharedFlow.distinctUntilChanged()
```

El parámetro `replay` **indica el tamaño que tendrá la caché de elementos más recientes** y por lo tanto indicará 
**el número de elementos que serán retransmitidos al suscriptor** en el momento mismo de la suscripción.

El valor por defecto de este parámetro es 0 provocando entonces que cada suscriptor reciba solamente los elementos
siguientes a su suscripción.

El parámetro `onBufferOverflow` **determina la estrategia a seguir cuando el buffer de nuestro flow se llena.** El
valor **por defecto es `SUSPEND`,** con esta estrategia el buffer **se irá llenando con los valores emitidos hasta que
alcance esté completamente lleno, momento en que la función emit se suspenderá** hasta que haya hueco de nuevo en
el buffer y pueda emitir el siguiente valor.

En este caso se usa **la estrategia `DROP_OLDEST`, que quiere lo que hace no es suspender la función emit cuando el
buffer está lleno, sino que elimina el elemento más antiguo del buffer y añade el nuevo en primer lugar.** Existen
otras estrategias para controlar un desbordamiento del buffer, **pero te lo contaré en otra entrada del blog.**

Si recuerdas un `StateFlow` **tiene que tener un valor inicial obligatorio, esto es lo que está haciendo el emit**
que le pasamos el valor 0, sería el valor inicial.

Por último, **un `StateFlow` no recolecta valores iguales que el anterior que se ha emitido,** es por esto que antes 
de hacer el `collect`, debemos especificar **este comportamiento con la función `distinctUntilChanged()`**

##### Función emit de StateFlow

Si entras dentro de la implementación de la clase de `StateFlow`, podrás observar que **también dispone de una 
función emit,** pero si entramos en su implementación veremos que no es más que **asignar el valor pasado por 
parámetro a `value`:**

```kotlin
override suspend fun emit(value: T) {
    this.value = value
}
```

##### El bucle infinito de collect

Collect es una función que tiene diferentes implementaciones dependiendo de la clase en la que se va a utilizar.
En el caso de StateFlow y SharedFlow, **`collect` es una función suspendible infinita**, porque siempre va a 
estar escuchando. Te muestro su código:

```kotlin
override suspend fun collect(collector: FlowCollector<T>): Nothing {
        val slot = allocateSlot()
        try {
            if (collector is SubscribedFlowCollector) collector.onSubscription()
            val collectorJob = currentCoroutineContext()[Job]
            var oldState: Any? = null // previously emitted T!! | NULL (null -- nothing emitted yet)
            // The loop is arranged so that it starts delivering current value without waiting first
            while (true) {
                // Here the coroutine could have waited for a while to be dispatched,
                // so we use the most recent state here to ensure the best possible conflation of stale values
                val newState = _state.value
                // always check for cancellation
                collectorJob?.ensureActive()
                // Conflate value emissions using equality
                if (oldState == null || oldState != newState) {
                    collector.emit(NULL.unbox(newState))
                    oldState = newState
                }
                // Note: if awaitPending is cancelled, then it bails out of this loop and calls freeSlot
                if (!slot.takePending()) { // try fast-path without suspending first
                    slot.awaitPending() // only suspend for new values when needed
                }
            }
        } finally {
            freeSlot(slot)
        }
    }
```

**La función tiene un bucle `while` infinito en su interior**, ya que **while nunca dejará de ser true.** Es por 
ello que **si tenemos dos collect anidados** dentro de la misma corrutina, **en cuanto inicie el segundo, el 
primero estará suspendido hasta el infinito.**

Por lo tanto, podemos fijarnos que **si tenemos un collect y seguidamente escribimos más código,** Android Studio 
te arrojará un warning que dice: *Unreachable code.* O lo que es lo mismo, **ese código nunca va a ser alcanzado.**