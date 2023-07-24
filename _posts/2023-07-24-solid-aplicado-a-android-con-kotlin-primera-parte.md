---
layout: post
title: "SOLID aplicado a Android con Kotlin: Primera parte"
author: isaac
categories: [Android, Kotlin, Solid]
image: assets/images/post_images/solid_principles/solid_principles_first_part.webp
---

Los principios SOLID **fueron introducidos por el famoso informático Robert J. Martin (también 
conocido como Uncle Bob)** en su artículo *“Design Principles and Design Patterns”* publicado 
en el año 2000. Aunque el acrónimo SOLID fue introducido más tarde por Michael Feathers.

Estos cinco principios de desarrollo de software **son las directrices que hay que seguir a la
hora de construir software para que sea más fácil de escalar y mantener.** En consecuencia, a 
medida que tus aplicaciones crecen en tamaño, podrás reducir su complejidad y ahorrarte muchos 
dolores de cabeza en el futuro.

### Principio de responsabilidad única (**Single responsibility principle)**

El principio de responsabilidad única (en inglés: ***single responsibility principle),*** o como 
yo lo llamo: “no seas un ansias queriendo hacer tantas cosas a la vez” establece que **cada clase 
o archivo debería tener una única responsabilidad**. 

Esto significa que si hay una necesidad de cambiar la clase por más de una razón, entonces se rompe 
el principio.

> Cuando se dice **"más de una razón", se refiere a que la clase está siendo modificada para cumplir 
> con múltiples objetivos o responsabilidades.**

**Rompiendo el principio**

Por ejemplo, imagina que tengo **una clase que contiene información del usuario y también se encarga 
del proceso de inicio y cierre de sesión. Aquí ya se estaría rompiendo este principio.** 

```kotlin
data class User(
    var id: Long,
    var name: String,
    var password: String
){

    fun signIn(){
        println("Este método contiene lógica de inicio de sesión")
    }

    fun signOut(){
        println("Este método contiene lógica de cierre de sesión")
    }
}
```

Ahora **supón que tienes que hacer algunos cambios para el proceso de autenticación** en los métodos 
de inicio y cierre de sesión, la clase de usuario se verá afectada, **con la posibilidad de que se 
produzcan bugs en código que ya funcionaba anteriormente.**

**Solución**

Cuando pasa esto hay que separar las clases. Esto significa que la clase de usuario sólo debe tener 
una responsabilidad, es decir, contener la información del usuario.

Para solucionar esto, **hay que crear una nueva clase para manejar el proceso de inicio y cierre de 
sesión y dejar que la clase de usuario solo maneje la información del usuario.** Así, se logra tener 
una clase con una sola tarea y se evita romper el principio 
de responsabilidad única.

```kotlin
data class User(
    var id: Long,
    var name: String,
    var password: String
)

class AuthenticationService(){

    fun signIn(){
        println("Este método contiene lógica de inicio de sesión")
    }

    fun signOut(){
        println("Este método contiene lógica de cierre de sesión")
    }
}
```

### Principio de abierto/cerrado **(Open/closed principle)**

El principio abierto/cerrado (en inglés: ***Open/closed principle***) o "aporta o aparta" dice que 
un artefacto de software debe estar abierto a ampliaciones pero cerrado a modificaciones.

> Un artefacto de software **es cualquier elemento o componente de software que es creado durante el 
> proceso de desarrollo de software, como por ejemplo una clase, una función, un módulo, una librería, 
> un paquete, entre otros.** En otras palabras, es cualquier cosa que pueda ser creada o generada como 
> parte del desarrollo de software.

En otras palabras, **el comportamiento de un artefacto de software debería poder ampliarse sin tener 
que modificar dicho artefacto.**

**Rompiendo el principio**

Voy a tomar de ejemplo de un taller de reparación de coches que tiene diferentes tipos de tareas:

```kotlin
enum class CarRepairTasks {
    REPAIR, CLEAN
}
```

```kotlin
class CarRepairService {

    fun workingOnTheCar(tasks: CarRepairTasks) { 
        when (tasks) {
            CarWorkshopTasks.REPAIR -> {
                // Repara el coche
            }

            CarWorkshopTasks.CLEAN -> {
                // Limpia el coche
            }
        }
    }
}
```

Imagina que **recibo un nuevo requerimiento y ahora el taller también infla ruedas**, lo que significa que
**tengo que actualizar el enum** `CarRepairTasks` **y el** `CarRepairService` para soportar esta nueva tarea. 
Entonces, `CarRepairTasks` y `CarRepairService` serán así

```kotlin
enum class CarRepairTasks {
    REPAIR, CLEAN, INFLATE_WHEELS
}
```

```kotlin
class CarRepairService {

    fun workingOnTheCar(tasks: CarRepairTasks) {
	    when (tasks) {
            CarWorkshopTasks.REPAIR -> {
                // repara el coche
            }

            CarWorkshopTasks.CLEAN -> {
                // limpia el coche
            }

            CarWorkshopTasks.INFLATING_WHEELS -> {
                // infla las ruedas
            }
        }
    }
}
```

Esto significa que **cada vez que añadas una nueva tarea al `enum` del taller, hay que actualizar el** 
`CarRepairService` **para que admita el cambio**. Con esto se está rompiendo el principio `open/closed`.

**Solución**

En primer lugar, **voy a crear una interfaz** llamada `CarRepairTask`.

```kotlin
interface CarRepairTask {
    fun workingOnTheCar()
}
```

**Creo las implementaciones** de `CarRepairTask` para cada tipo: `RepairTask` y `CleanTask`.

```kotlin
class RepairTask : CarRepairTask {
    
    override fun workingOnTheCar() {
        // repara el coche
    }
}
```

```kotlin
class CleanTask : CarRepairTask {
    
    override fun workingOnTheCar() {
        // limpia el coche
    }
}
```

**Creamos** `CarRepairService`.

```kotlin
class CarRepairService {

    fun workingOnTheCar(task: CarRepairTask) {
        task.workingOnTheCar()
    }
}
```

**Ahora**, tu `CarRepairService` **sigue el principio open/closed ya que puedes agregar o eliminar diferentes 
tipos de tareas sin modificar** `CarRepairService`, ya que lo que recibe es una instancia de la interfaz. 

Voy a crear otra clase llamada `InflatingWheelsTask` que implementa `CarRepairTask`.

```kotlin
class InflatingWheelsTask : CarRepairTask {
    
    override fun workingOnTheCar() {
        // infla las ruedas
    }
}
```

Como puedes ver, aunque he agregado `InflatingWheelsTask` no he tenido que modificar `CarRepairService`, 
siguiendo así el principio de open/closed.