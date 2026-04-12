<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

La **herencia** es un mecanismo fundamental de la programación orientada a objetos que permite definir una nueva clase (subclase o clase hija) basándose en una clase existente (superclase o clase padre), estableciendo una relación **"es-un"** (is-a). Esta relación indica que la subclase es un caso particular o una especialización de la superclase: un `Artillero` es un `Soldado`, un `Zapador` es un `Soldado`. La herencia aporta dos implicaciones principales: por un lado, la **compatibilidad de tipos**, que permite que una variable de tipo superclase pueda referenciar objetos de cualquier subclase; por otro lado, la **herencia de estado y comportamiento**, que significa que la subclase automáticamente posee todos los atributos y métodos de la superclase (excepto los privados), pudiendo añadir nuevos o modificar los existentes.

```java
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }

    public String getNombre() {
        return nombre;
    }
}

public class Artillero extends Soldado {
    private int numeroCohetes;

    public Artillero(String nombre, int numeroCohetes) {
        super(nombre);  // Llama al constructor de Soldado
        this.numeroCohetes = numeroCohetes;
    }

    public void dispararCohete() {
        if (numeroCohetes > 0) {
            System.out.println(getNombre() + " dispara un cohete ¡Boom!");
            numeroCohetes--;
        } else {
            System.out.println(getNombre() + " no tiene cohetes");
        }
    }

    public int getNumeroCohetes() {
        return numeroCohetes;
    }
}

public class Zapador extends Soldado {
    private int numeroMinas;

    public Zapador(String nombre, int numeroMinas) {
        super(nombre);
        this.numeroMinas = numeroMinas;
    }

    public void ponerMina() {
        if (numeroMinas > 0) {
            System.out.println(getNombre() + " coloca una mina");
            numeroMinas--;
        } else {
            System.out.println(getNombre() + " no tiene minas");
        }
    }

    public int getNumeroMinas() {
        return numeroMinas;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        // Compatibilidad de tipos: un Artillero es un Soldado, un Zapador es un Soldado
        Soldado[] ejercito = new Soldado[3];
        ejercito[0] = new Artillero("Sargento Rock", 5);
        ejercito[1] = new Zapador("Cabo Mina", 3);
        ejercito[2] = new Artillero("Teniente Fuego", 2);

        // Polimorfismo: todos saludan, aunque sean de tipos distintos
        for (Soldado s : ejercito) {
            s.saludar();  // Cada uno ejecuta su propia versión del método saludar
        }
    }
}
```

La **compatibilidad de tipos** se manifiesta claramente en el array de tipo `Soldado[]`, que puede almacenar objetos tanto de `Soldado` como de `Artillero` y `Zapador`, porque estos últimos **son** subtipos de `Soldado`. Al recorrer el array, cada objeto responde al mensaje `saludar()` según su implementación concreta (aunque en este ejemplo todas usan la misma de `Soldado`). La **herencia de estado y comportamiento** se observa en que `Artillero` y `Zapador` no necesitan redefinir el atributo `nombre` ni el método `saludar()`; los heredan automáticamente de `Soldado`. Además, añaden su propio estado (`numeroCohetes`, `numeroMinas`) y comportamiento específico (`dispararCohete()`, `ponerMina()`), demostrando cómo la herencia permite la especialización. Es importante notar que el atributo `nombre` es `private` en `Soldado`, por lo que las subclases no pueden accederlo directamente, pero sí mediante el getter `getNombre()` heredado, respetando así la encapsulación.


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

Al crear un objeto de una subclase (como `Artillero` o `Zapador`), se ejecutan **todos los constructores de la cadena de herencia**, comenzando por el constructor de la clase base más alta (`Soldado`) y continuando hacia abajo hasta el constructor de la subclase concreta. En el ejemplo, al instanciar `new Artillero("Sargento Rock", 5)`, el orden de ejecución es: primero el constructor de `Soldado` (la superclase), luego el constructor de `Artillero` (la subclase). Esto garantiza que la parte heredada del objeto se inicialice completamente antes de que la subclase añada sus propios atributos y comportamientos específicos.

La palabra clave **`super`** dentro de un constructor se utiliza para **invocar explícitamente al constructor de la superclase**. Debe ser la primera instrucción del constructor de la subclase. En el ejemplo, `super(nombre);` llama al constructor de `Soldado` que recibe un `String`. Si la subclase no incluye una llamada explícita a `super`, el compilador inserta automáticamente una llamada al **constructor sin parámetros** de la superclase (`super()`). Sin embargo, si la superclase no tiene un constructor sin parámetros visible (ya sea porque no está definido o porque es privado), el compilador no puede insertar esa llamada implícita y se produce un error de compilación.

Por lo tanto, **sí, se debe llamar a `super` siempre** cuando la superclase no disponga de un constructor sin parámetros accesible. Incluso si la superclase tiene un constructor sin parámetros, la llamada a `super()` es opcional pero implícita. La regla práctica es: si la superclase define constructores con parámetros (y no define explícitamente un constructor sin parámetros), entonces **toda subclase debe llamar explícitamente a uno de esos constructores** usando `super(...)` con los argumentos apropiados como primera instrucción de su constructor. Esta obligación garantiza que la parte heredada del objeto se inicialice correctamente con los valores necesarios, manteniendo la integridad del objeto y respetando las invariantes definidas en la superclase.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta
