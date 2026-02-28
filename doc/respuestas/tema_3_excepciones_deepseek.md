
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

### Respuesta


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta


## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

