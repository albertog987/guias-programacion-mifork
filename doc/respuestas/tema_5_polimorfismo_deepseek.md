# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

El **polimorfismo** (del griego "muchas formas") es la capacidad de que un mismo mensaje (o llamada a método) pueda comportarse de manera diferente según el objeto concreto que lo recibe, en tiempo de ejecución. Permite que una variable de un tipo genérico (como una superclase o una interfaz) pueda referenciar objetos de diferentes tipos específicos (subclases), y al invocar un método definido en el tipo genérico, se ejecute la implementación particular del objeto real. Esto es fundamental para construir programas flexibles y extensibles, ya que el código que depende de la superclase no necesita conocer todas las posibles subclases futuras; simplemente confía en que cada objeto sabe cómo responder al mensaje de manera apropiada.

La **sobreescritura** (override) es el mecanismo mediante el cual una subclase proporciona una implementación específica de un método que ya está definido en su superclase. Para que haya sobreescritura, el método en la subclase debe tener el mismo nombre, los mismos parámetros (misma firma) y el mismo tipo de retorno (o un subtipo, en caso de covariantes) que el método de la superclase. En Java, se suele utilizar la anotación `@Override` para indicar explícitamente la intención de sobrescribir, lo que permite al compilador detectar errores (como firma incorrecta). La sobreescritura es el vehículo que hace posible el polimorfismo en tiempo de ejecución: cuando se invoca un método sobrescrito a través de una referencia de la superclase, la JVM decide dinámicamente qué versión ejecutar según el tipo real del objeto. Esto contrasta con la **sobrecarga** (overloading), que es polimorfismo en tiempo de compilación y se basa en diferentes firmas dentro de la misma clase.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

La **ligadura dinámica** (o enlace tardío) es el mecanismo mediante el cual se **determina en tiempo de ejecución, no en tiempo de compilación, qué implementación de un método debe ejecutarse** cuando se realiza una llamada a través de una referencia de un tipo base. Cuando se invoca un método sobrescrito, el compilador no puede saber a ciencia cierta cuál será el tipo real del objeto en tiempo de ejecución, por lo que delega la decisión en la máquina virtual. En cada llamada, el sistema examina el tipo real del objeto (no el tipo de la referencia) y busca la versión más específica del método desde esa clase hacia arriba en la jerarquía. Este mecanismo es el que hace posible el polimorfismo: sin ligadura dinámica, una referencia de superclase siempre ejecutaría los métodos de la superclase, anulando cualquier comportamiento específico de la subclase.

La relación entre ligadura dinámica y polimorfismo es directa: **el polimorfismo en tiempo de ejecución no existiría sin la ligadura dinámica**. Ambos conceptos se refieren al mismo fenómeno visto desde ángulos complementarios: el polimorfismo describe la capacidad de un objeto de adoptar "muchas formas", mientras que la ligadura dinámica describe el mecanismo interno que permite que esa capacidad se realice. Es la JVM quien, al encontrar una instrucción de invocación de método, realiza la búsqueda dinámica en la tabla de métodos virtuales (vtable) del objeto real, localizando el método sobrescrito apropiado. Este proceso es transparente para el programador, que simplemente escribe código que llama a métodos a través de referencias de la superclase, confiando en que el comportamiento correcto se resolverá automáticamente en tiempo de ejecución.

La necesidad de indicar o no la ligadura dinámica varía según el lenguaje. En **Java**, los métodos de instancia (no estáticos, no privados, no final) son **virtuales por defecto**, es decir, siempre usan ligadura dinámica a menos que se declaren explícitamente `final` o `private`. El programador no necesita hacer nada especial para activarla; es el comportamiento normal. En **C++**, por el contrario, los métodos **no son virtuales por defecto** para maximizar el rendimiento. El programador debe declarar explícitamente un método como `virtual` en la superclase para que se active la ligadura dinámica; de lo contrario, el compilador decide qué método ejecutar basándose en el tipo de la referencia (ligadura estática). En **Python**, todos los métodos de instancia usan ligadura dinámica de manera natural, pero con una diferencia fundamental: la resolución se basa en un sistema de búsqueda en el diccionario del objeto y sus clases base (MRO - Method Resolution Order), sin necesidad de declaraciones especiales, aunque con la posibilidad de modificar el comportamiento en tiempo de ejecución de manera más flexible (y menos predecible) que en Java o C++. Así, mientras Java y C++ ofrecen ligadura dinámica controlada por el diseño de clases (con diferente configuración por defecto), Python la ofrece de forma inherente y dinámica, pero con menos garantías de corrección estática.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

```java
// Clase base Soldado
public class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    // Método que será sobrescrito por algunas subclases
    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }

    public String getNombre() {
        return nombre;
    }
}

// Subclase Artillero: NO sobrescribe saludar (usa el de Soldado)
public class Artillero extends Soldado {
    private int numeroCohetes;

    public Artillero(String nombre, int numeroCohetes) {
        super(nombre);
        this.numeroCohetes = numeroCohetes;
    }

    public void dispararCohete() {
        System.out.println(nombre + " dispara un cohete");
        numeroCohetes--;
    }

    // No hay método saludar() aquí - se hereda el de Soldado
}

// Subclase Zapador: SOBRESCRIBE saludar completamente
public class Zapador extends Soldado {
    private int numeroMinas;

    public Zapador(String nombre, int numeroMinas) {
        super(nombre);
        this.numeroMinas = numeroMinas;
    }

    @Override
    public void saludar() {
        // Comportamiento completamente diferente al de Soldado
        System.out.println("¡Cuidado con las minas! Soy " + nombre + ", el zapador");
    }

    public void ponerMina() {
        System.out.println(nombre + " coloca una mina");
        numeroMinas--;
    }
}
```

```java
// Demostración del polimorfismo
public class MainPolimorfismo {
    public static void main(String[] args) {
        // Array de tipo Soldado que contiene objetos de subclases
        Soldado[] ejercito = new Soldado[3];
        ejercito[0] = new Artillero("Sargento Rock", 5);
        ejercito[1] = new Zapador("Cabo Mina", 3);
        ejercito[2] = new Artillero("Teniente Fuego", 2);

        // Recorrido con referencias de tipo Soldado
        for (Soldado s : ejercito) {
            // Ligadura dinámica: se ejecuta la versión apropiada de saludar()
            s.saludar();
        }

        System.out.println("\n--- Salidas esperadas ---");
        System.out.println("Artillero: 'Soy el soldado Sargento Rock' (heredado de Soldado)");
        System.out.println("Zapador: '¡Cuidado con las minas! Soy Cabo Mina, el zapador' (sobrescrito)");
        System.out.println("Artillero: 'Soy el soldado Teniente Fuego' (heredado)");
    }
}
```

En este ejemplo, el método `saludar()` se comporta de manera diferente según el tipo real del objeto en tiempo de ejecución, aunque la referencia sea de tipo `Soldado`. El `Artillero` no sobrescribe el método, por lo que utiliza la implementación heredada de `Soldado`. El `Zapador`, en cambio, sobrescribe `saludar()` con su propia implementación, cambiando completamente el mensaje mostrado. El bucle `for` es idéntico para todos los elementos, pero gracias al polimorfismo y a la ligadura dinámica, cada objeto "sabe" cómo saludar correctamente. Esta es la esencia del polimorfismo: **código uniforme que produce comportamientos específicos según el tipo real del objeto**, permitiendo que el programa sea extensible sin modificar el código que procesa la colección base. Si en el futuro se añade una nueva clase `Francotirador` que también sobrescriba `saludar()`, el bucle seguirá funcionando sin cambios, demostrando la potencia del polimorfismo para construir sistemas flexibles y abiertos a la extensión.


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

Sí, es completamente posible invocar el método de la clase base dentro de una sobrescritura para **extender su comportamiento** (en lugar de reemplazarlo por completo). Esta técnica permite aprovechar la implementación existente y añadir funcionalidad adicional. La palabra clave utilizada en Java para invocar al método de la superclase es **`super`**, seguida del nombre del método y los parámetros correspondientes. Esto es válido tanto para métodos como para constructores, aunque en estos últimos `super()` tiene reglas adicionales de posicionamiento.

```java
public class Zapador extends Soldado {
    private int numeroMinas;

    public Zapador(String nombre, int numeroMinas) {
        super(nombre);
        this.numeroMinas = numeroMinas;
    }

    @Override
    public void saludar() {
        // Primero se invoca al método saludar() de la superclase Soldado
        super.saludar();  // Usa la palabra clave 'super'
        // Luego se añade el comportamiento específico del Zapador
        System.out.println("¡ZAPADOR A SUS ORDENES!");
    }

    public void ponerMina() {
        System.out.println(nombre + " coloca una mina");
        numeroMinas--;
    }
}
```

```java
// Demostración
public class Main {
    public static void main(String[] args) {
        Soldado zapador = new Zapador("Cabo Mina", 3);
        zapador.saludar();
    }
}

// Salida:
// Soy el soldado Cabo Mina
// ¡ZAPADOR A SUS ORDENES!
```

En este ejemplo, `super.saludar()` invoca la implementación original de `Soldado`, que imprime "Soy el soldado...". Después, la subclase `Zapador` añade su propio mensaje específico. La palabra clave `super` es similar a `this`, pero se refiere a la parte de la superclase del objeto actual, permitiendo acceder a miembros que de otra forma estarían ocultos por la sobrescritura. Es importante destacar que `super` puede usarse no solo para métodos, sino también para acceder a atributos de la superclase (aunque esto no es común, ya que los atributos suelen ser `private`). El uso de `super` para extender comportamientos respeta el principio de **no repetirse** (DRY - Don't Repeat Yourself), ya que se reutiliza el código base en lugar de duplicarlo. Esta técnica es muy útil cuando la subclase quiere especializar el comportamiento sin perder la funcionalidad fundamental provista por la superclase, como preparar precondiciones, realizar validaciones adicionales o añadir post-procesamiento.


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

Al sobreescribir un método en Java, el método en la subclase debe tener **exactamente la misma firma** que el método de la superclase: mismo nombre, misma lista de tipos de parámetros (y en el mismo orden), y el tipo de retorno debe ser el mismo o un **subtipo covariante** (un tipo más específico). Por ejemplo, si la superclase devuelve `Soldado`, la subclase puede devolver `Artillero` (subclase de `Soldado`). Además, el modificador de acceso no puede ser más restrictivo que el de la superclase (un método `protected` no puede volverse `private`), pero sí puede ser menos restrictivo (de `protected` a `public`). No se pueden cambiar las excepciones declaradas: se pueden lanzar menos excepciones o subtipos, pero no excepciones nuevas o más generales (en el caso de excepciones controladas).

La diferencia fundamental entre **sobreescritura** (*overriding*) y **sobrecarga** (*overloading*) es que la sobreescritura ocurre en la **jerarquía de herencia** (entre superclase y subclase) y redefine completamente el comportamiento de un método existente, mientras que la sobrecarga ocurre **dentro de la misma clase** (o entre clases relacionadas por herencia, aunque con firma diferente) y define múltiples métodos con el mismo nombre pero diferentes parámetros. La sobreescritura se resuelve en tiempo de ejecución (ligadura dinámica) y es la base del polimorfismo; la sobrecarga se resuelve en tiempo de compilación (ligadura estática) basándose en los tipos de los argumentos. Por ejemplo, `void saludar()` y `void saludar(String mensaje)` son sobrecarga; `void saludar()` en `Zapador` que redefine `void saludar()` de `Soldado` es sobreescritura.

La anotación **`@Override`** es opcional pero **altamente recomendable**. Sirve para indicar explícitamente al compilador que se pretende sobrescribir un método de la superclase. Si se usa y el método no sobrescribe realmente ningún método (por ejemplo, porque hay un error tipográfico en el nombre, los parámetros no coinciden exactamente, o el método es `static`/`final`/`private`), el compilador genera un error de compilación. Esta verificación temprana evita errores sutiles donde el programador cree que está sobrescribiendo pero en realidad está definiendo un método nuevo con una firma ligeramente diferente (como `saludar()` vs `saludar(String s)`). Además, `@Override` documenta la intención del programador y mejora la legibilidad del código, dejando claro que el método es parte de un contrato de herencia. Las herramientas de desarrollo modernas usan esta anotación para refactorizaciones seguras (como renombrar un método en la superclase y actualizar automáticamente todas sus sobrescrituras). Por todas estas razones, es una buena práctica incluir `@Override` siempre que se sobrescriba un método en Java.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

Sí, absolutamente. Cuando se sobrescribe `toString()` o `equals()` en Java, ya se está utilizando **polimorfismo**, aunque a menudo no se nombre explícitamente. Estos métodos están definidos en la clase base `Object`, de la que todas las clases heredan directa o indirectamente. Al proporcionar una implementación específica en una clase propia (como `Estudiante` o `Punto`), se está sobrescribiendo el comportamiento heredado. Cuando luego se pasa un objeto de esa clase a `System.out.println()`, que internamente llama a `toString()`, se produce polimorfismo: el método que se ejecuta es el de la clase concreta (el sobrescrito), no el de `Object`. Esto es ligadura dinámica en acción, incluso sin que el programador haya escrito explícitamente jerarquías complejas.

De esta manera, el polimorfismo es una característica fundamental de Java que se usa desde los primeros programas, aunque de forma implícita. `System.out.println(obj)` no necesita saber el tipo exacto de `obj`; simplemente confía en que cualquier objeto sabe cómo representarse como `String` a través del método `toString()`, que puede estar sobrescrito o no. De igual forma, `ArrayList.remove(obj)` utiliza `equals()` para encontrar el objeto, y si se sobrescribe `equals()` (junto con `hashCode()`), se está personalizando el comportamiento polimórfico de búsqueda de igualdad. Incluso si no se sobrescribe ningún método, el polimorfismo sigue presente a través de los métodos heredados de `Object`.

Por tanto, el polimorfismo no es un concepto avanzado que se introduce tardíamente; está presente desde el inicio del aprendizaje de Java, aunque se haga consciente más adelante. Entender que `toString()` sobrescrito produce comportamientos diferentes en tiempo de ejecución según la clase concreta del objeto es comprender la esencia del polimorfismo. Esta característica está tan integrada en el lenguaje que los programadores noveles la usan sin pensar, asumiendo que "cada objeto sabe cómo imprimirse a sí mismo". Reconocer que esto es polimorfismo ayuda a apreciar la potencia del lenguaje y a aplicar conscientemente el mismo principio para diseñar jerarquías extensibles y fácilmente mantenibles. Por tanto, responder a la pregunta: sí, desde el primer "Hola Mundo" con clases propias que sobrescriben `toString()` ya se está empleando polimorfismo en Java.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

Una **clase abstracta** es una clase que no puede ser instanciada, es decir, no se pueden crear objetos directamente de ella con `new`. Su propósito es servir como plantilla o esqueleto para otras clases (subclases concretas). Puede contener tanto métodos concretos (con implementación) como **métodos abstractos** (sin implementación). Una **clase abstracta** se declara con la palabra clave `abstract` antes de `class`. Una **clase abstracta** puede tener constructores (que serán llamados por las subclases), atributos, métodos concretos y métodos abstractos. Establece un "contrato" parcial: define qué deben hacer las subclases pero deja algunos detalles para que ellas los implementen específicamente.

Un **método abstracto** es un método declarado sin cuerpo (sin llaves ni implementación), solo con la firma: modificadores, tipo de retorno, nombre y parámetros. Se declara con la palabra clave `abstract` y termina con punto y coma (`;`). Los métodos abstractos **solo pueden existir dentro de clases abstractas**. Cuando una clase concreta (no abstracta) extiende una clase abstracta, **está obligada a implementar todos los métodos abstractos heredados**, a menos que ella misma sea abstracta. Esta obligación garantiza que todas las subclases concretas tengan un comportamiento definido para esos métodos, estableciendo una especie de "protocolo" que deben cumplir.

**No, no se pueden crear instancias de una clase abstracta**. El compilador de Java impide explícitamente escribir `new Soldado()`. Intentarlo produce un error de compilación. Una clase abstracta es incompleta porque tiene al menos un método sin implementación (o porque se ha declarado abstracta aunque no tenga métodos abstractos). Solo las subclases concretas que implementen todos los métodos abstractos pueden ser instanciadas.

```java
// Clase abstracta Soldado
public abstract class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    // Método concreto (ya implementado)
    public void saludar() {
        System.out.println("Soy el soldado " + nombre);
    }

    // Método abstracto: cada soldado concreto debe definir su forma de atacar
    public abstract void atacar();

    public String getNombre() {
        return nombre;
    }
}

// Subclase concreta Artillero
public class Artillero extends Soldado {
    private int numeroCohetes;

    public Artillero(String nombre, int numeroCohetes) {
        super(nombre);
        this.numeroCohetes = numeroCohetes;
    }

    @Override
    public void atacar() {
        System.out.println(nombre + " ataca con un cohete ¡BOOM!");
        numeroCohetes--;
    }
}

// Subclase concreta Zapador
public class Zapador extends Soldado {
    private int numeroMinas;

    public Zapador(String nombre, int numeroMinas) {
        super(nombre);
        this.numeroMinas = numeroMinas;
    }

    @Override
    public void atacar() {
        System.out.println(nombre + " coloca una mina enemiga");
        numeroMinas--;
    }
}
```

```java
// Demostración
public class Main {
    public static void main(String[] args) {
        // Soldado s = new Soldado("Juan"); // ERROR: Soldado es abstracto

        Soldado[] ejercito = new Soldado[2];
        ejercito[0] = new Artillero("Sargento Rock", 5);
        ejercito[1] = new Zapador("Cabo Mina", 3);

        for (Soldado s : ejercito) {
            s.saludar();   // Método concreto heredado
            s.atacar();    // Método abstracto implementado por cada subclase
            System.out.println();
        }
    }
}
```

En este ejemplo, la clase `Soldado` es abstracta porque contiene el método `atacar()` abstracto. Esto fuerza a que `Artillero` y `Zapador` proporcionen su propia implementación de `atacar()`. El modificador `abstract` debe colocarse en la declaración de la clase (`public abstract class Soldado`) y en la declaración del método (`public abstract void atacar();`), sin cuerpo. Las subclases concretas utilizan `@Override` para implementar el método abstracto (aunque técnicamente `@Override` es opcional, es muy recomendable). Este diseño garantiza que cualquier objeto `Soldado` (aunque sea mediante una referencia de tipo `Soldado`) sabe que puede responder a `atacar()`, y la ligadura dinámica se encargará de ejecutar la versión apropiada según el tipo real. Las clases abstractas son ideales cuando se tiene una jerarquía donde algunas funcionalidades son comunes (como `saludar()`) pero otras son específicas y deben ser definidas por cada subclase (como `atacar()`), evitando así la duplicación de código y garantizando que todas las subclases cumplan con un contrato mínimo.


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

La palabra clave **`final`** en Java tiene diferentes efectos según dónde se aplique. Cuando se aplica a un **método**, impide que ese método sea **sobrescrito** por las subclases. Cualquier intento de sobrescribir un método `final` en una subclass produce un error de compilación. Cuando se aplica a una **clase**, impide que esa clase sea **extendida**; es decir, ninguna clase puede heredar de ella (declararla como `final class` la convierte en una "hoja" en la jerarquía). Una clase `final` puede tener métodos `final` o no, pero todos sus métodos son implícitamente no extensibles en el sentido de que no pueden ser sobrescritos en subclases porque no puede haber subclases. `final` también se aplica a variables (para hacerlas constantes) pero ese es otro contexto.

La relación de `final` con el **polimorfismo** es restrictiva: elimina la posibilidad de polimorfismo en el punto donde se aplica. Al marcar un método como `final`, se fuerza a que todas las llamadas a ese método a través de referencias de cualquier tipo se resuelvan con la implementación definida en esa clase exacta, sin posibilidad de que una subclase proporcione un comportamiento especializado. Esto puede ser útil por razones de **seguridad** (evitar que subclases maliciosas alteren comportamientos críticos), **rendimiento** (los métodos `final` pueden ser optimizados por la JVM mediante inlining, aunque hoy en día las JVM modernas pueden optimizar incluso métodos no finales si no hay sobrescritura en la práctica), o **diseño** (cuando un método es parte de un algoritmo que no debe ser alterado). Una clase `final`, al no poder ser extendida, impide totalmente el polimorfismo basado en herencia desde esa clase, forzando a que si se necesita variabilidad se use composición en su lugar.

Un ejemplo clásico de clase `final` en la API estándar de Java es **`java.lang.String`**. La clase `String` es `final` por razones de seguridad, rendimiento y corrección de invariantes. Al ser `final`, se garantiza que cualquier objeto `String` es inmutable (no puede ser alterado por una subclase maliciosa que introduzca mutabilidad). Esto permite que las instancias de `String` sean compartidas de forma segura (por ejemplo, mediante el `String Pool`) y que sean usadas como claves en `HashMap` con la certeza de que su `hashCode` no cambiará. Otros ejemplos incluyen clases como `Integer`, `Double`, `Math`, `System`, `Scanner` (entre muchas otras). El diseño de estas clases como `final` refuerza el principio de que ciertos tipos fundamentales deben tener un comportamiento fijo y no deben ser especializados por los usuarios, previniendo errores sutiles y manteniendo la integridad del sistema. Así, `final` actúa como una herramienta de diseño para sellar jerarquías y proporcionar garantías fuertes en la API.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta
