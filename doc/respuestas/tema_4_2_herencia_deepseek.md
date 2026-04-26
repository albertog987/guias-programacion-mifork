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

Sí, los **atributos privados de la superclase forman parte de la instancia de la subclase en memoria**. Cuando se crea un objeto de `Artillero`, la memoria asignada contiene espacio para todos los atributos definidos en `Artillero` (como `numeroCohetes`) y también para los atributos heredados de `Soldado` (como el atributo privado `nombre`). La herencia no duplica la definición, sino que la subclase incorpora la estructura completa de la superclase, aunque no pueda acceder directamente a los miembros privados. En el momento de la instanciación, el constructor de `Soldado` es el responsable de inicializar esa parte de la memoria.

Sin embargo, **que los atributos privados existan en memoria no implica que la subclase pueda acceder a ellos directamente**. El modificador `private` es precisamente el mecanismo que impide el acceso desde cualquier clase distinta a aquella donde se declararon. En el ejemplo, el método `saludar()` de `Soldado` puede acceder a `nombre` directamente, pero `Artillero` no puede hacer `this.nombre` ni `super.nombre` porque es privado. La subclase solo puede acceder a los atributos privados de la superclase a través de **métodos públicos o protegidos heredados**, como el getter `getNombre()`.

```java
public class Artillero extends Soldado {
    private int numeroCohetes;

    public Artillero(String nombre, int numeroCohetes) {
        super(nombre);
        this.numeroCohetes = numeroCohetes;
    }

    public void mostrarInfo() {
        // Esto NO compila: 'nombre' es private en Soldado
        // System.out.println("Nombre: " + nombre);
        
        // Esto SÍ funciona: usa el getter heredado
        System.out.println("Nombre: " + getNombre());
        System.out.println("Cohetes: " + numeroCohetes);
    }
}
```

La existencia de los atributos privados en memoria es la razón por la que se dice que la herencia establece una relación **"es-un"** fuerte: la subclase es completamente la superclase, con todo su estado, pero la encapsulación impide la manipulación directa de ese estado. Este diseño protege la integridad de la superclase, ya que esta conserva el control exclusivo sobre sus propios atributos. Si se necesita que las subclases accedan a ciertos atributos, la superclase debería declararlos como `protected` (accesible desde subclases) o proporcionar métodos `protected` o `public` para su manipulación controlada, en lugar de hacerlos `private`. La decisión de usar `private` versus `protected` refleja el grado de confianza que la superclase deposita en sus subclases y el nivel de encapsulación deseado.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

La **compatibilidad de tipos** que proporciona la herencia tiene una implicación fundamental en la **extensibilidad** del código: permite añadir nuevos subtipos (nuevas clases hijas) **sin modificar el código que depende exclusivamente de la superclase**. Esto significa que los programas escritos en términos de la clase base (como los bucles que procesan arrays de `Soldado`) son **abiertos a la extensión pero cerrados a la modificación**, un principio conocido como OCP (Open/Closed Principle). El código existente no necesita saber nada sobre las nuevas subclases para tratarlas correctamente, porque cualquier objeto de la nueva subclase es también un objeto de la superclase y puede ser utilizado en su lugar.

Para ilustrarlo, añádase un nuevo tipo de `Soldado`, el `Francotirador`, sin modificar el bucle de saludos ya existente. El código original que recorría el array de `Soldado` llamando a `saludar()` sigue funcionando perfectamente con el nuevo tipo, sin necesidad de cambios.

```java
// Nueva clase añadida sin modificar el código existente
public class Francotirador extends Soldado {
    private int precision;  // Nuevo atributo específico

    public Francotirador(String nombre, int precision) {
        super(nombre);
        this.precision = precision;
    }

    // Puede sobrescribir el método saludar si se desea
    @Override
    public void saludar() {
        System.out.println("Soy el francotirador " + getNombre() + " con precisión " + precision);
    }

    public int getPrecision() {
        return precision;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[4];
        ejercito[0] = new Artillero("Sargento Rock", 5);
        ejercito[1] = new Zapador("Cabo Mina", 3);
        ejercito[2] = new Artillero("Teniente Fuego", 2);
        ejercito[3] = new Francotirador("Capitán Certero", 95);  // Nuevo tipo añadido

        // Este bucle NO se modifica al añadir Francotirador
        for (Soldado s : ejercito) {
            s.saludar();  // Cada uno, incluido el nuevo francotirador, saluda correctamente
        }
    }
}
```

La extensibilidad se manifiesta en que el bucle `for` que recorre el array **no ha necesitado ninguna modificación** para incorporar al `Francotirador`. El código que depende de la superclase `Soldado` funciona con cualquier subtipo futuro, ya que no necesita conocer los detalles específicos de cada uno. Esto es posible gracias al **polimorfismo** (la capacidad de que una variable de tipo `Soldado` pueda referenciar objetos de cualquier subclase) y a la **ligadura dinámica** (en tiempo de ejecución se ejecuta la versión concreta de `saludar()` correspondiente al objeto real). Si en el futuro se añaden `Medico`, `Piloto` o `Comandante`, el mismo código seguirá funcionando sin cambios, lo que hace que el sistema sea fácilmente extensible, mantenible y menos propenso a errores al incorporar nuevos requisitos. Esta es una de las ventajas más poderosas de la herencia y el polimorfismo en la programación orientada a objetos.


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

Sí, en Java es perfectamente posible tener una referencia del supertipo (como `Soldado`) que apunte a objetos reales de un subtipo (como `Artillero` o `Zapador`). Esta capacidad es la base del polimorfismo. Sin embargo, **con una referencia del supertipo solo se pueden invocar los métodos que están definidos en el supertipo**, aunque el objeto real sea de un subtipo. No se puede invocar directamente un método específico del subtipo (como `dispararCohete()` de `Artillero`) a través de una referencia `Soldado`, porque el compilador solo conoce el tipo declarado de la referencia, no el tipo real del objeto en tiempo de ejecución.

El **`upcasting`** es la conversión automática (implícita) de un subtipo a un supertipo. Ocurre, por ejemplo, al asignar `Soldado s = new Artillero(...);`. Es siempre seguro y nunca falla, porque todo `Artillero` es un `Soldado`. El **`downcasting`** es la conversión explícita de un supertipo a un subtipo, como `Artillero a = (Artillero) s;`. Es necesario cuando se quiere acceder a métodos específicos del subtipo. Como puede fallar (si `s` no es realmente un `Artillero`), se debe usar con precaución. El operador **`instanceof`** permite comprobar si un objeto es de un tipo determinado (o subtipo), devolviendo `true` o `false`. Se utiliza justo antes de un `downcasting` para evitar excepciones de tipo `ClassCastException`.

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[4];
        ejercito[0] = new Artillero("Sargento Rock", 5);
        ejercito[1] = new Zapador("Cabo Mina", 3);
        ejercito[2] = new Artillero("Teniente Fuego", 2);
        ejercito[3] = new Francotirador("Capitán Certero", 95);

        for (Soldado s : ejercito) {
            s.saludar();  // Todos saludan (polimorfismo)
            
            // Comprobar si el objeto real es un Artillero usando instanceof
            if (s instanceof Artillero) {
                // Downcasting seguro: convertir la referencia Soldado a Artillero
                Artillero a = (Artillero) s;
                System.out.println("  -> Tiene " + a.getNumeroCohetes() + " cohetes");
            }
        }
    }
}
```

En este ejemplo, el bucle recorre el array de `Soldado`. Para cada elemento, se utiliza `instanceof` para comprobar si el objeto real es un `Artillero`. Si es así, se realiza un **`downcasting`** explícito (`(Artillero) s`) para obtener una referencia de tipo `Artillero`, con la cual se puede invocar `getNumeroCohetes()`. Tanto `upcasting` (al asignar los objetos al array de tipo `Soldado`) como `downcasting` (con la comprobación previa) se utilizan de forma segura. Es importante destacar que `instanceof` también devuelve `true` si el objeto es de una subclase del tipo comprobado; por ejemplo, si hubiera una clase `ArtilleroAvanzado` que heredara de `Artillero`, también pasaría la comprobación. La práctica recomendada es usar `instanceof` y `downcasting` solo cuando sea estrictamente necesario, ya que su uso excesivo puede indicar un diseño deficiente donde la jerarquía de herencia no captura adecuadamente todo el comportamiento necesario en el supertipo.


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

El modificador de acceso **`protected`** en Java proporciona un nivel de visibilidad intermedio entre `private` (accesible solo desde la misma clase) y `public` (accesible desde cualquier lugar). Un miembro (atributo o método) declarado como `protected` es accesible desde **cualquier clase dentro del mismo paquete** y, además, desde **cualquier subclase, incluso si está en un paquete diferente**. Este nivel es especialmente útil en jerarquías de herencia, donde se desea que las subclases puedan acceder directamente a ciertos miembros de la superclase para reutilizarlos o especializarlos, sin exponerlos completamente al mundo exterior (como sería con `public`). De esta manera, se mantiene un buen equilibrio entre encapsulación y extensibilidad.

La principal diferencia con `private` es que este último impide absolutamente el acceso desde las subclases, obligándolas a usar métodos públicos o protegidos de la superclase para acceder a los datos. `Protected` "abre" la encapsulación controladamente hacia las subclases, asumiendo que ellas conocen y respetan las invariantes de la superclase. Sin embargo, sigue ocultando los detalles a clases no relacionadas que no sean subclases o no estén en el mismo paquete. Es una herramienta de diseño para familias de clases relacionadas por herencia.

```java
public class Soldado {
    protected String nombre;  // Ahora protegido, visible para subclases
    // private String nombre;  // Versión privada original

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

public class Zapador extends Soldado {
    private int numeroMinas;

    public Zapador(String nombre, int numeroMinas) {
        super(nombre);
        this.numeroMinas = numeroMinas;
    }

    public void ponerMina() {
        if (numeroMinas > 0) {
            // Acceso directo al atributo 'nombre' gracias a protected
            System.out.println(nombre + " coloca una mina (acceso directo al nombre protegido)");
            numeroMinas--;
        } else {
            System.out.println(nombre + " no tiene minas");
        }
    }

    public int getNumeroMinas() {
        return numeroMinas;
    }
}
```

En este ejemplo, el atributo `nombre` de `Soldado` se declara como `protected`. Esto permite que la subclase `Zapador` acceda a él directamente dentro del método `ponerMina()`, sin necesidad de utilizar el getter `getNombre()`. Esta decisión de diseño puede ser apropiada si se considera que el nombre es un atributo que las subclases legítimamente necesitan conocer y la superclase confía en que no será mal utilizado. Sin embargo, también tiene desventajas: el atributo `protected` queda expuesto a modificaciones directas desde la subclase (`this.nombre = "otro";`), lo que podría violar invariantes que `Soldado` quisiera mantener. Por ello, muchos diseñadores prefieren mantener los atributos `private` y proporcionar getters `protected` o `public` según el caso. La elección entre `protected` y `private` con métodos de acceso debe basarse en la estabilidad del diseño, la confianza en las subclases y la necesidad de controlar cómo se accede y modifica el estado heredado.


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

No hay una regla universal en todos los lenguajes orientados a objetos. Algunos lenguajes, como **Java**, **C#**, **Objective-C** y **Smalltalk**, poseen una **clase base única** de la que derivan (directa o indirectamente) todas las demás clases. En Java, esa clase raíz se llama `Object` (en el paquete `java.lang`). Esto significa que cualquier clase en Java, ya sea creada por el programador o perteneciente a la biblioteca estándar, es automáticamente una subclase de `Object`, ya sea explícitamente (mediante `extends Object`) o implícitamente por omisión. Otros lenguajes, como **C++** y **Python**, no tienen una clase base universal; en ellos, las clases no están obligadas a heredar de un tronco común y las jerarquías pueden ser múltiples (herencia múltiple en C++) o independientes.

En Java, la existencia de una clase base universal `Object` aporta **comportamientos comunes** a todos los objetos. `Object` define métodos que toda instancia debe tener, como `toString()` (devuelve una representación textual), `equals(Object obj)` (compara igualdad), `hashCode()` (devuelve un código hash), `getClass()` (obtiene información de tiempo de ejecución sobre el tipo), y métodos relacionados con sincronización (`wait()`, `notify()`, `notifyAll()`). Esto garantiza una base homogénea: cualquier objeto puede ser tratado como `Object`, lo que permite construir estructuras de datos genéricas (por ejemplo, antes de la introducción de los genéricos en Java 5, las colecciones como `ArrayList` almacenaban directamente `Object`). Además, permite que métodos como `System.out.println(Object o)` invoque automáticamente `toString()` del objeto, proporcionando una integración elegante.

La clase `Object` también juega un papel crucial en el **polimorfismo extremo**: una referencia de tipo `Object` puede apuntar a cualquier objeto de cualquier clase. Esto es útil en situaciones donde el tipo concreto no es relevante, aunque luego puede requerir `downcasting` para acceder a comportamientos específicos. Sin embargo, parte de la filosofía de diseño de Java es que, aunque todo es un `Object` para ciertas operaciones fundamentales, los tipos primitivos (`int`, `char`, etc.) no heredan de `Object`, lo que obligó a crear las clases envoltorio (`Integer`, `Character`, etc.) para integrarlos en el mundo de los objetos. La clase base única es una característica distintiva de Java que contrasta con lenguajes como C++, donde no existe tal raíz universal, permitiendo una mayor flexibilidad pero también una menor uniformidad en las operaciones básicas sobre objetos.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

La **herencia múltiple** es un mecanismo de la programación orientada a objetos mediante el cual una clase puede **heredar directamente de más de una superclase**. Esto significa que la subclase adquiere los atributos y métodos de todas sus clases padre, pudiendo combinar comportamientos de orígenes diversos. Por ejemplo, una clase `Hijo` podría heredar simultáneamente de `Padre` y de `Madre`, obteniendo características de ambas. Lenguajes como C++ y Python sí permiten herencia múltiple, lo que ofrece gran flexibilidad, pero también introduce problemas de ambigüedad (como el llamado "problema del diamante"), donde una misma firma puede provenir de múltiples caminos de herencia.

**En Java no existe herencia múltiple de clases**. Una clase en Java solo puede extender **como máximo una clase padre** directa (herencia simple). Esta decisión de diseño fue tomada deliberadamente por los creadores de Java para simplificar el lenguaje y evitar la complejidad y la ambigüedad asociadas a la herencia múltiple de implementación. El "problema del diamante" clásico ocurre cuando una clase D hereda de B y C, que a su vez heredan de A, y tanto B como C sobrescriben un mismo método de A, sin quedar claro qué versión debe usar D. En Java esto no puede ocurrir con clases, ya que la jerarquía es siempre un árbol (no un grafo) y cada clase tiene una única línea de ascendencia directa.

Sin embargo, Java ofrece un mecanismo alternativo: las **interfaces**. Una clase en Java puede implementar **múltiples interfaces**, lo que se conoce como **herencia múltiple de tipos**, aunque no de implementación. Las interfaces definen comportamientos (métodos sin implementación, o con implementación `default` a partir de Java 8) que la clase debe proporcionar. Esto permite que un objeto se comporte como múltiples tipos (por ejemplo, `Serializable`, `Comparable` y `Runnable` pueden ser implementados por una misma clase), resolviendo la mayoría de los casos de uso de la herencia múltiple sin sus problemas de ambigüedad. Cuando una clase implementa varias interfaces que tienen métodos `default` con la misma firma, el compilador exige que la clase sobrescriba explícitamente el método conflictivo para resolver la ambigüedad. En definitiva, Java sacrifica la herencia múltiple de clases por la claridad y la robustez, pero proporciona interfaces como un sustituto poderoso y seguro para lograr polimorfismo de múltiples tipos.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente.

```java
// Clase Usuario (inmutable para mantener consistencia con la excepción)
public class Usuario {
    private final String id;
    private final String nombre;

    public Usuario(String id, String nombre) {
        this.id = id;
        this.nombre = nombre;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }

    @Override
    public String toString() {
        return "Usuario{id='" + id + "', nombre='" + nombre + "'}";
    }
}
```

```java
// Excepción personalizada NO CONTROLADA (hereda de RuntimeException)
public class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuario;  // Composición: la excepción contiene el usuario problemático

    // Constructor sin causa
    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario);
        this.usuario = usuario;
    }

    // Constructor con causa (para encadenamiento de excepciones)
    public UsuarioNoEncontradoException(Usuario usuario, Throwable cause) {
        super("Usuario no encontrado: " + usuario, cause);
        this.usuario = usuario;
    }

    // Getter para acceder al usuario que causó el problema
    public Usuario getUsuario() {
        return usuario;
    }
}
```

```java
// Ejemplo de uso
public class ServicioUsuarios {
    // Simula una búsqueda que puede fallar
    public Usuario buscarUsuarioPorId(String id) {
        // Simulación: solo existe el usuario "123"
        if ("123".equals(id)) {
            return new Usuario("123", "Ana García");
        }
        
        // Si no se encuentra, se lanza la excepción personalizada
        Usuario usuarioBuscado = new Usuario(id, "Desconocido");
        throw new UsuarioNoEncontradoException(usuarioBuscado);
    }

    // Versión que propaga una causa subyacente
    public Usuario buscarUsuarioConCausa(String id) {
        try {
            // Simular una operación de base de datos que falla
            throw new SQLException("Error de conexión a la base de datos");
        } catch (SQLException e) {
            Usuario usuarioBuscado = new Usuario(id, "Desconocido");
            throw new UsuarioNoEncontradoException(usuarioBuscado, e);
        }
    }

    public static void main(String[] args) {
        ServicioUsuarios servicio = new ServicioUsuarios();
        
        try {
            servicio.buscarUsuarioPorId("999");
        } catch (UsuarioNoEncontradoException e) {
            System.out.println("Error: " + e.getMessage());
            System.out.println("Usuario problemático: " + e.getUsuario());
        }

        try {
            servicio.buscarUsuarioConCausa("456");
        } catch (UsuarioNoEncontradoException e) {
            System.out.println("\nError con causa: " + e.getMessage());
            System.out.println("Causa original: " + e.getCause());
            e.printStackTrace();  // Muestra ambas trazas
        }
    }
}
```

Este ejemplo ilustra una excepción personalizada **no controlada** (hereda de `RuntimeException`), lo que significa que el compilador no obliga a declararla `throws` ni a capturarla, aunque es recomendable hacerlo cuando sea apropiado. La excepción utiliza **composición** para almacenar el objeto `Usuario` que causó el problema, proporcionando un `getter` para acceder a él. Esto permite al código que captura la excepción no solo saber que no se encontró, sino también exactamente qué usuario se buscaba (incluyendo sus atributos `id` y `nombre`), facilitando acciones como loguear información detallada o intentar una recuperación específica.

La sobrecarga de constructores sigue el patrón estándar de las excepciones de Java: una versión simple y otra que acepta una `Throwable cause`. Esto permite el **encadenamiento de excepciones**, donde una excepción de nivel superior (`UsuarioNoEncontradoException`) envuelve una excepción de bajo nivel (como `SQLException`). La causa se pasa al constructor de la superclase (`RuntimeException`) mediante `super(mensaje, cause)`, preservando la traza completa para depuración. Al llamar a `printStackTrace()` sobre la excepción personalizada, se mostrará tanto la traza de la excepción como la de su causa, lo que facilita enormemente la localización del error original en sistemas multicapa.


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

La razón fundamental por la que no se debe emplear herencia simplemente por reutilizar código es que la herencia **crea un acoplamiento muy fuerte** entre la superclase y la subclase, mucho mayor que el que se obtiene con la composición. Cuando una clase hereda de otra, no solo reutiliza su código, sino que queda **permanentemente atada** a su implementación interna, incluyendo detalles que podrían no ser necesarios o que podrían cambiar. Cualquier modificación en la superclase (incluso aparentemente inocua) puede romper el comportamiento de todas sus subclases, un fenómeno conocido como el **"problema de la clase base frágil"** (fragile base class problem). Además, la herencia es una relación "es-un" que debe cumplir el **principio de sustitución de Liskov**: una subclase debe poder sustituir a su superclase sin alterar el comportamiento esperado del programa. Forzar herencia solo por reutilizar código suele violar este principio, porque se crean jerarquías artificiales donde la subclase no es realmente un subtipo de la superclase.

La **composición** (una clase "tiene-un" o "tiene-varios" objetos de otra clase) es una alternativa mucho más flexible y desacoplada para reutilizar código. Al componer objetos, las clases se comunican a través de interfaces bien definidas, sin quedar atadas a los detalles internos de sus colaboradores. Esto permite cambiar la implementación de un componente sin afectar a los demás, facilita las pruebas unitarias (se pueden inyectar simulaciones o *mocks*) y evita la propagación de cambios no deseados a lo largo de una jerarquía. La composición también respeta mejor el principio de **encapsulación**, ya que una clase puede exponer exactamente la funcionalidad que necesita de sus componentes, ocultando el resto. Por ejemplo, si una clase `Coche` necesita un motor, es más apropiado que contenga una referencia a un objeto `Motor` (composición) a que herede de `Motor`. Un coche **no es un** motor; un coche **tiene un** motor.

El eslogan de diseño **"preferir la composición sobre la herencia"** (favor composition over inheritance) resume esta recomendación. La herencia debe reservarse para relaciones genuinas de subtipado donde exista una jerarquía conceptual clara y se cumpla el principio de sustitución de Liskov. Para reutilizar código de forma más flexible y segura, la composición, junto con la delegación y la inyección de dependencias, ofrece mejores resultados a largo plazo, facilitando el mantenimiento, la evolución y la reutilización en sistemas de software complejos. La herencia no está prohibida, pero debe ser una decisión de diseño consciente y justificada, no el mecanismo por defecto para reutilizar unas pocas líneas de código.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

La máxima de **"favorecer la composición frente a la herencia"** es uno de los principios de diseño más citados en la programación orientada a objetos, y responde fundamentalmente a que la composición proporciona un **acoplamiento más débil**, una **flexibilidad superior** y una **mejor encapsulación** que la herencia. Mientras que la herencia establece una relación estática y rígida determinada en tiempo de compilación (una vez que una clase extiende a otra, esa decisión es irreversible), la composición permite establecer relaciones dinámicas y cambiantes en tiempo de ejecución. Un objeto puede modificar su comportamiento simplemente cambiando las referencias a los objetos que lo componen, algo imposible con la herencia sin recompilar y modificar el código fuente.

La composición también evita el **problema de la clase base frágil** (fragile base class problem), donde cambios aparentemente seguros en una superclase pueden romper inadvertidamente el funcionamiento de sus subclases. Al componer, las clases interactúan únicamente a través de interfaces públicas bien definidas, aislando los impactos de los cambios. Si una clase `C` utiliza un objeto `D` mediante composición, una modificación en la implementación de `D` solo afectará a `C` si se cambia su interfaz pública. En cambio, en la herencia, modificar un método protegido o incluso privado en la superclase puede alterar el comportamiento esperado en la subclase, especialmente si la subclase sobrescribe otros métodos que dependen indirectamente de ese cambio. Esto hace que el código basado en herencia sea más frágil y difícil de mantener a gran escala.

Además, la composición respeta mejor el principio de **encapsulación**. Una clase no necesita exponer sus detalles internos a sus subclases para que estas reutilicen su comportamiento; simplemente se delega en objetos colaboradores. La herencia, en cambio, a menudo fuerza a las subclases a conocer la implementación interna de la superclase (por ejemplo, usando miembros `protected`), rompiendo el encapsulamiento. Finalmente, la composición ofrece una **mayor reutilización**: una clase puede ser compuesta con muchas otras de diferentes familias, mientras que una subclase solo puede extender una única superclase (en lenguajes de herencia simple como Java). Por todas estas razones, la composición es generalmente preferible: produce sistemas más flexibles, desacoplados, testables y mantenibles. La herencia no debe abandonarse por completo, sino utilizarse solo cuando existe una relación genuina "es-un" que cumpla el principio de sustitución de Liskov y cuando los beneficios de la jerarquía de tipos superen las desventajas del acoplamiento adicional.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

La afirmación de que la **"herencia rompe la encapsulación"** se refiere a que, para que una subclase pueda reutilizar el comportamiento de su superclase de manera efectiva, a menudo necesita **conocer y depender de los detalles internos** de la superclase, no solo de su interfaz pública. A diferencia de un cliente cualquiera que utiliza una clase exclusivamente a través de sus métodos públicos, una subclase tiene acceso a los miembros `protected` (e incluso, en ciertos lenguajes como C++, a los miembros `private` mediante métodos de acceso especiales). Esta visibilidad ampliada crea una dependencia más íntima y frágil: si la superclase cambia su implementación interna (por ejemplo, modifica cómo gestiona un atributo `protected` o cambia la lógica de un método `protected`), todas las subclases pueden verse afectadas y potencialmente romperse, incluso si la interfaz pública permanece intacta.

Esta ruptura de la encapsulación es particularmente problemática en el **problema de la clase base frágil**. Considérese una superclase `Lista` que expone un método `añadirTodos(Collection c)` que, por eficiencia, añade los elementos uno a uno llamando a un método `añadir()` que las subclases pueden sobrescribir. Si la implementación interna de `añadirTodos` cambia posteriormente para optimizar la inserción (por ejemplo, evitando llamadas redundantes a `añadir`), las subclases que sobrescribían `añadir` podrían dejar de funcionar correctamente porque sus expectativas sobre cuándo se invoca su método se ven violadas. La superclase no puede prever todas las posibles formas en que las subclases podrían depender de sus detalles internos, y las subclases no pueden prever cómo evolucionará la implementación de la superclase.

La composición, en cambio, **no rompe la encapsulación**. Una clase `A` que compone un objeto de clase `B` solo interactúa con `B` a través de su interfaz pública (métodos `public`). Si la implementación de `B` cambia, pero su interfaz pública permanece igual, `A` no se ve afectada. Además, `A` no tiene acceso a los miembros privados o protegidos de `B`, por lo que no puede crear dependencias ocultas en los detalles internos de `B`. Esto hace que el sistema sea más modular, fácil de mantener y menos propenso a efectos colaterales inesperados. Por esta razón, la herencia debe utilizarse con mucha precaución y solo cuando la relación "es-un" es clara y estable; para la reutilización de comportamiento, la composición ofrece un diseño más robusto y encapsulado a largo plazo.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

## Modelo con Herencia

```java
// Superclase que encapsula los datos comunes
public class Persona {
    private final String dni;
    private final String nombre;

    public Persona(String dni, String nombre) {
        if (dni == null || nombre == null) {
            throw new IllegalArgumentException("DNI y nombre no pueden ser nulos");
        }
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }

    @Override
    public String toString() {
        return nombre + " (" + dni + ")";
    }
}

// Estudiante "es-una" Persona
public class Estudiante extends Persona {
    private String carrera;

    public Estudiante(String dni, String nombre, String carrera) {
        super(dni, nombre);  // Reutiliza el constructor de Persona
        this.carrera = carrera;
    }

    public String getCarrera() { return carrera; }
    public void setCarrera(String carrera) { this.carrera = carrera; }

    @Override
    public String toString() {
        return super.toString() + " - Estudiante de " + carrera;
    }
}

// Trabajador "es-una" Persona
public class Trabajador extends Persona {
    private String empresa;

    public Trabajador(String dni, String nombre, String empresa) {
        super(dni, nombre);
        this.empresa = empresa;
    }

    public String getEmpresa() { return empresa; }
    public void setEmpresa(String empresa) { this.empresa = empresa; }

    @Override
    public String toString() {
        return super.toString() + " - Trabaja en " + empresa;
    }
}
```

## Modelo con Composición

```java
// Clase que encapsula los datos personales (reutilizable por composición)
public class DatosPersonales {
    private final String dni;
    private final String nombre;

    public DatosPersonales(String dni, String nombre) {
        if (dni == null || nombre == null) {
            throw new IllegalArgumentException("DNI y nombre no pueden ser nulos");
        }
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() { return dni; }
    public String getNombre() { return nombre; }

    @Override
    public String toString() {
        return nombre + " (" + dni + ")";
    }
}

// Estudiante "tiene-unos" DatosPersonales (composición)
public class Estudiante {
    private final DatosPersonales datosPersonales;  // Composición
    private String carrera;

    public Estudiante(DatosPersonales datosPersonales, String carrera) {
        if (datosPersonales == null) {
            throw new IllegalArgumentException("Datos personales no pueden ser nulos");
        }
        this.datosPersonales = datosPersonales;
        this.carrera = carrera;
    }

    // Métodos de delegación hacia DatosPersonales
    public String getDni() { return datosPersonales.getDni(); }
    public String getNombre() { return datosPersonales.getNombre(); }
    public String getCarrera() { return carrera; }
    public void setCarrera(String carrera) { this.carrera = carrera; }

    @Override
    public String toString() {
        return datosPersonales.toString() + " - Estudiante de " + carrera;
    }
}

// Trabajador "tiene-unos" DatosPersonales (composición)
public class Trabajador {
    private final DatosPersonales datosPersonales;  // Composición
    private String empresa;

    public Trabajador(DatosPersonales datosPersonales, String empresa) {
        if (datosPersonales == null) {
            throw new IllegalArgumentException("Datos personales no pueden ser nulos");
        }
        this.datosPersonales = datosPersonales;
        this.empresa = empresa;
    }

    // Métodos de delegación hacia DatosPersonales
    public String getDni() { return datosPersonales.getDni(); }
    public String getNombre() { return datosPersonales.getNombre(); }
    public String getEmpresa() { return empresa; }
    public void setEmpresa(String empresa) { this.empresa = empresa; }

    @Override
    public String toString() {
        return datosPersonales.toString() + " - Trabaja en " + empresa;
    }
}
```

## Comparación de los dos enfoques

**Ventajas de la herencia**:
- Código más conciso: no hay necesidad de delegar manualmente cada método de `Persona`.
- Polimorfismo automático: un `Estudiante` puede tratarse como `Persona` en arrays y métodos.
- Relación conceptual clara si realmente la entidad "es" una persona.

**Ventajas de la composición**:
- Mayor flexibilidad: un `Estudiante` podría cambiar sus `DatosPersonales` en tiempo de ejecución (si no fuera `final`).
- Un `Estudiante` no queda atado a la jerarquía `Persona`; puede tener múltiples roles o ser compuesto con otros objetos.
- Mejor encapsulación: `DatosPersonales` es independiente y puede evolucionar sin afectar a sus usuarios.
- Evita el problema de la clase base frágil y permite reutilizar la misma instancia de `DatosPersonales` entre varios objetos (por ejemplo, un `Estudiante` que también es `Trabajador` podría compartir los mismos datos personales).

**Cuándo elegir uno u otro**: Si la relación "es-un" es genuina, estable y se cumplen todas las expectativas del principio de sustitución de Liskov (por ejemplo, un `Estudiante` puede hacer todo lo que una `Persona` hace), la herencia puede ser adecuada. Si solo se busca reutilizar el código de `DatosPersonales` pero no existe una relación de subtipado conceptual (o se prevé que la jerarquía podría complicarse), la composición es preferible. Java, al ser de herencia simple, fuerza además esta decisión: con herencia, `Estudiante` no puede extender ninguna otra clase; con composición, puede extender otra clase (por ejemplo, `AlumnoUniversitario`) y a la vez tener `DatosPersonales`. La composición suele ser más flexible a largo plazo.