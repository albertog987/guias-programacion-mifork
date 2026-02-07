# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

Las cuatro características básicas de la programación orientada a objetos son: encapsulamiento, herencia, polimorfismo y abstracción. Estas características, cuando se combinan, permiten modelar problemas de manera más cercana a cómo se conceptualizan en el mundo real.

El **encapsulamiento** consiste en agrupar los datos (atributos) y los métodos (funciones) que operan sobre esos datos dentro de una unidad llamada clase, y restringir el acceso directo a algunos de sus componentes internos. Esto se logra típicamente mediante modificadores de acceso como private y public. El principal beneficio es proteger la integridad de los datos, ya que solo se puede modificar el estado interno del objeto a través de métodos públicos definidos, lo que previene modificaciones no controladas.

La **herencia** permite crear una nueva clase (clase hija o subclase) basada en una clase existente (clase padre o superclase), adoptando sus atributos y métodos. Esto facilita la reutilización de código y establece una relación jerárquica de "es-un". Por ejemplo, una clase CocheDeportivo puede heredar de una clase más general Coche, adquiriendo sus propiedades básicas como ruedas y motor, y a la vez añadir sus propias características especiales.

El **polimorfismo** (que significa "muchas formas") permite que objetos de diferentes clases respondan de manera específica a un mismo mensaje o método. Esto se manifiesta cuando una subclase redefine un método heredado de su superclase, o cuando se usa una interfaz común para referirse a objetos de distintos tipos. Por último, la abstracción consiste en ocultar los detalles de implementación complejos y mostrar solo la funcionalidad esencial al usuario, normalmente a través de clases abstractas o interfaces, que definen qué debe hacer un objeto sin especificar necesariamente cómo lo hace.

La **abstracción** es el principio que permite modelar entidades del mundo real simplificando su complejidad, enfocándose únicamente en los aspectos relevantes para el sistema que se está desarrollando. Se implementa mediante clases que exponen una interfaz clara de métodos públicos, ocultando los detalles internos de cómo se llevan a cabo las operaciones. Por ejemplo, al usar un objeto Coche, se conoce el método arrancar(), pero no es necesario conocer los complejos procesos electrónicos y mecánicos que ocurren internamente para ejecutar esa acción.


## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

Cuatro lenguajes de programación populares que permiten la programación orientada a objetos son Java, C++, Python y C#. Estos lenguajes, aunque tienen diferentes filosofías y sintaxis, incorporan los principios fundamentales de clases, objetos, herencia y polimorfismo, permitiendo el desarrollo de software modular y reutilizable.

**Java** es un lenguaje diseñado desde su origen para ser orientado a objetos, donde casi todos los elementos son objetos y se ejecuta en la Máquina Virtual de Java (JVM), lo que garantiza portabilidad entre diferentes sistemas operativos. **C++** es un lenguaje multiparadigma que extiende el lenguaje C, añadiéndole características de orientación a objetos como clases y herencia, manteniendo al mismo tiempo la eficiencia y el control de bajo nivel del código procedural, lo que lo hace ideal para sistemas donde el rendimiento es crítico.

**Python**, conocido por su sintaxis clara y legible, soporta la orientación a objetos de una manera muy flexible y dinámica, permitiendo la creación de clases y objetos de forma sencilla, y es ampliamente utilizado en campos como la ciencia de datos y el desarrollo web. Por último, **C#** es un lenguaje moderno desarrollado por Microsoft, fuertemente orientado a objetos y parte del ecosistema .NET, que comparte similitudes con Java pero con características propias como propiedades y eventos integrados en el lenguaje, siendo una opción principal para el desarrollo de aplicaciones Windows y videojuegos con Unity.


## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

La **programación estructurada** es un paradigma de programación anterior a la orientación a objetos que se centra en mejorar la claridad, calidad y tiempo de desarrollo de un programa mediante el uso extensivo de estructuras de control de flujo claras y la evitación total de la instrucción `goto`. Se basa en tres estructuras básicas: la secuencia (ejecución de instrucciones en orden), la selección (uso de condicionales como `if`/`else`) y la iteración (bucles como `while` y `for`). Su objetivo principal es lograr programas más legibles, mantenibles y menos propensos a errores, al descomponer problemas complejos en subproblemas más pequeños y manejables, que se resuelven mediante procedimientos o funciones.

Por otro lado, la **programación modular** es una evolución o un enfoque complementario dentro de la programación estructurada. Consiste en dividir un programa en módulos o unidades funcionales separadas, cada una con una responsabilidad específica. Estos módulos, que en lenguajes como C se implementan típicamente en archivos de código fuente separados (`.c` y `.h`), contienen conjuntos de funciones y datos relacionados. La comunicación entre módulos se realiza a través de interfaces bien definidas, ocultando los detalles de implementación interna.

La diferencia clave con la programación orientada a objetos radica en el foco: mientras la programación modular organiza el código alrededor de *funciones* y *procedimientos*, la orientación a objetos lo organiza alrededor de *datos* (objetos) que combinan estado y comportamiento. La programación modular promueve la reutilización de código y la reducción de la complejidad, siendo un paso fundamental hacia paradigmas más avanzados. Lenguajes como C y Pascal ejemplifican este enfoque, donde la estructura principal del programa es una jerarquía de llamadas a funciones, no una red de objetos interactuantes.


## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Los tres elementos que definen a un objeto en programación orientada a objetos son: identidad, estado y comportamiento. Estos elementos distinguen a un objeto de una mera colección de datos o de una función aislada, dotándolo de una representación coherente y autocontenida de una entidad dentro del sistema.

La **identidad** es la propiedad que hace que un objeto sea único y diferenciable de cualquier otro, incluso si su estado es idéntico. En la práctica, la identidad suele estar representada por la referencia o dirección de memoria que apunta al objeto, lo que garantiza que dos instancias creadas por separado sean consideradas distintas. El **estado** está definido por el conjunto de valores de sus atributos o propiedades en un momento dado. Estos valores, almacenados en variables miembro, representan las características concretas del objeto y pueden cambiar a lo largo de su ciclo de vida, por ejemplo, el nivel de combustible de un objeto `Coche`.

El **comportamiento** está determinado por los métodos o funciones asociadas al objeto, que definen qué operaciones puede realizar y cómo responde a los mensajes. Los métodos pueden consultar o modificar el estado interno del objeto y definir la interacción con otros objetos. En conjunto, estos tres elementos permiten modelar entidades del mundo real: la identidad responde a "quién es" el objeto, el estado a "cómo está" en un instante y el comportamiento a "qué puede hacer". Esta encapsulación de identidad, estado y comportamiento dentro de una unidad es la esencia de un objeto.


## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

Una **clase** es una plantilla o un plano que define un tipo de dato abstracto. Especifica un conjunto de atributos (variables que almacenan el estado) y métodos (funciones que definen el comportamiento) que serán comunes a todos los objetos creados a partir de ella. En términos prácticos, una clase actúa como un molde a partir del cual se pueden crear múltiples entidades concretas, estableciendo qué datos se guardan y qué operaciones se pueden realizar, pero sin ser ella misma un dato concreto. Por ejemplo, la clase `Libro` podría definir los atributos `titulo` y `autor`, y métodos como `prestar()`.

No es lo mismo una clase que un objeto. La clase es la definición abstracta, mientras que un **objeto** es una **instancia** concreta de esa clase, creada en tiempo de ejecución con valores específicos para sus atributos. Si la clase es el plano de una casa, el objeto es la casa real construida a partir de ese plano. Cada objeto ocupa su propia región de memoria y tiene una identidad única, incluso si sus atributos tienen los mismos valores que otro objeto. El proceso de crear un objeto a partir de una clase se denomina **instanciación**, y por ello "objeto" e "instancia" son términos que a menudo se usan de manera intercambiable.

No todos los lenguajes orientados a objetos manejan el concepto de clase. Existen lenguajes basados en **prototipos**, como JavaScript, donde los objetos se crean directamente a partir de otros objetos existentes (prototipos) y no a partir de clases abstractas. En estos lenguajes, la herencia se realiza mediante la delegación a un objeto prototipo, en lugar de mediante una jerarquía de clases. Sin embargo, la mayoría de los lenguajes OOP populares, como Java, C++, C# y Python, son lenguajes basados en clases, donde la clase es la construcción fundamental para definir tipos y crear objetos.


## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

En los lenguajes orientados a objetos como Java y C#, los objetos se almacenan dinámicamente en una región de memoria llamada **heap** o montón. Este espacio de memoria se gestiona en tiempo de ejecución y permite la asignación de bloques de tamaño variable para los datos de los objetos, cuyos atributos y estructuras internas residen allí. Las variables que referencian a estos objetos (las referencias o punteros) se almacenan típicamente en la **pila** (stack) o como parte de otros objetos en el propio heap, conteniendo la dirección de memoria donde se encuentra el objeto real.

No es igual en todos los lenguajes. En C++, por ejemplo, el programador tiene un control directo y puede elegir dónde crear un objeto: en el heap (usando `new`, similar a Java) o en la stack (con una declaración local, lo que proporciona un ciclo de vida automático vinculado al ámbito de la función). Esta flexibilidad ofrece mayor rendimiento pero también requiere una gestión manual de la memoria en muchos casos. En Python, todos los objetos también residen en un heap gestionado dinámicamente, pero todas las variables son referencias a estos objetos, simplificando el modelo aunque ocultando los detalles de bajo nivel.

La **recolección de basura** (garbage collection) es un mecanismo automático de gestión de memoria que identifica y libera la memoria del heap que ya no está en uso por el programa, es decir, memoria ocupada por objetos a los que ninguna referencia activa puede acceder. Este proceso evita las fugas de memoria (memory leaks) y libera al programador de la responsabilidad de desasignar memoria manualmente (como con `delete` en C++). La implementación varía entre lenguajes; Java y C# tienen recolectores de basura integrados, mientras que en C++ este mecanismo no es parte del lenguaje estándar, requiriendo librerías externas o una gestión manual.


## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

Un **método** es una función o procedimiento definido dentro de una clase que describe un comportamiento o una operación que los objetos de esa clase pueden realizar. Los métodos tienen acceso a los atributos del objeto (sus variables de instancia) y pueden modificarlos o consultarlos, permitiendo así la interacción con el estado interno. Definen la interfaz pública a través de la cual otros objetos o partes del programa pueden comunicarse con una instancia, encapsulando la lógica asociada a los datos.

La **sobrecarga de métodos** (method overloading) es una característica que permite definir múltiples métodos dentro de una misma clase con el **mismo nombre**, pero con una **firma diferente**. La firma de un método está determinada por el número, tipo y orden de sus parámetros. El compilador (o intérprete) decide cuál de los métodos sobrecargados debe ejecutarse en tiempo de compilación, basándose en los argumentos proporcionados en la llamada. Esto no debe confundirse con la **sobreescritura** (overriding), que ocurre en la herencia y permite a una subclase redefinir un método ya existente en la superclase.

La sobrecarga mejora la legibilidad y la usabilidad del código, permitiendo ofrecer diferentes maneras de realizar una operación similar sin necesidad de inventar nombres distintos para variaciones de la misma tarea. Por ejemplo, una clase `Calculadora` podría tener varios métodos llamados `sumar()`: uno que acepte dos enteros, otro que acepte tres enteros y otro que acepte dos números decimales. Es importante destacar que el tipo de retorno por sí solo no diferencia la firma de un método; la distinción debe radicar exclusivamente en los parámetros.


## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

```java
public class Punto {
    double x;
    double y;

    public double calculaDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Punto miPunto = new Punto();
        miPunto.x = 3.0;
        miPunto.y = 4.0;

        double distancia = miPunto.calculaDistanciaAOrigen();
        System.out.println("La distancia al origen es: " + distancia); // Imprimirá 5.0
    }
}
```

La clase `Punto` define dos atributos, `x` e `y`, con visibilidad de paquete (por defecto), lo que permite un acceso directo a ellos desde otras clases dentro del mismo paquete, aunque en un diseño más robusto se suelen declarar como privados y se accede a través de métodos *getter* y *setter*. El método `calculaDistanciaAOrigen` utiliza los valores almacenados en estos atributos para aplicar la fórmula matemática de la distancia euclidiana al origen (0,0), devolviendo el resultado como un valor de tipo `double`.

En el ejemplo de uso, la clase `Main` contiene el método `main`, que es el punto de entrada del programa. En él, se crea una instancia de `Punto` llamada `miPunto` utilizando el constructor por defecto que Java provee implícitamente. Posteriormente, se asignan valores concretos a sus atributos `x` e `y`. Finalmente, se invoca el método `calculaDistanciaAOrigen` sobre este objeto, almacenando el resultado y mostrándolo por consola, lo que en este caso concreto imprimirá el valor `5.0`, de acuerdo con el clásico triángulo rectángulo de lados 3 y 4.


## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

El punto de entrada en un programa Java es el método **`main`**, que debe tener una firma específica: `public static void main(String[] args)`. Este método es invocado por la Máquina Virtual de Java (JVM) al iniciar la ejecución de la aplicación y es obligatorio en cualquier programa ejecutable. El array de `String` `args` permite recibir argumentos desde la línea de comandos.

La palabra clave **`static`** denota que un miembro (método o variable) pertenece a la clase en sí misma, y no a ninguna instancia particular de la clase. Esto significa que un método o variable `static` puede ser accedido directamente utilizando el nombre de la clase, sin necesidad de crear un objeto. El método `main` debe ser `static` porque la JVM necesita poder llamarlo sin tener que instanciar primero ningún objeto de la clase que lo contiene.

`Static` no se emplea únicamente para el método `main`. También se utiliza para crear variables de clase (compartidas por todas las instancias), métodos utilitarios que no operan sobre el estado de un objeto (como `Math.sqrt()`), o bloques de inicialización estática. Por ejemplo, un contador del número total de instancias creadas de una clase suele almacenarse en una variable `static`.

Cuando `static` se combina con **`final`**, se crea una **constante a nivel de clase**. Una variable `static final` es compartida por todas las instancias y su valor no puede ser modificado después de su inicialización, siendo comúnmente utilizada para definir valores fijos como configuraciones o constantes matemáticas. Por ejemplo, `public static final double PI = 3.141592653589793;` en una clase `Matematicas`. La combinación `static final` asegura que solo exista una copia en memoria de la constante y que esta sea inmutable.


## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

Para compilar y ejecutar un programa Java desde la línea de comandos, primero se escribe el código fuente en un archivo con extensión `.java`, por ejemplo, `MiPrograma.java`. Luego, se utiliza el comando `javac MiPrograma.java` para compilarlo. Este proceso traduce el código fuente legible por humanos a un código intermedio llamado **byte-code**, generando uno o más archivos con extensión `.class` (como `MiPrograma.class`). Finalmente, para ejecutar el programa, se usa el comando `java MiPrograma` (sin la extensión `.class`), que invoca a la Máquina Virtual de Java (JVM) para que interprete y ejecute el byte-code contenido en el archivo `.class`.

Java es considerado tanto un lenguaje **compilado** como **interpretado**. Es compilado porque el código fuente se traduce completamente a byte-code mediante `javac` (compilador Java), pero no a código máquina nativo de un procesador específico. En cambio, es interpretado (o más precisamente, compilado *just-in-time* - JIT) porque el byte-code resultante es ejecutado por la JVM, que actúa como un intérprete o un compilador en tiempo de ejecución. Esta dualidad es clave para el principio "write once, run anywhere" (escribe una vez, ejecuta en cualquier lugar).

La **Máquina Virtual de Java (JVM)** es un programa que proporciona un entorno de ejecución para el byte-code de Java. Actúa como una capa de abstracción entre el código compilado y el hardware y sistema operativo subyacente, permitiendo que los programas Java sean portables entre diferentes plataformas sin necesidad de recompilarlos. El **byte-code** es un conjunto de instrucciones optimizadas, de bajo nivel pero independientes de la arquitectura, que la JVM puede entender y ejecutar. Cada archivo `.class` contiene el byte-code correspondiente a una clase definida en el código fuente, incluyendo sus métodos, atributos y metadatos. La JVM carga estos archivos `.class`, verifica el byte-code y finalmente lo ejecuta, utilizando a menudo un compilador JIT para traducir secuencias de byte-code frecuentemente usadas a código máquina nativo para mayor velocidad.


## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

La palabra clave **`new`** en Java es un operador que se utiliza para crear una nueva **instancia** u objeto de una clase. Su función es asignar memoria dinámica en el heap para almacenar el estado del nuevo objeto (sus atributos) y devolver una referencia a esa ubicación de memoria. Además, `new` invoca automáticamente al **constructor** de la clase, que es un bloque de código especial que inicializa el objeto recién creado.

Un **constructor** es un método especial dentro de una clase que tiene el mismo nombre que la clase y no tiene tipo de retorno (ni siquiera `void`). Su propósito principal es inicializar el estado interno del objeto, asignando valores iniciales a sus atributos. Si no se define explícitamente ningún constructor, Java provee un **constructor por defecto** (sin parámetros) que asigna valores predeterminados (como `0`, `null`, o `false`). Sin embargo, es común definir constructores con parámetros para garantizar que el objeto se cree en un estado válido y consistente desde el momento de su creación.

Por ejemplo, en una clase `Empleado`, un constructor podría recibir como parámetros el DNI, nombre y apellidos para inicializar los atributos correspondientes al instanciar el objeto. Esto asegura que no exista un empleado sin estos datos básicos.

```java
public class Empleado {
    String dni;
    String nombre;
    String apellidos;

    // Constructor
    public Empleado(String dni, String nombre, String apellidos) {
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }
}
```

Para crear un objeto `Empleado` usando este constructor, se utilizaría `new Empleado("12345678A", "Ana", "García López")`. El uso de `this` dentro del constructor es necesario para distinguir los atributos de la clase de los parámetros que tienen el mismo nombre, asignando los valores recibidos a los atributos del objeto que se está inicializando.


## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

La referencia **`this`** en Java es una palabra clave que, dentro de un método de instancia o de un constructor, hace referencia al **objeto actual** sobre el que se está actuando. Proporciona una manera de acceder a los atributos y métodos de la propia instancia cuando hay ambigüedad de nombres, especialmente cuando los parámetros de un método tienen el mismo identificador que los atributos de la clase. También se utiliza para llamar a otros constructores de la misma clase o para pasar la instancia actual como argumento a otros métodos.

No se llama igual en todos los lenguajes. En C++ y C# también se utiliza la palabra clave `this`, aunque en C++ es un puntero, por lo que se accede a los miembros con `this->atributo`. En Python, la referencia equivalente es `self`, que debe ser el primer parámetro explícito de los métodos de instancia. En JavaScript, la palabra clave es `this` también, pero su semántica y comportamiento son notablemente diferentes y más complejos debido a su vinculación dinámica.

Un ejemplo del uso de `this` en la clase `Punto` sería dentro de un constructor que reciba parámetros con el mismo nombre que los atributos, para realizar la asignación correctamente. También se podría usar en un método que compare la instancia actual con otro objeto.

```java
public class Punto {
    double x;
    double y;

    // Constructor usando 'this' para desambiguar
    public Punto(double x, double y) {
        this.x = x; // 'this.x' se refiere al atributo, 'x' al parámetro
        this.y = y;
    }

    // Método que usa 'this' para pasar la instancia actual
    public boolean esMismoPunto(Punto otro) {
        return (this.x == otro.x && this.y == otro.y);
    }
}
```

En el constructor, `this.x` especifica el atributo `x` del objeto que se está inicializando, diferenciándolo del parámetro `x` recibido. Sin `this`, la asignación `x = x` sería ambigua y solo asignaría el parámetro a sí mismo, dejando el atributo sin inicializar. El uso de `this` clarifica y asegura la correcta inicialización del estado del objeto.


## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

```java
public class Punto {
    double x;
    double y;

    // ... (otros métodos y constructores previos)

    public double distanciaA(Punto otroPunto) {
        double diferenciaX = this.x - otroPunto.x;
        double diferenciaY = this.y - otroPunto.y;
        return Math.sqrt(diferenciaX * diferenciaX + diferenciaY * diferenciaY);
    }
}
```

El método `distanciaA` es un método de instancia que recibe un parámetro de tipo `Punto`, representando otro punto en el plano. Para calcular la distancia euclidiana entre el punto actual (`this`) y el punto pasado como argumento (`otroPunto`), se calcula la diferencia entre sus coordenadas `x` y `y` respectivas. Estas diferencias se elevan al cuadrado, se suman y, finalmente, se obtiene la raíz cuadrada del resultado utilizando `Math.sqrt`.

El uso de `this` en la expresión `this.x` es explícito para enfatizar que se accede a los atributos del objeto receptor del mensaje, aunque en este contexto no sería estrictamente necesario porque no hay ambigüedad de nombres con los parámetros del método. Sin embargo, mejora la legibilidad al dejar claro qué coordenadas pertenecen a qué objeto. Para utilizar este método, se llamaría desde una instancia de `Punto`, por ejemplo: `double d = punto1.distanciaA(punto2);`, donde `punto1` sería el objeto referenciado por `this` y `punto2` el parámetro `otroPunto`.


## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función?

En Java, el paso de parámetros es **siempre por valor**. Sin embargo, es crucial distinguir qué es lo que se copia. Cuando el parámetro es un tipo primitivo (como `int`, `double`, `boolean`), se copia el valor mismo del dato. Si el parámetro es una referencia a un objeto (como `Punto`), lo que se copia es el valor de la referencia (la dirección de memoria), no el objeto en sí.

Por lo tanto, al pasar un objeto `Punto` a un método, el método recibe una copia de la referencia que apunta al mismo objeto en memoria. Si dentro del método se modifican los atributos de ese objeto (por ejemplo, `otroPunto.x = 10;`), esa modificación **sí afecta** al objeto original fuera del método, porque tanto la referencia original como la copia apuntan a la misma ubicación de memoria. Sin embargo, si dentro del método se intenta reasignar la propia referencia (por ejemplo, `otroPunto = new Punto();`), esa reasignación solo afecta a la copia local de la referencia, no a la referencia original fuera del método. El objeto original permanece inalterado.

En el caso de pasar un entero (`int`), se copia su valor. Si dentro de la función se modifica el valor del parámetro (por ejemplo, `parametro = 20;`), esta modificación es local al método y **no tiene ningún efecto** sobre la variable original que se pasó como argumento. Esto ocurre porque el método trabaja con una copia independiente del valor primitivo. Java no tiene un mecanismo para pasar primitivos por referencia, a diferencia de lenguajes como C++ que permiten el uso de punteros o referencias explícitas.


## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

El método `toString()` en Java es un método definido en la clase base `Object` (de la que todas las clases heredan implícitamente) que devuelve una representación del objeto en forma de cadena de texto (`String`). Su propósito principal es proporcionar una descripción legible y significativa del estado del objeto, útil para depuración, logging o interfaz de usuario. Si una clase no sobrescribe este método, se usa la implementación por defecto de `Object`, que devuelve un string con el nombre de la clase y un código hash, generalmente poco informativo.

Este concepto existe en otros lenguajes con nombres y mecanismos similares. En C#, el método equivalente también se llama `ToString()` y suele sobrescribirse. En Python, el método equivalente es `__str__()` (para una representación legible) y `__repr__()` (para una representación más técnica o de depuración). En C++, la funcionalidad similar se logra típicamente sobrecargando el operador `<<` para flujos de salida.

Un ejemplo de `toString()` en la clase `Punto` sería:

```java
public class Punto {
    double x;
    double y;

    // ... constructores y otros métodos

    @Override
    public String toString() {
        return "Punto [x=" + x + ", y=" + y + "]";
    }
}
```

Al sobrescribir `toString()` con la anotación `@Override`, se garantiza que cuando se utilice un objeto `Punto` en contextos que esperan una cadena (como `System.out.println(miPunto);` o en concatenaciones), se invocará esta versión personalizada, mostrando los valores de `x` e `y`. Esto es mucho más útil que la salida por defecto, ya que muestra directamente el estado interno del objeto.


## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?

Un `struct` en C comparte con una clase en Java la capacidad de agrupar diferentes tipos de datos (variables) bajo un mismo nombre, formando un tipo compuesto. Ambos permiten definir una estructura de datos que puede ser reutilizada para crear múltiples variables (en C) u objetos (en Java). Sin embargo, un `struct` en C es fundamentalmente un **contenedor pasivo de datos**; su función principal es organizar información en memoria, pero carece de comportamiento asociado intrínseco.

Para que un `struct` se asemeje a una clase y sus variables sean verdaderas instancias, le faltarían tres pilares esenciales de la orientación a objetos. En primer lugar, **métodos** (funciones) que estén íntimamente ligados a los datos y definan el comportamiento del tipo. En C, las funciones que operan sobre un `struct` son entidades separadas que reciben un puntero al struct como parámetro explícito, no están encapsuladas dentro del tipo. En segundo lugar, le falta **encapsulamiento**: en un `struct` todos los miembros son accesibles directamente desde cualquier parte del programa, no hay control de visibilidad (como `private` o `public`). En tercer lugar, le faltan los mecanismos de **herencia** y **polimorfismo**, que permiten crear jerarquías de tipos y comportamientos especializados.

En resumen, mientras una clase combina **estado** (atributos) y **comportamiento** (métodos) en una unidad que controla su acceso y puede participar en relaciones de herencia, un `struct` en C es solo una **estructura de datos**. Para emular una clase en C, el programador debe manualmente asociar funciones al struct, gestionar la visibilidad por convención y simular herencia mediante composición o estructuras anidadas, pero sin el soporte directo del lenguaje.


## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

Para emular la clase `Punto` en C usando un `struct`, se define la estructura con los mismos atributos y se crea una función separada que reciba un puntero a esa estructura como parámetro explícito. El `struct` actúa como el contenedor de datos, y la función externa simula el método que opera sobre esos datos.

```c
#include <math.h>

// Definición del "struct" equivalente a la clase
struct Punto {
    double x;
    double y;
};

// Función que simula el método "calculaDistanciaAOrigen"
double Punto_calculaDistanciaAOrigen(struct Punto* self) {
    return sqrt((self->x * self->x) + (self->y * self->y));
}
```

La referencia **`this`** de Java se emula explícitamente mediante un **puntero** al `struct`, convencionalmente llamado `self` o `this`. Este puntero, pasado como primer parámetro a cada función, representa la instancia concreta sobre la que se debe operar. A diferencia de Java, donde `this` es implícito y automático dentro de los métodos, en C el programador debe declararlo y pasarlo manualmente en cada llamada.

Para usar esta emulación, se crea una variable de tipo `struct Punto`, se asignan valores a sus miembros y se llama a la función pasando la dirección del struct. La función accede a los datos a través del puntero `self` usando el operador `->`. Esta aproximación separa claramente los datos del comportamiento, carece de encapsulamiento real (todos los miembros del struct son públicos) y no posee herencia ni polimorfismo integrados, pero demuestra la base conceptual sobre la que se construyen los objetos en lenguajes de más alto nivel.