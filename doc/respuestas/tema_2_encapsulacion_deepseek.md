# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

La **encapsulación** busca agrupar en una unidad (la clase) tanto los datos (atributos) como las operaciones (métodos) que actúan sobre esos datos. La **ocultación de información** (o *information hiding*) es un principio complementario que restringe el acceso directo a los detalles internos de esa unidad, exponiendo solo una interfaz pública controlada. Juntos, estos conceptos permiten que un objeto gestione su estado interno y proteja su integridad, determinando qué partes son visibles y modificables desde el exterior y cuáles son privadas.

Entre las ventajas clave de la ocultación de información se encuentran: en primer lugar, **aumentar la robustez y fiabilidad** del software, ya que el estado interno solo puede modificarse a través de métodos validados, previniendo estados inconsistentes o corruptos. En segundo lugar, **facilita el mantenimiento y la evolución** del código, porque los cambios en la implementación interna de una clase (cómo almacena los datos o realiza los cálculos) no afectan al código externo que utiliza su interfaz pública, siempre que esta interfaz se mantenga estable.

Otras ventajas importantes incluyen la **reducción de la complejidad** para el programador que usa la clase, quien solo necesita entender la interfaz pública y no los detalles internos, y la **mejora de la modularidad y reutilización**, ya que las clases bien encapsuladas son unidades autocontenidas y desacopladas. Finalmente, la ocultación de información es fundamental para la **seguridad**, al evitar que código no autorizado manipule directamente datos sensibles o críticos.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

Se entiende por **interfaz pública** de un objeto o clase al conjunto de métodos y atributos que son accesibles desde fuera de la misma, es decir, desde otras clases o partes del programa. Esta interfaz define cómo otros objetos pueden interactuar con una instancia de la clase: qué operaciones se pueden solicitar (métodos públicos) y, en algunos casos, qué datos de solo lectura se pueden consultar (normalmente a través de métodos *getter*). Constituye el contrato o la fachada que la clase ofrece al mundo exterior, especificando *qué* puede hacerse, pero no necesariamente *cómo* se implementa internamente.

La interfaz pública se relaciona directamente con la ocultación de información al actuar como el único punto de acceso controlado a las capacidades del objeto. Mientras la interfaz pública revela la funcionalidad esencial, todos los detalles de implementación —los atributos privados, los métodos auxiliares internos y la lógica compleja— permanecen ocultos y protegidos detrás de ella. Este desacople permite modificar la implementación interna de la clase sin afectar a los clientes que dependen únicamente de su interfaz pública, siempre que esta no cambie.

Esta separación entre el "qué" (interfaz) y el "cómo" (implementación) es el núcleo de la ocultación de información. La interfaz pública actúa como una barrera de abstracción que reduce la complejidad percibida por el usuario de la clase y previene dependencias no deseadas en los detalles internos, promoviendo así un diseño más flexible, mantenible y robusto.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

Hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase porque actúa como un **contrato** estable y permanente con todo el código que la utilice. Una interfaz pública bien definida y minimalista reduce el acoplamiento entre clases, facilita la comprensión del sistema y guía una implementación interna coherente. Un diseño descuidado puede resultar en una interfaz confusa, excesivamente amplia o que exponga detalles de implementación, lo que dificulta el uso correcto, promueve errores y hace que el sistema sea frágil ante cambios futuros.

No es fácil cambiar la interfaz pública una vez que la clase está siendo utilizada por otras partes del código, especialmente en sistemas grandes o por bibliotecas distribuidas. Cualquier modificación en la firma de un método público (cambiar su nombre, parámetros o tipo de retorno) o la eliminación de un miembro público, obliga a actualizar **todas** las llamadas a ese miembro en el código cliente. Esto puede requerir un esfuerzo de mantenimiento significativo y conlleva un alto riesgo de introducir errores.

Por ello, el diseño de la interfaz pública debe ser el resultado de una cuidadosa reflexión sobre las responsabilidades esenciales de la clase, anticipando sus usos más comunes. Técnicas como el principio de la menor sorpresa, el diseño por contrato y el uso de interfaces abstractas pueden ayudar a crear interfaces robustas y estables desde el inicio, minimizando la necesidad de cambios disruptivos en el futuro.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las **invariantes de clase** son condiciones o restricciones lógicas sobre el estado interno de un objeto que deben ser verdaderas en todo momento de la vida del objeto, excepto, transitoriamente, durante la ejecución de sus propios métodos. Representan las reglas de consistencia que definen qué combinaciones de valores de atributos son válidas para que el objeto tenga sentido dentro del dominio del problema. Por ejemplo, en una clase `CuentaBancaria`, una invariante podría ser que el atributo `saldo` nunca sea menor que un `limiteDescubierto` permitido.

La ocultación de información es fundamental para garantizar el mantenimiento de las invariantes de clase. Al hacer que los atributos sean privados (o protegidos) y solo permitir su modificación a través de métodos públicos (como *setters* o métodos de negocio), la clase centraliza y controla todas las operaciones que pueden alterar su estado. Dentro de estos métodos, se puede incluir lógica de validación para asegurar que cualquier cambio mantenga las invariantes. Sin ocultación, el acceso directo a los atributos desde código externo permitiría modificaciones arbitrarias, imposibilitando el control y violando potencialmente las invariantes.

De esta manera, la ocultación de información actúa como un **mecanismo de defensa** que delega la responsabilidad de mantener la integridad del objeto en la propia clase. Esto no solo previene estados inconsistentes, sino que también simplifica el razonamiento sobre el correcto funcionamiento del sistema, ya que para verificar las invariantes solo es necesario analizar el código de la propia clase y no todo el programa que podría interactuar con ella.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

```java
public class Punto {
    // Atributos privados: ocultación de información
    private double x;
    private double y;

    // Constructor público: parte de la interfaz
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Método público: parte de la interfaz
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }

    // Métodos públicos getter: también parte de la interfaz para consulta controlada
    public double getX() {
        return x;
    }

    public double getY() {
        return y;
    }

    // Métodos públicos setter con posible validación: parte de la interfaz para modificación controlada
    public void setX(double x) {
        this.x = x;
    }

    public void setY(double y) {
        this.y = y;
    }
}
```

La **interfaz pública** de la clase `Punto` está compuesta por todos sus miembros declarados con `public`: el constructor `Punto(double, double)`, los métodos `calcularDistanciaAOrigen()`, `getX()`, `getY()`, `setX(double)` y `setY(double)`. Esta es la colección de operaciones que otros objetos pueden utilizar para crear instancias de `Punto`, consultar sus coordenadas, modificarlas y calcular la distancia al origen. Los atributos `x` e `y` no forman parte de la interfaz pública, ya que son `private`.

Los modificadores de acceso **`public`** y **`private`** son mecanismos directos para implementar la ocultación de información. `Public` significa que el miembro (atributo, método o constructor) es accesible desde cualquier otra clase en cualquier paquete. `Private` significa que el miembro solo es accesible dentro de la propia clase donde se declara, siendo completamente invisible e inaccesible desde el exterior. En este ejemplo, al declarar `x` e `y` como `private`, se impide que otras clases los modifiquen o lean directamente, forzando el uso de los métodos públicos `get` y `set` si se desea interactuar con ellos, lo que da a la clase un control total sobre su estado interno y protege posibles invariantes.


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores de acceso `public` y `private` se pueden aplicar a **miembros de una clase** y a las **propias clases** (aunque con restricciones). Los miembros incluyen: **atributos** (variables de instancia o de clase), **métodos** (de instancia o estáticos) y **constructores**. Al aplicar estos modificadores, se controla desde qué otras partes del código se puede acceder a dichos miembros.

Cuando se aplica a una **clase**, el modificador `public` significa que la clase es visible desde cualquier otro paquete. Sin embargo, una clase solo puede ser declarada `public` si su archivo `.java` tiene exactamente el mismo nombre que la clase. El modificador `private` **no se puede aplicar directamente a una clase de nivel superior** (top-level class); solo es válido para **clases anidadas** (nested classes), donde una clase interna puede ser declarada `private` para que solo sea accesible dentro de la clase que la contiene.

Además, estos modificadores definen distintos niveles de encapsulación. Un miembro `public` es accesible universalmente, mientras que un miembro `private` solo es accesible dentro del cuerpo de la misma clase donde se declara, siendo la forma más restrictiva de ocultación de información. Existen también los modificadores `protected` y el acceso por defecto (package-private), que ofrecen niveles intermedios de visibilidad. La elección correcta del modificador es fundamental para definir una interfaz pública clara y proteger la integridad del estado interno del objeto.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En POO, además de la visibilidad pública y privada, suelen existir niveles intermedios de acceso. En **Java**, existen cuatro modificadores de visibilidad definidos: `public` (accesible desde cualquier lugar), `private` (solo desde la misma clase), **`protected`** (accesible desde la misma clase, subclases y otras clases del mismo paquete) y el **acceso por defecto** (también llamado *package-private*), que se aplica cuando no se escribe ningún modificador y permite el acceso solo desde clases del mismo paquete. Estos niveles ofrecen un control granular para implementar encapsulación y herencia de manera efectiva.

En otros lenguajes orientados a objetos, los conceptos son análogos pero con implementaciones distintas. En **C++**, existen `public`, `private` y `protected`, con semántica similar a Java, aunque sin el concepto de *package*; el acceso `protected` permite el acceso desde subclases, pero no desde otras clases del mismo "paquete". En **C#**, además de `public`, `private` y `protected`, existe `internal` (accesible dentro del mismo ensamblado, análogo al *package-private* de Java) y combinaciones como `protected internal`. **Python** adopta un enfoque diferente: no tiene modificadores estrictos, sino convenciones; un atributo o método precedido por un guion bajo (`_nombre`) se considera *protegido* (por convención), y con dos guiones bajos (`__nombre`) se produce un *name mangling* que simula un comportamiento similar a privado, pero no es totalmente inaccesible.

La existencia de estos múltiples niveles responde a la necesidad de equilibrar la flexibilidad en el diseño de jerarquías de herencia y la organización modular, con la rigurosidad en la ocultación de información. Mientras `private` asegura la máxima encapsulación, `protected` y los accesos de paquete o ensamblado permiten compartir detalles internos de manera controlada entre componentes estrechamente relacionados, facilitando la extensibilidad sin romper la encapsulación a nivel global.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

Los miembros de instancia privados de un objeto **no están ocultos para otras instancias de la misma clase**. En Java, el modificador `private` restringe el acceso al nivel de **clase**, no al nivel de **objeto**. Esto significa que cualquier método de una clase puede acceder directamente a los atributos `private` de cualquier otro objeto de esa misma clase, sin necesidad de usar métodos getters. Solo están ocultos para otras clases distintas.

Un ejemplo con el método `calcularDistanciaAPunto` lo demuestra:

```java
public class Punto {
    private double x;
    private double y;

    // ... constructor y otros métodos

    public double calcularDistanciaAPunto(Punto otro) {
        // Acceso directo a los atributos privados 'x' e 'y' de 'otro' (otra instancia)
        double diferenciaX = this.x - otro.x; // 'otro.x' es accesible aquí
        double diferenciaY = this.y - otro.y; // 'otro.y' es accesible aquí
        return Math.sqrt(diferenciaX * diferenciaX + diferenciaY * diferenciaY);
    }
}
```

En el método `calcularDistanciaAPunto`, el objeto actual (`this`) accede directamente a los atributos privados `x` e `y` del parámetro `otro`, que es otra instancia de la clase `Punto`. Esto es perfectamente válido en Java porque el acceso `private` se define por pertenencia a la misma clase, no por pertenencia al mismo objeto. Por lo tanto, la respuesta correcta es que los miembros privados están ocultos para **otras clases** (a), pero **no** para otras instancias de la misma clase (b). Este diseño facilita la implementación de operaciones que necesitan comparar o manipular el estado interno de objetos del mismo tipo.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos **"getter"** y **"setter"** (también conocidos como *accessors* y *mutators*) son métodos públicos en una clase cuyo propósito es proporcionar acceso controlado a los atributos privados de un objeto. Un **getter** es un método que retorna el valor de un atributo privado, típicamente con un nombre como `getAtributo()`. Un **setter** es un método que permite modificar el valor de un atributo privado, usualmente con un nombre como `setAtributo(valor)`, y a menudo incluye validaciones o lógica adicional antes de realizar la asignación.

Su principal función es mantener el principio de **encapsulación** y **ocultación de información**. Al hacer que los atributos sean privados y solo accesibles a través de estos métodos, la clase retiene el control total sobre cómo se lee y se modifica su estado interno. Por ejemplo, un setter puede validar que el nuevo valor sea válido (como un saldo no negativo) antes de asignarlo, o puede actualizar otros atributos dependientes. Un getter puede computar o formatear el valor antes de devolverlo, o incluso devolver una copia defensiva de una estructura de datos mutable para preservar la inmutabilidad del objeto interno.

Aunque en casos simples pueden parecer redundantes, ya que aparentemente solo exponen lo que ocultan, son fundamentales para un diseño robusto. Permiten cambiar la implementación interna de la clase (por ejemplo, almacenar una fecha como timestamp en lugar de tres enteros) sin afectar el código cliente, que sigue usando los mismos métodos `getDia()`, `getMes()`, etc. Además, facilitan la depuración, ya que se pueden añadir puntos de interrupción o logging en estos métodos para rastrear cuándo y cómo se accede o modifica el estado del objeto.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No, cuando se habla de que la ocultación de información mejora la "seguridad" en el contexto de la programación orientada a objetos, generalmente **no se refiere a seguridad frente a ataques externos o hacking** (como inyecciones de código o acceso no autorizado a sistemas). En este ámbito, la "seguridad" se entiende principalmente como **integridad y robustez del propio programa software**, es decir, la protección contra errores de programación, inconsistencias de estado y comportamientos inesperados que pueden surgir durante la ejecución legítima del programa.

La mejora de seguridad se logra al prevenir que el código cliente (otras partes del programa) modifique directamente el estado interno de un objeto de maneras no previstas o inválidas. Al forzar todas las interacciones a través de una interfaz pública controlada (como setters con validación), se asegura que el objeto nunca entre en un estado que viole sus invariantes de clase. Por ejemplo, un objeto `CuentaBancaria` con un setter privado para `saldo` y un método público `retirar(double cantidad)` que valida fondos, evita que otra parte del programa pueda asignar directamente un saldo negativo y corromper la lógica del negocio.

Por supuesto, una buena encapsulación puede indirectamente contribuir a la seguridad contra ciertas vulnerabilidades, como la corrupción de memoria en lenguajes de bajo nivel o la manipulación inapropiada de datos sensibles, al centralizar y auditar los puntos de acceso. Sin embargo, la protección contra hackers implica medidas adicionales a nivel de sistema, red, criptografía y validación de entradas externas, que van más allá del ámbito de la ocultación de información en el diseño de clases.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

La diferencia principal radica en la pertenencia y la memoria. Un **miembro de instancia** (atributo o método no estático) pertenece a cada **objeto individual** creado a partir de la clase. Cada instancia tiene su propia copia de los atributos de instancia, y los métodos de instancia operan sobre el estado específico de ese objeto (accediendo a él mediante `this`). En cambio, un **miembro de clase** (declarado con `static`) pertenece a la **clase en sí misma**, no a ningún objeto particular. Existe una única copia en memoria, compartida por todas las instancias de la clase, y se puede acceder a él directamente usando el nombre de la clase.

Sí, los **miembros de clase también se pueden y deben ocultar** mediante los mismos modificadores de acceso (`private`, `protected`, etc.). Un atributo `static` declarado como `private` solo será accesible dentro de los métodos de la misma clase (estáticos o de instancia), protegiendo así datos compartidos globales de modificaciones arbitrarias. Por ejemplo, un contador estático del número total de instancias creadas (`private static int contador`) debe ser privado para que solo se modifique de manera controlada dentro de los constructores de la clase.

La ocultación de miembros estáticos es igualmente importante para mantener la encapsulación a nivel de clase. Los métodos estáticos públicos suelen actuar como interfaz para operar sobre estos datos privados estáticos o para ofrecer utilidades. La decisión de hacer un miembro estático privado evita que otras clases dependan de detalles de implementación interna compartidos, facilitando cambios futuros y preservando la integridad del estado compartido entre todas las instancias.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene sentido en escenarios específicos donde se desea **controlar estrictamente la creación de instancias** de una clase. Un constructor privado impide que otras clases instancien el objeto directamente usando `new` desde fuera, pero sigue siendo accesible desde dentro de la propia clase. Esto es la base de patrones de diseño como el **Singleton** (donde solo puede existir una única instancia global, creada y controlada por la clase misma) o el **Factory Method** (donde la creación de objetos se delega a un método estático público de la misma clase, que puede decidir qué subclase concreta instanciar).

También es útil para clases que solo agrupan **métodos y constantes estáticas** (clases de utilidad, como `Math` en Java), donde no tiene sentido crear instancias. Al hacer el constructor privado, se previene la instanciación accidental y se comunica claramente la intención de que la clase es solo un contenedor de funcionalidad estática. Además, se puede usar en clases donde las instancias solo se crean a través de métodos de fábrica estáticos que realizan validaciones complejas, caching de objetos o devuelven instancias preexistentes, ofreciendo mayor flexibilidad y control sobre el ciclo de vida de los objetos que un constructor público.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

Los **miembros de clase** en Java se indican con la palabra clave **`static`**. Se antepone a la declaración de un atributo o método para denotar que pertenece a la clase y no a instancias individuales.

En la clase `Punto`, para almacenar los valores máximos de `x` e `y` establecidos en todas las instancias, se pueden declarar dos atributos estáticos privados. Luego, en los constructores y setters, se actualizan estos máximos cada vez que se asigne un nuevo valor. Se proporcionan también getters estáticos públicos para consultarlos.

```java
public class Punto {
    private double x;
    private double y;
    
    // Miembros de clase (estáticos) para almacenar los máximos
    private static double maxX = Double.MIN_VALUE;
    private static double maxY = Double.MIN_VALUE;
    
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        actualizarMaximos(x, y);
    }
    
    private static void actualizarMaximos(double x, double y) {
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }
    
    // Setters que también actualizan los máximos
    public void setX(double x) {
        this.x = x;
        actualizarMaximos(x, this.y);
    }
    
    public void setY(double y) {
        this.y = y;
        actualizarMaximos(this.x, y);
    }
    
    // Getters estáticos públicos para los máximos (miembros de clase)
    public static double getMaxX() {
        return maxX;
    }
    
    public static double getMaxY() {
        return maxY;
    }
    
    // ... otros métodos (getters de instancia, calcularDistanciaAOrigen, etc.)
}
```

Los atributos `maxX` y `maxY` son `static`, por lo que existe una única copia compartida por todas las instancias de `Punto`. El método `actualizarMaximos` también es `static` (y `private`) porque solo opera con los miembros estáticos. Los métodos `getMaxX()` y `getMaxY()` son `static` y públicos, formando parte de la interfaz a nivel de clase, y se invocan como `Punto.getMaxX()`, sin necesidad de una instancia. Este diseño encapsula y oculta los contadores máximos, exponiéndolos de manera controlada.


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

Sí, se usaría `static` porque un método factoría es típicamente un método de clase que proporciona una forma alternativa de crear instancias sin depender únicamente del constructor.

```java
public static Punto crearPuntoRedondeado(double x, double y) {
    long xRedondeado = Math.round(x); // Math.round devuelve long
    long yRedondeado = Math.round(y);
    return new Punto((double) xRedondeado, (double) yRedondeado);
}
```

El método es `static`, pertenece a la clase `Punto` y puede ser invocado como `Punto.crearPuntoRedondeado(3.7, 4.2)`. Internamente, utiliza `Math.round()` para redondear cada coordenada al entero más cercano (devuelve `long`) y luego crea y devuelve una nueva instancia de `Punto` con esos valores convertidos a `double`. Este enfoque encapsula la lógica de redondeo en un punto único de creación, manteniendo los constructores normales disponibles para otros usos.


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

```java
public class Punto {
    // Atributo privado: array interno de dos posiciones
    private double[] coordenadas = new double[2]; // índice 0: x, índice 1: y

    // Constructor público: interfaz se mantiene (double x, double y)
    public Punto(double x, double y) {
        this.coordenadas[0] = x;
        this.coordenadas[1] = y;
    }

    // Getters: interfaz se mantiene
    public double getX() {
        return coordenadas[0];
    }

    public double getY() {
        return coordenadas[1];
    }

    // Setters: interfaz se mantiene
    public void setX(double x) {
        this.coordenadas[0] = x;
    }

    public void setY(double y) {
        this.coordenadas[1] = y;
    }

    // Método calcularDistanciaAOrigen: interfaz y funcionalidad se mantienen
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(coordenadas[0] * coordenadas[0] + coordenadas[1] * coordenadas[1]);
    }
}
```

La implementación interna cambia radicalmente al usar un array privado `coordenadas` para almacenar `x` e `y`, pero la **interfaz pública se mantiene exactamente igual**. Los constructores, getters, setters y otros métodos públicos conservan sus firmas originales (`double x, double y` o sin parámetros), de modo que cualquier código que ya use la clase `Punto` no necesita modificarse ni es consciente del cambio. La encapsulación logra su objetivo: se puede alterar completamente la representación interna (incluso cambiando de dos variables independientes a un array, a un objeto `List`, etc.) sin impactar a los clientes de la clase, siempre que la interfaz pública sea estable.


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

No es mejor declararlo público, aunque superficialmente parezca equivalente. La diferencia crucial radica en el **control** que la clase mantiene sobre el acceso a su estado. Un atributo público permite lectura y escritura directa e incondicional desde cualquier parte del código, mientras que un par getter/setter, aunque expongan el atributo, centralizan el acceso en métodos. Esto permite, ahora o en el futuro, añadir validaciones, lógica de notificación, sincronización en hilos, cálculo derivado o cambiar la representación interna sin alterar la interfaz. Un atributo público fija permanentemente la implementación y renuncia a cualquier control.

La convención más habitual y considerada una **buena práctica** en Java y en la POO es declarar **todos los atributos como privados** (o, en casos muy específicos, protegidos). Los atributos públicos son excepcionales y generalmente se limitan a constantes estáticas finales (`public static final`). Esta convención es un reflejo directo del principio de **ocultación de información**: se oculta la representación interna y se expone una interfaz controlada.

Esto tiene una relación directa y esencial con las **invariantes de clase**. Los setters públicos son el lugar idóneo para incluir validaciones que aseguren que cualquier nuevo valor asignado mantenga las invariantes del objeto. Si el atributo fuera público, cualquier fragmento de código externo podría asignar un valor inválido, corrompiendo el estado del objeto y violando sus reglas de consistencia. Por tanto, los getters/setters no son solo una convención de nomenclatura, sino un **mecanismo de defensa** para preservar la integridad del objeto y hacer cumplir sus invariantes.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase **inmutable** es aquella cuyas instancias, una vez creadas, no pueden modificar su estado interno. Todos sus atributos suelen ser declarados `private` y `final`, y no se proporcionan métodos que permitan cambiar su valor después de la construcción. Si se necesita un objeto con un estado diferente, se crea una nueva instancia. Ejemplos clásicos en Java son `String` y las clases envoltorio como `Integer`.

Un **método modificador** es cualquier método que altera el estado interno del objeto (sus atributos de instancia). Un "setter" es el tipo más común de método modificador, pero no el único. Cualquier método que cambie el estado, como `incrementarSaldo()` o `rotar()`, es un modificador. No todos los métodos que reciben parámetros son modificadores; un método como `calcularDistancia(...)` que solo lee atributos y retorna un cálculo sin alterar el estado, es un **método de acceso** no modificador.

Una clase inmutable tiene ventajas significativas. En primer lugar, garantiza **seguridad en entornos concurrentes (multihilo)**, ya que al no poder cambiar, no hay riesgo de condiciones de carrera ni se requiere sincronización. En segundo lugar, son más **simples de razonar y depurar**, porque su estado es fijo y conocido tras su creación. También permiten **compartir libremente referencias** sin riesgo de efectos secundarios indeseados, y son candidatos ideales para ser utilizados como claves en colecciones como `HashMap` o elementos en `HashSet`, porque su `hashCode` no cambia, preservando la integridad de la colección.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No es recomendable incluir métodos "setter" siempre y como convención automática. La decisión de proveer setters debe basarse en el **diseño de la clase** y en si se pretende que sus atributos sean modificables después de la construcción. Incluir setters por defecto para todos los atributos puede violar el principio de **inmutabilidad** y debilitar el control que la clase tiene sobre su estado, exponiéndolo a modificaciones arbitrarias que pueden romper invariantes.

Es una práctica más adecuada **pensar primero si el atributo *debe* ser modificable**. Para atributos que representan propiedades esenciales e inmutables de la entidad (como el DNI de una persona o la fecha de creación), no se debe proporcionar setter. Para atributos que pueden cambiar, pero bajo reglas específicas, es mejor ofrecer **métodos modificadores con semántica de negocio** (como `depositar(double monto)` en lugar de `setSaldo(...)`) que encapsulen la lógica de validación y mantengan la coherencia. Los setters genéricos solo son apropiados para atributos que genuinamente pueden cambiarse libremente sin restricciones complejas.

La convención más importante no es crear setters de manera indiscriminada, sino **exponer la mínima interfaz necesaria** para que la clase cumpla su responsabilidad. Una clase con pocos o ningún setter suele ser más robusta, más fácil de mantener y más segura en entornos concurrentes. Los setters deben ser una elección de diseño consciente, no un reflejo automático.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

La clase `String` en Java es **inmutable**. Esto significa que una vez creado un objeto `String`, su secuencia de caracteres no puede ser modificada. Cualquier operación que parezca modificar una cadena, como `toUpperCase()` o `concat()`, en realidad **crea y devuelve un nuevo objeto** `String` con el resultado, dejando la cadena original sin cambios.

Al concatenar dos cadenas con el operador `+` (o el método `concat()`), se crea un **nuevo objeto** `String` que contiene la unión de ambas. Si esta operación se repite muchas veces en un bucle para construir una cadena larga, se genera una gran cantidad de objetos intermedios que son inmediatamente descartados, lo que resulta en un **impacto significativo en el rendimiento** (uso de memoria y tiempo de CPU debido a la copia repetida de caracteres y a la recolección de basura).

Para construir eficientemente una cadena de manera incremental, se debe usar la clase **`StringBuilder`** (o `StringBuffer` en contextos concurrentes). Estas clases son mutables y están diseñadas específicamente para esta tarea, ya que permiten modificar su secuencia interna de caracteres sin crear nuevos objetos en cada operación. Al finalizar la construcción, se llama a su método `toString()` para obtener el objeto `String` inmutable final. Este patrón evita la sobrecarga de las concatenaciones repetidas con `String`.


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java?

En POO, la comparación de objetos puede realizarse desde dos perspectivas fundamentales: **identidad** y **igualdad de contenido**. La **identidad** se refiere a si dos referencias apuntan exactamente al mismo objeto en memoria (misma ubicación). En Java, esto se verifica con el operador `==`. La **igualdad de contenido** (o equivalencia lógica) se refiere a si dos objetos distintos tienen el mismo estado o valor según los criterios definidos por la clase, lo que se verifica típicamente con el método **`equals(Object obj)`**.

El método `equals` en Java está definido en la clase `Object` (la raíz de toda la jerarquía) y está pensado para ser sobrescrito por las clases que necesiten comparar el contenido de sus instancias. **Por defecto**, la implementación en `Object` simplemente compara las identidades usando `==`, es decir, devuelve `true` solo si ambas referencias apuntan al mismo objeto. Por lo tanto, para comparar por contenido, una clase debe sobrescribir `equals` con una lógica que compare los atributos relevantes de los objetos.

Para comparar dos cadenas (`String`) en Java por su contenido, **siempre se debe usar el método `equals`** (o `equalsIgnoreCase` para ignorar mayúsculas/minúsculas). Nunca se debe usar `==`, ya que este solo devuelve `true` si ambas referencias apuntan al mismo objeto `String` en memoria, lo que no es garantizado incluso para cadenas con los mismos caracteres (a menos que sean *internadas* explícitamente). Por ejemplo, `str1.equals(str2)` comparará secuencialmente los caracteres de ambas cadenas y devolverá `true` si son idénticas.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?

Las clases **"wrapper"** (envoltorio o contenedor) son clases que encapsulan un valor de tipo primitivo (como `int`, `char`, `double`) dentro de un objeto. En Java, cada tipo primitivo tiene su clase wrapper correspondiente en el paquete `java.lang`: `Integer` para `int`, `Double` para `double`, `Character` para `char`, etc. Su propósito principal es permitir que los valores primitivos sean tratados como objetos cuando el contexto lo requiere, como al ser almacenados en colecciones genéricas (`ArrayList<Integer>`) o al usar métodos que esperan objetos.

El proceso de conversión entre primitivos y sus wrappers puede ser **automático** mediante el **autoboxing** y **unboxing** (introducido en Java 5). El *autoboxing* convierte automáticamente un primitivo a su wrapper (ej. `int` a `Integer`) cuando se necesita un objeto. El *unboxing* realiza la operación inversa, extrayendo el valor primitivo del objeto wrapper. Por ejemplo: `Integer obj = 42;` (autoboxing) y `int valor = obj;` (unboxing). Sin embargo, a nivel del compilador y de la JVM, estas operaciones implican la creación o extracción del objeto, no son meras reinterpretaciones.

La ventaja principal es la **uniformidad** en el sistema de tipos orientado a objetos, permitiendo que los primitivos participen en estructuras y APIs diseñadas para objetos. También proporcionan métodos utilitarios estáticos (como `Integer.parseInt(String)` o `Double.isNaN()`) y constantes útiles. No todos los lenguajes orientados a objetos tienen esta dicotomía. En C#, por ejemplo, los "tipos de valor" (`int`, `struct`) y "tipos de referencia" (`class`) coexisten, pero hay *boxing* y *unboxing* automático similar. En Python o Ruby, todo es un objeto, por lo que no existen primitivos separados ni se necesitan wrappers; un entero ya es una instancia de la clase `int`.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un **tipo de dato enumerado** (enum) es un tipo de dato especial que define un conjunto fijo de constantes nombradas, representando todos los valores posibles que una variable de ese tipo puede tomar. En la POO, los enumerados modelan dominios con un número discreto y limitado de valores, como los días de la semana, estados de un proceso (`ACTIVO`, `INACTIVO`, `PENDIENTE`) o categorías fijas, lo que mejora la legibilidad y seguridad del código al evitar el uso de literales numéricos o cadenas "mágicas".

En Java, un **enum es una clase** especial que extiende implícitamente `java.lang.Enum`. Puede tener atributos, constructores, métodos y hasta implementar interfaces, pero no puede extender otra clase (debido a la herencia única). Cada constante enumerada es una instancia única y pública de esa clase, creada al inicio de la JVM. Por ejemplo, `enum Dia { LUNES, MARTES }` define una clase `Dia` con dos instancias `LUNES` y `MARTES`.

En términos de **encapsulación**, los enumerados en Java ofrecen ventajas significativas. Al ser una clase, puede declarar atributos privados y un constructor privado (implícitamente privado), encapsulando los datos asociados a cada constante. Por ejemplo, un enum `Planeta` puede encapsular atributos como `masa` y `radio`, que se inicializan en el constructor y se exponen mediante getters públicos, garantizando que estos datos sean inmutables y estén controlados. Además, el conjunto de constantes es cerrado y no puede extenderse desde fuera, lo que protege la integridad del tipo y previene la creación de instancias no válidas, siendo un excelente ejemplo de un tipo fuertemente encapsulado y seguro en tiempo de compilación.


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

```java
public enum Mes {
    ENERO(31, 1),
    FEBRERO(28, 2),
    MARZO(31, 3),
    ABRIL(30, 4),
    MAYO(31, 5),
    JUNIO(30, 6),
    JULIO(31, 7),
    AGOSTO(31, 8),
    SEPTIEMBRE(30, 9),
    OCTUBRE(31, 10),
    NOVIEMBRE(30, 11),
    DICIEMBRE(31, 12);

    // Atributos privados encapsulados
    private final int dias;
    private final int ordinalAnio;

    // Constructor privado (implícitamente privado en los enums)
    Mes(int dias, int ordinalAnio) {
        this.dias = dias;
        this.ordinalAnio = ordinalAnio;
    }

    // Métodos getter públicos
    public int getDias() {
        return dias;
    }

    public int getOrdinalAnio() {
        return ordinalAnio;
    }

    // Métodos para estaciones (hemisferio norte como referencia común)
    public boolean esDePrimavera(boolean esHemisferioNorte) {
        int m = this.ordinalAnio;
        return esHemisferioNorte ? (m >= 3 && m <= 5) : (m >= 9 && m <= 11);
    }

    public boolean esDeVerano(boolean esHemisferioNorte) {
        int m = this.ordinalAnio;
        return esHemisferioNorte ? (m >= 6 && m <= 8) : (m >= 12 || m <= 2);
    }

    public boolean esDeOtonio(boolean esHemisferioNorte) {
        int m = this.ordinalAnio;
        return esHemisferioNorte ? (m >= 9 && m <= 11) : (m >= 3 && m <= 5);
    }

    public boolean esDeInvierno(boolean esHemisferioNorte) {
        int m = this.ordinalAnio;
        return esHemisferioNorte ? (m >= 12 || m <= 2) : (m >= 6 && m <= 8);
    }
}
```

Este enum `Mes` encapsula los atributos `dias` y `ordinalAnio` como `private final`, inicializados a través de un constructor privado que se llama al crear cada constante. Los métodos `getDias()` y `getOrdinalAnio()` proporcionan acceso controlado a estos datos. Los métodos de estaciones (`esDePrimavera`, etc.) implementan la lógica basada en el ordinal del mes y el parámetro booleano que indica el hemisferio, devolviendo `true` o `false` según corresponda. El diseño aprovecha la encapsulación del enum para garantizar la inmutabilidad y la consistencia de los datos asociados a cada mes.