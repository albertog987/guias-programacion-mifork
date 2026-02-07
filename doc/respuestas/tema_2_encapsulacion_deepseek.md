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

### Respuesta


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

### Respuesta


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

### Respuesta


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

### Respuesta


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

### Respuesta


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado. Añade además cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta
