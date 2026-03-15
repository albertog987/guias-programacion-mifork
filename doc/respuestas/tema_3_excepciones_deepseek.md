# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

En C, al no contar con un mecanismo de excepciones integrado en el lenguaje, el programador debe idear formas explícitas de comunicar condiciones de error desde las funciones a quien las invoca. La función `raiz` que debe calcular la raíz cuadrada de un número positivo necesita, por tanto, un mecanismo para señalar que se ha recibido un argumento inválido (negativo) y permitir que el código llamante tome una decisión informada.

Una primera opción de diseño es utilizar el **valor de retorno** de la función para indicar el éxito o fracaso de la operación. En este enfoque, la función podría devolver un código de error (por ejemplo, un entero) y pasar el resultado calculado a través de un puntero proporcionado como argumento. Esta es una técnica muy común en C, donde las funciones a menudo devuelven un entero que indica éxito (0) o un código de error específico (distinto de cero), y los resultados se devuelven mediante parámetros de salida.

```c
#include <stdio.h>
#include <math.h>

// Devuelve 0 si éxito, -1 si error (número negativo)
int raiz(double numero, double *resultado) {
    if (numero < 0.0) {
        return -1; // Código de error
    }
    *resultado = sqrt(numero);
    return 0; // Éxito
}

int main() {
    double valor = -9.0;
    double res;
    int estado = raiz(valor, &res);
    
    if (estado != 0) {
        printf("Error: no se puede calcular raíz de número negativo\n");
    } else {
        printf("La raíz es: %f\n", res);
    }
    return 0;
}
```

Una segunda opción es utilizar una **variable global de error** (como `errno` en la biblioteca estándar de C). En este diseño, la función `raiz` no modifica su interfaz de retorno (podría devolver directamente el resultado o un valor especial como NaN), y en caso de error, establece una variable global que el código llamante debe consultar después de la llamada para verificar si ocurrió algún problema. Aunque funcional, este enfoque tiene desventajas en entornos concurrentes y puede llevar a errores si el programador olvida verificar la variable.

```c
#include <stdio.h>
#include <math.h>

int error_raiz = 0; // Variable global de error

double raiz(double numero) {
    if (numero < 0.0) {
        error_raiz = 1; // Señalizar error
        return 0.0; // Valor de retorno por defecto en caso de error
    }
    error_raiz = 0; // Limpiar señal de error
    return sqrt(numero);
}

int main() {
    double valor = -9.0;
    double res = raiz(valor);
    
    if (error_raiz) {
        printf("Error: no se puede calcular raíz de número negativo\n");
    } else {
        printf("La raíz es: %f\n", res);
    }
    return 0;
}
```

Ambos enfoques representan las estrategias fundamentales de manejo de errores en C: el retorno de códigos de estado y el uso de variables globales de error. Cada uno tiene sus compromisos en términos de claridad, robustez y facilidad de uso.


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una **excepción** es un evento anómalo o inesperado que ocurre durante la ejecución de un programa y que interrumpe el flujo normal de instrucciones. Representa una condición de error o una situación excepcional que el código no puede manejar en el contexto actual, como una división por cero, un archivo no encontrado o un argumento inválido. En lenguajes como Java, las excepciones son objetos que encapsulan información sobre el error, incluyendo su tipo, un mensaje descriptivo y la traza de la pila de llamadas.

El programador utiliza las excepciones con un **doble objetivo**. Por un lado, al **implementar funciones**, las usa para señalar que ha ocurrido una condición de error que no puede (o no debe) ser manejada localmente. En lugar de devolver códigos de error que el llamante podría ignorar, la función "lanza" una excepción (`throw`), delegando la responsabilidad del manejo a niveles superiores. Por otro lado, al **llamar a funciones**, el programador "captura" (`catch`) las excepciones para recuperarse del error de manera controlada, separando así el código normal del código de manejo de errores y mejorando la claridad y robustez del programa.

Las excepciones proporcionan un mecanismo estructurado y uniforme para la propagación de errores, a diferencia de los códigos de retorno en C que deben verificarse explícitamente en cada llamada. Permiten agrupar el manejo de errores en bloques específicos (`try-catch-finally`), garantizando que ciertas acciones (como liberar recursos) se ejecuten siempre, y facilitando la depuración mediante la traza de la pila que indica dónde y cómo se originó el problema.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

```java
public class Calculadora {
    
    // Método que calcula la raíz cuadrada y lanza una excepción si el argumento es inválido
    public double raiz(double numero) throws IllegalArgumentException {
        if (numero < 0) {
            throw new IllegalArgumentException("No se puede calcular la raíz cuadrada de un número negativo: " + numero);
        }
        return Math.sqrt(numero);
    }
    
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        double valor = -9.0;
        
        try {
            double resultado = calc.raiz(valor);
            System.out.println("La raíz es: " + resultado);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
            // Opcionalmente se puede imprimir la traza completa:
            // e.printStackTrace();
        }
    }
}
```

En este ejemplo, el método `raiz` de la clase `Calculadora` verifica si el número proporcionado es negativo. Si lo es, en lugar de devolver un código de error o establecer una variable global, **lanza una excepción** mediante `throw new IllegalArgumentException(...)`. Esta excepción es un objeto de tipo `IllegalArgumentException` que contiene un mensaje descriptivo. La declaración `throws IllegalArgumentException` en la firma del método informa a quien llame que este método puede lanzar ese tipo de excepción (aunque en este caso, al ser una excepción no verificada, no es obligatorio declararla).

Desde el método `main`, la llamada a `raiz` se envuelve en un bloque `try-catch`. Dentro del `try` se ejecuta el código que puede fallar. Si se lanza la excepción, la ejecución salta inmediatamente al bloque `catch` correspondiente, donde se maneja el error mostrando el mensaje. Este mecanismo separa claramente la lógica normal del programa (cálculo y muestra del resultado) de la lógica de manejo de errores, y garantiza que el flujo del programa no continúe con un resultado inválido. A diferencia de C, no es necesario verificar explícitamente códigos de retorno en cada llamada; el flujo de control se transfiere automáticamente al manejador adecuado.


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

**Lanzar** una excepción es el acto de crear un objeto de excepción y entregarlo al sistema de ejecución mediante la palabra clave `throw`. Cuando un método lanza una excepción, indica que ha ocurrido una situación anómala que impide continuar con la ejecución normal del método. En el ejemplo de `Calculadora.raiz`, la instrucción `throw new IllegalArgumentException(...)` crea una nueva excepción y la lanza, deteniendo inmediatamente la ejecución del método `raiz` en ese punto. El control abandona el método sin ejecutar ninguna instrucción posterior y sin devolver ningún valor.

**Capturar** (o controlar) una excepción es el proceso mediante el cual se intercepta la excepción lanzada para manejarla apropiadamente. Esto se realiza con un bloque `try-catch`. En el ejemplo, el bloque `try` envuelve la llamada a `raiz`. Si se lanza una excepción, la ejecución salta al bloque `catch` que coincide con el tipo de excepción (`IllegalArgumentException`), donde se ejecuta el código de manejo (en este caso, mostrar un mensaje de error). Una vez finalizado el bloque `catch`, el programa continúa con la siguiente instrucción después del bloque `try-catch`, a menos que se haya lanzado una nueva excepción o se haya salido del método con `return`.

La **propagación** de una excepción es el mecanismo por el cual la excepción "asciende" por la pila de llamadas en busca de un bloque `catch` que pueda manejarla. Cuando un método lanza una excepción y no la captura dentro de sí mismo, la excepción se propaga al método que lo invocó, y así sucesivamente. Durante esta propagación, los métodos intermedios que no capturan la excepción **no se reanudan**; su ejecución se interrumpe en el punto de la llamada que causó la excepción, y se van retirando de la pila de llamadas (se "desenrolla" la pila). Ninguna instrucción posterior en esos métodos se ejecuta. Solo cuando se encuentra un manejador `catch` apropiado, la excepción queda capturada y el flujo continúa desde ahí.

En el ejemplo, si se llama a `raiz` con un número negativo, la excepción se lanza dentro de `raiz`. Al no haber un bloque `try-catch` dentro del propio método `raiz`, la excepción se propaga inmediatamente al método `main` (el llamante), justo en el punto donde se realizó la llamada `calc.raiz(valor)`. Como la llamada está dentro de un bloque `try`, la excepción es capturada por el `catch` correspondiente. Si `main` no capturara la excepción, esta se propagaría a la máquina virtual, que normalmente mostraría la traza de la pila y terminaría el programa. Durante esta propagación, la ejecución de `raiz` se interrumpe completamente, y ninguna línea posterior a `throw` dentro de `raiz` se ejecuta.


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

La **propagación natural** de las excepciones a través de la pila de llamadas ofrece una ventaja fundamental frente a los mecanismos de C: **libera al programador de la tediosa y propensa a errores tarea de verificar y propagar manualmente códigos de error en cada nivel de la jerarquía de llamadas**. En C, cada función debe comprobar explícitamente el valor de retorno de las funciones que llama y decidir si puede manejar el error localmente o debe devolver a su vez un código de error a su llamante, generando una cadena de comprobaciones que ensucia el código y aumenta la probabilidad de olvidar algún caso.

En Java, cuando una función lanza una excepción, esta **asciende automáticamente por la pila hasta encontrar un bloque `catch` apropiado**. Los métodos intermedios no necesitan contener ninguna lógica de verificación ni de reenvío; simplemente no capturan la excepción y esta continúa su propagación de forma implícita. Esto simplifica drásticamente el código de las funciones que actúan como meras intermediarias, ya que pueden centrarse en su lógica principal sin contaminarse con comprobaciones de error de bajo nivel.

Otra ventaja crucial es la **centralización del manejo de errores**. En lugar de tener fragmentos de código de error dispersos por todo el programa, se pueden agrupar en un único lugar (o en pocos lugares) donde tenga sentido manejar la excepción, típicamente en un nivel más alto de abstracción. Por ejemplo, una función de bajo nivel puede lanzar una excepción de "archivo no encontrado", y esta puede propagarse hasta el nivel de la interfaz de usuario, donde se muestra un mensaje apropiado y se ofrece la posibilidad de reintentar la operación, sin que las funciones intermedias tengan que ocuparse de este detalle.

Además, la propagación garantiza que **no se omita el manejo de errores por descuido**. En C, un programador puede olvidar verificar un código de retorno y el programa continuaría con datos inválidos o en un estado inconsistente. En Java, si una excepción no es capturada en ningún punto, eventualmente llega a la máquina virtual, que termina el programa mostrando la traza, haciendo visible el problema en lugar de ocultarlo. Esto fomenta un diseño más robusto y fiable, donde las condiciones excepcionales no pueden ser ignoradas silenciosamente.


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

En orientación a objetos, específicamente en Java, **las excepciones son objetos** que pertenecen a clases derivadas de la clase `Throwable` (o de sus subclases `Exception` o `RuntimeException`). Cada excepción que se lanza es una instancia de una clase, con sus propios atributos y métodos. Esto significa que cuando se escribe `throw new IllegalArgumentException("mensaje")`, se está creando un objeto de la clase `IllegalArgumentException`, que encapsula información relevante sobre el error: un mensaje descriptivo, la traza de la pila (una lista de los métodos que se estaban ejecutando en ese momento) y, opcionalmente, una causa subyacente.

Esta naturaleza orientada a objetos de las excepciones aporta ventajas significativas en términos de **encapsulación**. Al ser objetos, las excepciones pueden contener datos adicionales sobre el error de manera estructurada. Por ejemplo, una excepción personalizada para una operación bancaria podría encapsular el saldo actual, el monto solicitado y el tipo de operación fallida. Esta información queda protegida dentro del objeto excepción (con atributos privados y métodos de acceso) y solo se expone a través de métodos públicos como `getMessage()`, `printStackTrace()` o métodos específicos definidos por el programador. El código que captura la excepción puede consultar esta información de manera controlada, sin necesidad de recurrir a variables globales o estructuras de datos desorganizadas.

Sí, se pueden **crear excepciones personalizadas** definiendo nuevas clases que hereden de `Exception` (para excepciones verificadas) o de `RuntimeException` (para excepciones no verificadas). Esto permite modelar con precisión los errores específicos del dominio del problema. Por ejemplo, en una aplicación bancaria se podría definir `SaldoInsuficienteException` con atributos como `saldoActual` y `cantidadSolicitada`. Esta personalización mejora la expresividad del código y facilita un manejo de errores más fino y adaptado al contexto, ya que los bloques `catch` pueden distinguir entre diferentes tipos de excepciones personalizadas y reaccionar de manera adecuada a cada una.


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

En cualquier objeto excepción de Java, la **información esencial** que resulta invaluable al llegar a un manejador es, en primer lugar, el **tipo de la excepción** (la clase específica de la que es instancia). Este tipo indica categóricamente la **naturaleza del error** que ha ocurrido, permitiendo al programador diferenciar, por ejemplo, entre un archivo no encontrado (`FileNotFoundException`) y un error de formato de datos (`NumberFormatException`). El manejador puede así usar múltiples bloques `catch` para reaccionar de manera distinta ante cada situación, algo imposible de lograr con un simple código numérico de error como en C, donde un único valor `-1` podría significar múltiples cosas y requerir documentación externa para ser interpretado.

En segundo lugar, todo objeto excepción encapsula un **mensaje descriptivo** (accesible mediante `getMessage()`) que proporciona detalles humanos legibles sobre las circunstancias específicas del error, como el nombre del archivo que no pudo abrirse o el valor que causó la excepción. Adicionalmente, y quizás lo más poderoso, la excepción captura automáticamente la **traza de la pila** (*stack trace*), una lista completa de todos los métodos que estaban en ejecución en el momento del lanzamiento, incluyendo nombres de clases, métodos y números de línea. Esta información, ausente en los códigos de retorno de C, es fundamental para depurar, ya que revela no solo qué falló, sino exactamente la secuencia de llamadas que condujo al fallo, permitiendo localizar el origen del problema con precisión quirúrgica sin necesidad de añadir instrucciones de depuración manuales.


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

En Java, un bloque `try` puede ir seguido de **múltiples bloques `catch`**. Esta estructura permite manejar diferentes tipos de excepciones de manera específica, proporcionando respuestas distintas según la naturaleza del error que se haya producido. Cada bloque `catch` se define con un parámetro que indica el tipo de excepción que puede capturar, por ejemplo, `catch (IOException e)` para errores de entrada/salida o `catch (NumberFormatException e)` para errores de conversión numérica.

De todos los bloques `catch` asociados a un mismo `try`, **solamente se ejecuta uno**, y únicamente si el tipo de la excepción lanzada coincide con el tipo declarado en ese bloque (o es una subclase del mismo). La ejecución sigue un orden secuencial: cuando se lanza una excepción dentro del bloque `try`, el sistema la compara con cada bloque `catch` en el orden en que aparecen escritos. En cuanto encuentra el primer bloque cuyo tipo sea compatible con la excepción lanzada, ejecuta el código de ese bloque y, a continuación, el flujo del programa continúa después de toda la estructura `try-catch`. El resto de bloques `catch` se ignoran por completo.

Es importante señalar que, por este motivo, los bloques `catch` deben ordenarse de más específicos a más generales. Si se coloca primero un bloque `catch (Exception e)` (que captura cualquier excepción), los bloques posteriores para excepciones más concretas nunca se ejecutarían, ya que el primero siempre coincidiría. Esta organización garantiza que el manejo de errores sea preciso y que cada excepción reciba el tratamiento más adecuado a su tipo.


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

En Java, el bloque **`finally`** es la construcción que garantiza la ejecución de código necesario, como la liberación de recursos, **independientemente de si se lanza o no una excepción, y de si esta es capturada o no**. El bloque `finally` se coloca después de los bloques `try` y `catch`, y su código se ejecuta siempre: tanto si el bloque `try` termina normalmente, como si se lanza una excepción y es capturada, como incluso si la excepción no es capturada y se propaga. Esta característica lo hace ideal para tareas de limpieza que deben ocurrir en cualquier escenario.

En el siguiente ejemplo con `catch`, se abre un fichero y se procesa. Tanto si la operación tiene éxito como si se produce una excepción (por ejemplo, al leer), el bloque `finally` se encarga de cerrar el fichero, asegurando que el recurso se libera adecuadamente:

```java
import java.io.*;

public class EjemploFinally {
    public void procesarFichero(String nombre) {
        BufferedReader lector = null;
        try {
            lector = new BufferedReader(new FileReader(nombre));
            String linea = lector.readLine();
            System.out.println("Primera línea: " + linea);
            // Aquí podría lanzarse una excepción
        } catch (IOException e) {
            System.out.println("Error al leer el fichero: " + e.getMessage());
        } finally {
            try {
                if (lector != null) {
                    lector.close(); // Liberación del recurso
                    System.out.println("Fichero cerrado correctamente");
                }
            } catch (IOException e) {
                System.out.println("Error al cerrar el fichero");
            }
        }
    }
}
```

En el caso de **no usar `catch`**, el bloque `finally` sigue ejecutándose antes de que la excepción se propague al llamante. Esto es útil cuando la función no puede manejar el error pero debe garantizar la limpieza de recursos antes de fallar:

```java
public void leerFichero(String nombre) throws IOException {
    BufferedReader lector = null;
    try {
        lector = new BufferedReader(new FileReader(nombre));
        String linea = lector.readLine();
        System.out.println("Primera línea: " + linea);
    } finally {
        // Este bloque se ejecuta incluso si hay excepción y antes de propagarla
        if (lector != null) {
            try {
                lector.close();
            } catch (IOException e) {
                System.out.println("Error al cerrar el fichero");
            }
        }
    }
    // Si hay excepción, no se llega aquí, pero el finally ya se ejecutó
}
```

En ambos casos, el bloque `finally` proporciona la garantía de que el código de limpieza se ejecuta, manteniendo la robustez del programa y evitando fugas de recursos. Es importante destacar que `finally` se ejecuta incluso si hay instrucciones `return` en `try` o `catch`, o si se relanza una excepción dentro de `catch`. Solo en situaciones extremas, como la llamada a `System.exit()` o un fallo catastrófico de la máquina virtual, el bloque `finally` podría no ejecutarse.


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, en Java el bloque `finally` puede aparecer **sin un bloque `catch`** asociado. La sintaxis permite un bloque `try` seguido directamente de un bloque `finally`, sin necesidad de que haya un `catch` intermedio. Esta estructura es útil cuando se desea garantizar la liberación de recursos o la ejecución de código de limpieza, pero no se pretende manejar la excepción en ese nivel; en su lugar, la excepción se propagará al llamante después de que se haya ejecutado el bloque `finally`.

El bloque `finally` se ejecuta **siempre, tanto si ocurre una excepción como si no**. Esta es su característica fundamental y la razón de su existencia. Si no ocurre ninguna excepción, el flujo ejecuta todo el bloque `try` y luego pasa al `finally`. Si ocurre una excepción dentro del `try`, el flujo salta inmediatamente al bloque `finally` (antes de propagar la excepción, si no hay `catch`, o después del `catch` si lo hay), ejecuta su código, y luego la excepción continúa propagándose o el programa sigue su curso normal.

Incluso si hay una instrucción `return` dentro del bloque `try` (o dentro de un `catch`), el bloque `finally` **se ejecuta antes de que el valor sea devuelto**. Primero se ejecuta el `finally` y luego se completa el `return`. Si el bloque `finally` también contiene una instrucción `return`, esta sobrescribirá el valor devuelto originalmente, lo que puede llevar a comportamientos inesperados y, por lo tanto, es una práctica que debe evitarse. Esta garantía de ejecución incondicional hace de `finally` el lugar ideal para liberar recursos como archivos abiertos, conexiones de red o bloqueos, asegurando que no queden recursos retenidos incluso ante situaciones excepcionales.


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

En Java, las excepciones se clasifican en dos grandes categorías: **controladas** (*checked exceptions*) y **no controladas** (*unchecked exceptions*). Esta distinción es fundamental en el diseño del lenguaje y afecta a cómo el programador debe gestionarlas.

Las **excepciones controladas** son aquellas que el compilador obliga a manejar explícitamente. Esto significa que si un método puede lanzar una excepción controlada, debe declararlo en su firma con `throws` o rodear el código que la puede lanzar con un bloque `try-catch`. Si no se hace ninguna de estas dos cosas, el código no compila. Estas excepciones son subclases de `Exception` pero **no** de `RuntimeException`. Representan condiciones anómalas que, aunque externas al control del programa (como errores de entrada/salida), son previsibles y de las cuales el programa debería intentar recuperarse de manera explícita.

Las **excepciones no controladas** son aquellas que el compilador **no** obliga a capturar ni declarar. Son subclases de `RuntimeException` (que a su vez es subclase de `Exception`). Representan errores que suelen ser consecuencia de problemas en la lógica del programa, como violaciones de precondiciones, argumentos inválidos o uso incorrecto de una API. La filosofía detrás de ellas es que, al ser errores de programación, deberían prevenirse en lugar de capturarse; si ocurren, a menudo indican un bug que debe corregirse en el código.

`RuntimeException` actúa como la clase raíz de todas las excepciones no controladas. Cualquier excepción que herede directa o indirectamente de `RuntimeException` es no controlada. Esto incluye excepciones muy comunes como `NullPointerException`, `IllegalArgumentException` o `ArrayIndexOutOfBoundsException`. El programador puede crear sus propias excepciones no controladas extendiendo `RuntimeException`.

**Ejemplos de situaciones donde se suelen preferir excepciones controladas:**

1. **Errores de entrada/salida**: Al intentar abrir un archivo que puede no existir (`FileNotFoundException`), o al leer de una conexión de red que puede fallar (`IOException`). Son situaciones externas, previsibles, y el programa debería poder ofrecer alternativas (como pedir otro archivo o reintentar la conexión).
2. **Problemas de configuración**: Al cargar un archivo de propiedades necesario para el funcionamiento de la aplicación que puede estar ausente o tener formato incorrecto.
3. **Errores de base de datos**: Al intentar establecer una conexión con una base de datos que puede no estar disponible (`SQLException`). La aplicación podría mostrar un mensaje al usuario y esperar a que el administrador solucione el problema.
4. **Falta de recursos críticos**: Al intentar crear un objeto que requiere un recurso externo (como un certificado SSL) que no se encuentra en el sistema.

**Ejemplos de situaciones donde se suelen preferir excepciones no controladas:**

1. **Precondiciones violadas**: Un método recibe un argumento `null` cuando la documentación especifica que no debe serlo (`IllegalArgumentException` o `NullPointerException`). Esto indica un error en el código llamante.
2. **Índices fuera de rango**: Al acceder a una posición inexistente en un array o lista (`ArrayIndexOutOfBoundsException`, `IndexOutOfBoundsException`). Es un error de lógica que debería corregirse.
3. **Operaciones aritméticas inválidas**: División por cero (`ArithmeticException`). El programa debería asegurarse de que el divisor no sea cero antes de realizar la operación.
4. **Errores de conversión de tipos**: Al intentar convertir una cadena a número con formato incorrecto (`NumberFormatException`), que aunque podría provenir de entrada del usuario, a menudo indica una validación insuficiente en niveles superiores.

La distinción no es siempre absoluta y puede haber debate, pero la guía general es: si se espera que el cliente de la API pueda recuperarse de manera sensata, usar controladas; si el error es de programación y la recuperación no tiene sentido (el programa debe corregirse), usar no controladas.


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

La palabra clave **`throws`** en Java se utiliza en la **declaración de un método** para indicar que ese método **puede lanzar** uno o más tipos de excepciones controladas durante su ejecución, y que no las va a capturar dentro de sí mismo. Forma parte de la firma del método, situándose después de la lista de parámetros y antes del cuerpo, seguida de los nombres de las clases de excepción separadas por comas. Por ejemplo: `public void leerFichero(String nombre) throws IOException, FileNotFoundException { ... }`.

`throws` actúa como una **declaración de intenciones** o un contrato entre el método y sus llamantes. Informa al compilador y a quien utilice el método que, al invocarlo, debe estar preparado para manejar esas excepciones, bien mediante un bloque `try-catch` que las capture, bien declarando a su vez `throws` para propagarlas a niveles superiores. Esta declaración es obligatoria para las excepciones controladas que un método puede lanzar pero no captura; sin ella, el código no compilaría.

`throws` es una **alternativa directa a capturar una excepción controlada** precisamente porque permite diferir la responsabilidad del manejo del error a quien invoca el método. Mientras que capturar la excepción con `try-catch` implica que el método actual asume el manejo del error y resuelve la situación localmente, `throws` indica que el método no sabe o no debe manejar ese error, y prefiere que sus llamantes decidan cómo actuar. Por ejemplo, un método de bajo nivel que lee de un archivo no debería decidir qué hacer si el archivo no existe (quizá mostrárselo al usuario, quizá usar un archivo por defecto), por lo que es más apropiado que declare `throws IOException` y permita que la capa de interfaz de usuario tome esa decisión.

Esta dualidad entre capturar y declarar con `throws` ofrece flexibilidad en el diseño. Una misma excepción controlada puede ser capturada en algún nivel de la jerarquía donde tenga sentido manejar el error, mientras que los niveles intermedios simplemente la propagan con `throws`. Esto evita que el código se llene de bloques `try-catch` innecesarios en funciones que no pueden hacer nada útil con el error, manteniendo la separación de responsabilidades y la claridad del flujo de control.


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

```java
import java.io.*;

public class LectorFicheros {
    
    public String leerPrimeraLinea(String nombreFichero) throws IOException {
        BufferedReader lector = null;
        try {
            lector = new BufferedReader(new FileReader(nombreFichero));
            return lector.readLine(); // Puede lanzar IOException
        } finally {
            // Este bloque se ejecuta antes de que la excepción se propague
            if (lector != null) {
                try {
                    lector.close();
                } catch (IOException e) {
                    // Si falla el cierre, se imprime pero no se interfiere con la excepción original
                    System.err.println("Error al cerrar el fichero: " + e.getMessage());
                }
            }
        }
    }
}
```

En este ejemplo, el método `leerPrimeraLinea` declara explícitamente `throws IOException` en su firma. Esto indica que no manejará internamente las excepciones de entrada/salida que puedan ocurrir, como que el fichero no exista (`FileNotFoundException`, una subclase de `IOException`) o que ocurra un error durante la lectura. En su lugar, estas excepciones se propagarán al método llamante, que deberá capturarlas o declararlas a su vez.

El bloque `finally` garantiza que, independientemente de si la operación tiene éxito o se lanza una excepción, el recurso `BufferedReader` se cierre correctamente. Nótese que el `return` está dentro del `try`; si se lanza una excepción antes de llegar a él, el flujo salta al `finally` y luego la excepción continúa propagándose. Incluso si el `return` se ejecuta, el `finally` se ejecuta **antes** de que el valor sea devuelto. El bloque `finally` contiene su propio `try-catch` para manejar posibles errores durante el cierre, pero estos no interfieren con la excepción original (si la hubo), aunque en una implementación más robusta se podría considerar registrar el error de cierre sin enmascarar la excepción principal.


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, se puede incluir cualquier tipo de excepción en la cláusula `throws` de un método, incluyendo excepciones no controladas como `RuntimeException` o sus subclases. La sintaxis de Java lo permite sin generar error de compilación. Sin embargo, hacerlo **no es obligatorio** y, de hecho, es poco común y generalmente considerado una mala práctica, ya que va contra la filosofía de diseño de las excepciones no controladas.

El método llamador **no está obligado** a poner un bloque `try-catch` para las excepciones no controladas, incluso si están declaradas en `throws`. El compilador no exige su manejo porque, por definición, las excepciones no controladas no están sujetas a la regla "catch-or-specify". El llamador puede, si lo desea, capturarlas con un `try-catch`, pero también puede ignorarlas completamente y dejar que se propaguen hasta la máquina virtual.

El **sentido** de declarar una excepción no controlada en `throws` sería puramente **documental**: informar a los usuarios del método que, aunque no estén obligados a capturarla, es posible que se lance esa excepción en determinadas circunstancias. Esto puede ser útil en APIs donde, aunque el error sea de programación (como una `IllegalArgumentException`), se quiere dejar constancia explícita de las condiciones bajo las cuales puede ocurrir. Por ejemplo, un método `calcularRaiz` podría documentar en su firma `throws IllegalArgumentException` para enfatizar que no acepta números negativos.

No obstante, esta práctica tiene detractores, ya que añade ruido a la firma y puede confundir a programadores acostumbrados a que `throws` solo se use para excepciones controladas. La alternativa más aceptada es documentar estas condiciones en los comentarios del método (con etiquetas `@throws` en JavaDoc) en lugar de incluirlas en la cláusula `throws`. De esta manera, se informa sin imponer una carga de manejo que no tiene sentido para errores de programación.


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

La recomendación general para decidir entre excepciones controladas y no controladas se basa en la **naturaleza del error** y en quién debe ser responsable de manejarlo. Las excepciones **controladas** (como `IOException`) deben usarse cuando se trata de condiciones anómalas **externas al programa**, razonablemente **previsibles**, y de las cuales **se espera que un cliente bien escrito pueda recuperarse de manera sensata**. Por ejemplo, al intentar abrir un archivo que puede no existir, o al conectarse a un servidor que puede no estar disponible. En estos casos, la API obliga al llamante a considerar explícitamente el error, fomentando un manejo robusto.

Las excepciones **no controladas** (como `IllegalArgumentException`, `NullPointerException` o cualquier subclase de `RuntimeException`) deben reservarse para **errores de programación** o **violaciones de precondiciones**. Estos errores indican que el código llamante no ha cumplido con el contrato establecido (por ejemplo, pasar un argumento `null` cuando no se permite, o un índice fuera del rango de un array). La filosofía subyacente es que estos errores deberían prevenirse en tiempo de desarrollo, no manejarse en tiempo de ejecución. Capturarlos rara vez tiene sentido, ya que normalmente indican un bug que debe corregirse en el código.

No todos los lenguajes orientados a objetos ofrecen ambas opciones. La distinción entre controladas y no controladas es una característica **particular de Java** (y en cierta medida de C# con las `checked exceptions`, aunque su uso es mucho más limitado y opcional). Lenguajes como **C++, Python, JavaScript o Ruby** solo tienen un tipo de excepción, equivalente a las no controladas de Java. En estos lenguajes, no hay obligación de declarar ni capturar excepciones; es responsabilidad del programador decidir si capturarlas o no, basándose en la documentación.

En los lenguajes con un solo tipo de excepción, la opción más habitual es el modelo **"no controlado"** (unchecked). La decisión de capturar o no una excepción queda enteramente en manos del programador, sin intervención del compilador. Esto proporciona mayor flexibilidad pero también requiere más disciplina y documentación para garantizar que los errores esperados sean manejados apropiadamente. La experiencia ha demostrado que el modelo de Java, aunque teóricamente más seguro, puede llevar a código excesivamente verboso y a malas prácticas (como capturar genéricamente `Exception` para evitar la propagación), por lo que muchos frameworks y librerías modernas en Java tienden a preferir el uso de excepciones no controladas, dejando las controladas solo para casos muy específicos como operaciones de E/S.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Sí, tiene perfecto sentido lanzar excepciones dentro de un bloque `catch`. Esta práctica, conocida como **relanzamiento de excepciones**, es una técnica común en el manejo de errores que permite realizar acciones locales (como registrar el error, liberar recursos parciales o añadir información contextual) y luego delegar el manejo definitivo de la excepción a un nivel superior. Al relanzar, el bloque `catch` actúa como un filtro o un punto de observación antes de que la excepción continúe su propagación.

**Sí se puede relanzar la misma excepción capturada** utilizando la palabra clave `throw` seguida de la referencia a la excepción capturada. Por ejemplo: `catch (IOException e) { ...; throw e; }`. Esto preserva el tipo original de la excepción, su mensaje y, lo más importante, la traza de la pila completa, que incluye el punto donde se originó la excepción. El método que finalmente capture esta excepción verá la traza completa, incluyendo el lugar original del error y el método intermedio que la relanzó.

Un escenario típico donde tiene sentido relanzar la misma excepción es el **registro centralizado de errores** (*logging*). Una función puede capturar una excepción, escribir un mensaje en un archivo de log con el contexto específico de esa operación (por ejemplo, "Error al procesar el pedido con ID 12345"), y luego relanzar la misma excepción para que sea manejada en un nivel superior, quizás mostrando un mensaje al usuario.

```java
public void procesarPedido(int idPedido) throws DatosException {
    try {
        // Operaciones que pueden lanzar SQLException
    } catch (SQLException e) {
        // Registrar el error con contexto adicional
        System.err.println("Error en pedido " + idPedido + ": " + e.getMessage());
        // Relanzar la misma excepción (podría ser convertida a otro tipo)
        throw e;
    }
}
```

También es común **relanzar una excepción diferente**, a menudo más abstracta, encapsulando la original. Esto se hace para **abstraer la capa de persistencia** y no exponer detalles de implementación (como excepciones SQL) a capas superiores. En este caso, se captura la excepción de bajo nivel y se lanza una excepción de negocio que contiene la original como causa.

```java
public void guardarUsuario(Usuario u) throws PersistenciaException {
    try {
        // Operaciones de base de datos
    } catch (SQLException e) {
        // Lanzar una excepción de negocio que encapsula la técnica
        throw new PersistenciaException("Error al guardar el usuario", e);
    }
}
```

En este segundo ejemplo, se preserva la causa original mediante el constructor que acepta `Throwable cause`, lo que permite mantener la traza completa para depuración mientras se ofrece una abstracción más limpia a las capas superiores. Esta técnica es fundamental para mantener la **separación de capas** y la **encapsulación** en aplicaciones multicapa.


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

Cuando una excepción es la **"causa"** de otra, significa que la excepción de nivel superior **envuelve** a la excepción original que ocurrió en una capa más baja, estableciendo una relación de causalidad. La excepción original (la causa) queda almacenada como un atributo dentro de la nueva excepción, accesible mediante el método `getCause()`. Este mecanismo es fundamental para mantener la trazabilidad completa del error a través de las diferentes capas de abstracción de una aplicación, sin perder la información específica de bajo nivel que originó el problema.

La causa permite que, aunque se lance una excepción de más alto nivel (como `PersistenciaException`), se preserve la información detallada de la excepción técnica original (como `SQLException`). De esta manera, quien maneja el error en la capa de presentación puede decidir qué mostrar al usuario basándose en la excepción de alto nivel, pero si es necesario depurar, se puede acceder a la causa para conocer el detalle técnico exacto de lo que falló.

```java
public class PersistenciaException extends Exception {
    public PersistenciaException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

public class UsuarioDAO {
    public void guardarUsuario(Usuario usuario) throws PersistenciaException {
        try {
            // Simulación de operación de base de datos que falla
            throw new SQLException("Error de conexión: timeout");
        } catch (SQLException e) {
            // Se encapsula la SQLException como causa de PersistenciaException
            throw new PersistenciaException("No se pudo guardar el usuario", e);
        }
    }
}
```

Cuando una excepción que tiene una causa se imprime por pantalla (por ejemplo, mediante `printStackTrace()` o cuando la máquina virtual la muestra al no ser capturada), **sí se ve** la información de la causa. La traza de la pila incluye tanto la excepción de alto nivel como la excepción causal, mostrando una sección "Caused by:" que detalla la excepción original y su propia traza. Esto proporciona una visión completa y jerárquica del error, desde el punto más alto donde se lanzó la excepción encapsulada hasta el lugar más bajo donde se originó el problema técnico, facilitando enormemente la depuración y la localización de fallos en aplicaciones multicapa.