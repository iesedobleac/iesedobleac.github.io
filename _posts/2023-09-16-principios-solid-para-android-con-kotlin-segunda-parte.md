---
layout: post
title: "Principios SOLID para Android con Kotlin: Segunda parte"
author: isaac
categories: [Android, Kotlin, Solid]
image: assets/images/post_images/solid_principles/solid_principles_second_part.webp
---

Continuamos con los principios SOLID en esta serie de entradas en los que, de la forma más 
sencilla posible, intento explicarte en que consiste cada uno de ellos y porque te puede 
ser de mucha utilidad en tu día a día. 

En la <a href="https://queridoprogramador.com/principios-solid-para-kotlin-primera-parte/">
primera parte</a> veíamos los dos primeros principios SOLID. **En este apartado vamos a 
adentrarnos en los dos siguientes:**

### Principio de sustitución de Liskov **(*Liskov substitution principle*)**

Este principio debe su nombre a Barbara Liskov, reconocida matemática y científica de la 
computación. Su definición más pura nos dice que:

>Si S es un subtipo de T, entonces **los objetos de tipo T en una aplicación pueden ser 
>sustituidos por objetos de tipo S** (es decir, los objetos de tipo S pueden sustituir 
>objetos de tipo T), **sin alterar ninguna de las propiedades deseables** de esa aplicación 
>(la corrección, la tarea que realiza, etc.)*

En palabras sencillas quiere decir que **si tenemos una clase padre, las clases hijas deben 
poder ser sustituidas entre ellas, heredando las funciones de la clase padre, sin alterar su 
lógica.** 

Si tienes una clase y creas otra a partir de ella, ésta de la que heredas se convierte en 
clase padre y la nueva en clase hija. **La clase hija debe ser capaz de hacer todo lo que 
la clase padre puede hacer, además de sus propias funcionalidades.** Esto es lo que conocemos 
como herencia, ya que la clase hija hereda el comportamiento de la clase padre.

**Rompiendo el principio**

Supongamos que **tenemos una clase llamada `Duck` que define a todos los patos**

```jsx
open class Duck{

    open fun quack() {
        println("El animal está haciendo cuac")
    }

    open fun swim() {
        println("El animal está nadando")
    }

    open fun walk() {
        println("El animal está andando")
    }
}
```

Vamos a crear algunas clases que extiendan de `Duck`

```jsx
class NormalDuck : Duck()
```

Hasta aquí todo bien, **hemos creado un `NormalDuck` que es un `Duck` y** hereda las clases del 
padre, **esto es correcto ya que un pato puede hacer cuac, nadar y andar.**

Pero… **¿qué ocurre si creamos un pato de metal?** Vamos a crear una clase `MetalDuck` que 
también extiende de `Duck`, pero aquí falla algo: **un pato de metal no puede nadar.**

```jsx
class MetalDuck : Duck() {

    override fun swim() {
        TODO("Not yet implemented")
    }
}
```

Es probable que te hayas encontrado **en más de una ocasión que al heredar una clase, hay funciones 
que no tienen sentido**, como en este caso, y tenemos dos opciones:

- **Sobreescribes el método dejándolo en blanco** para que no haga ningún comportamiento que no 
- queramos conseguir.

- **Sobreescribes el método y lanzas una excepción**, con el inconveniente de ocasionar posibles 
  crashes en nuestro código y de ensuciarlo con artefactos que no necesitamos.

Llegado a este punto, **nos encontramos con una violación clara del `LSP`**, ya que no podemos hacer 
que una clase que hereda de la clase padre, realice las mismas acciones que esta. 

**Solución**

**¿Cómo puedes solucionar esto?** En Android usando Kotlin como lenguaje he encontrado dos formas 
de hacerlo, y hasta donde se, no hay una que sea mejor que otra, asi que te daré las dos formas con 
sus ventajas e inconvenientes:

**Usando clases abiertas a extensión**

Primero **hay que encontrar para este caso aquello que hagan todos los patos, ya que será la clase 
padre**, en este caso sabes que todos los patos hacen cuac y andan, al menos los no mutantes.

```jsx
open class Duck {

    open fun quack() {
        println("The duck says quack")
    }

    open fun walk() {
        println("The duck is walking")
    }
}
```

Ya tenemos nuestra clase padre, **ahora tenemos que hacer aquellas clases donde queramos hacer una 
lógica concreta,** para nuestro caso, como no todos los patos pueden nadar, nos creamos una clase 
llamada `DuckThatCanSwim` que extienda de la clase padre.

```jsx
open class DuckThatCanSwim : Duck() {

    open fun swim() {
        println("The duck is swimming")
    }
}
```

**Ahora crearé las clases hijas que son las que van a heredar de estas clases que he creado 
especializadas**, sin necesidad de sobrescribir métodos vacíos o lanzar excepciones.

```jsx
class NormalDuck : DuckThatCanSwim()

class MetalDuck : Duck()
```

Ahora puedes comprobar que si creas una instancia de la clase padre y la sustituyes por las hijas, 
podrá seguir utilizando las mismas funcionalidades:

```jsx
val duck = Duck()

duck.quack()
duck.walk()

val duck = NormalDuck()

duck.quack()
duck.walk()

val duck = MetalDuck()

duck.quack()
duck.walk()
```

**Usando interfaces**

**Otra forma de cumplir el principio de sustitución de Liskov es usando interfaces**, declarando 
la interfaz que actuará de padre y aquellas interfaces que queremos “especializar”, como en el caso 
de `DuckThatCanSwim`

```jsx
interface Duck {
    fun quack()
    fun walk()
}

interface DuckThatCanSwim : Duck {
    fun swim()
}
```

Una vez las tenemos, **crearemos nuestras clases** `NormalDuck` y `MetalDuck` que implementarán 
dichas interfaces

```jsx
class NormalDuck : DuckThatCanSwim {

    override fun swim() {
        println("The duck is swimming")
    }

    override fun quack() {
        println("The duck says quack")
    }

    override fun walk() {
        println("The duck is walking")
    }
}

class MetalDuck : Duck {

    override fun quack() {
        println("The duck says quack")
    }

    override fun walk() {
        println("The duck is walking")
    }
}
```

**Aunque no puedas crear una instancia de una interfaz, sí se puede definir.** Por ejemplo: 
definir una variable con el tipo `Duck` y luego asignarle `NormalDuck` o `MetalDuck` y en ese 
caso tanto `NormalDuck` como `MetalDuck` satisfarían el comportamiento de la interfaz `Duck`.

```jsx
val duck: Duck = if (true) MetalDuck() else NormalDuck()

duck.quack()
duck.walk()
```

Para mi gusto personal, **usaría las interfaces cuando la lógica comience a ser más compleja**, 
ya que **podemos heredar a una única clase pero podemos implementar tantas interfaces como 
queramos,** lo que nos ofrece una mayor versatilidad.

### Principio de segregación de la interfaz **(*Interface segregation principle*)**

El principio de segregación de interfaces establece que los desarrolladores no deben verse 
obligados a depender de interfaces que no utilizan.

En otras palabras, la clase que implementa la interfaz no debería verse obligada a utilizar 
los métodos que no necesita.

**Rompiendo el principio**

Supongamos que **tenemos una interfaz llamada `Animal`,** los animales pueden respirar, 
nadar y volar por lo que creamos sus respectivas funciones.

```jsx
interface Animal {
    fun breath()
    fun fly()
}
```

Vamos a crear algunas clases que extiendan de `Animal`

```jsx
class Bird : Animal() {

    override fun breath() {
        println("El animal está caminando")
    

    override fun fly() {
        println("El animal está volando")
    }
}
```

Hasta aquí todo bien, **hemos creado un `Bird` que es un `Animal` y hereda las clases del 
padre** dándoles un comportamiento

Pero… **¿qué ocurre con animales que no tienen la capacidad de volar o nadar?** Vamos a 
crear una clase `Dog` que también extiende de `Animal`, pero aquí falla algo: **un perro 
no puede volar.**

```jsx
class Dog : Animal() {

    override fun breath() {
        println("El animal está caminando")
    }

    override fun fly() {
        TODO("Not yet implemented")
    }
}
```

**Solución**

Para solucionar esto voy a crear interfaces más pequeñas con funciones específicas. Por 
un lado he sacado de la interfaz `Animal` el método `fly()` y lo he movido a una nueva 
interfaz llamada `AnimalsThatCanFly`

```jsx
interface Animal {
    fun breath()
}

interface AnimalsThatCanFly {
    fun fly()
}
```

Ahora gracias a esto, puedes hacer que tus clases hereden de las interfaces que te hagan 
falta y evitamos tener que dejar funciones vacías o que lancen excepciones.

```jsx
class Bird : AnimalsThatCanFly, Animal {

    override fun breath() {
        println("El animal está respirando")
    }

    override fun fly() {
        println("El animal está volando")
    }
}

class Dog : Animal {

    override fun breath() {
        println("El animal está respirando")
    }
}
```