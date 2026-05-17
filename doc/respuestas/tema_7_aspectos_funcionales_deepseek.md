# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

Un **puntero a función** en C es una variable que almacena la dirección de memoria de una función, permitiendo invocar dicha función indirectamente a través del puntero. Los punteros a función se declaran especificando el tipo de retorno, el nombre del puntero con un asterisco y paréntesis, y los tipos de los parámetros. Una vez que el puntero apunta a una función (por asignación del nombre de la función sin paréntesis), se puede llamar a la función usando el operador de desreferencia o directamente con el nombre del puntero seguido de los argumentos entre paréntesis. Este mecanismo es la base para implementar **retrollamadas** (*callbacks*) y pasar comportamientos como argumentos a otras funciones.

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

// Función que convierte una cadena a mayúsculas
void convertirAMayusculas(char* cadena) {
    for (int i = 0; cadena[i] != '\0'; i++) {
        cadena[i] = toupper(cadena[i]);
    }
}

int main() {
    // Declaración de un puntero a función:
    // void (*aMayusculas)(char*)  -> puntero a función que devuelve void y recibe char*
    void (*aMayusculas)(char*);
    
    // Asignación: se asigna la dirección de la función (el nombre de la función sin paréntesis)
    aMayusculas = convertirAMayusculas;
    
    // Uso del puntero para invocar la función
    char texto[] = "hola mundo";
    printf("Antes: %s\n", texto);
    
    aMayusculas(texto);   // Llamada mediante puntero (sintaxis directa)
    // También se puede usar (*aMayusculas)(texto); (sintaxis de desreferencia explícita)
    
    printf("Después: %s\n", texto);  // "HOLA MUNDO"
    
    return 0;
}
```

En el ejemplo, el puntero `aMayusculas` se declara con la sintaxis `void (*aMayusculas)(char*)`, que se lee como "aMayusculas es un puntero a una función que devuelve void y recibe un char*". Se le asigna la dirección de `convertirAMayusculas` (en C, el nombre de una función sin paréntesis se convierte implícitamente en un puntero a esa función). Luego, se invoca la función a través del puntero usando `aMayusculas(texto)`. Los punteros a función son la base de las técnicas de **programación funcional** en C (como `qsort` que recibe un puntero a función de comparación) y, aunque no tienen relación directa con la orientación a objetos, son el precedente histórico de conceptos como lambdas, closures y funciones de orden superior en lenguajes modernos (incluyendo Java con sus interfaces funcionales y expresiones lambda). La principal limitación en C es que los punteros a función no pueden capturar variables del contexto circundante (no tienen *closure*), lo que sí es posible en lenguajes funcionales y en Java a partir de las lambdas.


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

Una **función lambda** (o expresión lambda) es una función anónima que se puede definir de forma concisa en línea, sin necesidad de declararla como un método separado con nombre. Las lambdas permiten tratar el comportamiento (código) como un valor que puede ser asignado a una variable, pasado como argumento o devuelto desde una función. Este estilo de programación se denomina **programación funcional** y complementa la orientación a objetos permitiendo escribir código más declarativo, conciso y fácil de paralelizar. Las lambdas suelen aparecer acompañadas de **inferencia de tipos** (el compilador deduce los tipos de los parámetros) y pueden **capturar variables del contexto circundante** (formando un closure).

**Ejemplo en JavaScript** (las funciones lambda se definen con sintaxis `(parametros) => expresion` o `(parametros) => { bloque }`):

```javascript
// Lambda asignada a una variable local
const aMayusculas = (texto) => texto.toUpperCase();

// Uso de la lambda
let resultado = aMayusculas("hola mundo");
console.log(resultado);  // "HOLA MUNDO"

// También se puede pasar directamente sin asignar
console.log(((texto) => texto.toUpperCase())("otro texto"));
```

**Ejemplo en Java** (usando la interfaz funcional `Function<T, R>` del paquete `java.util.function`):

```java
import java.util.function.Function;

public class EjemploLambda {
    public static void main(String[] args) {
        // Lambda asignada a una variable de tipo Function<String, String>
        Function<String, String> aMayusculas = (String texto) -> texto.toUpperCase();
        
        // Versión más concisa con inferencia de tipos (Java permite omitir el tipo del parámetro)
        Function<String, String> aMayusculas2 = texto -> texto.toUpperCase();
        
        // Uso de la lambda
        String resultado = aMayusculas.apply("hola mundo");
        System.out.println(resultado);  // "HOLA MUNDO"
        
        // También se puede usar directamente sin variable
        Function<String, String> lambdaDirecta = s -> s.toUpperCase();
        System.out.println(lambdaDirecta.apply("java lambdas"));
    }
}
```

**Diferencias clave con los punteros a función de C**: Mientras que en C los punteros a función solo apuntan a funciones con nombre preexistentes, las lambdas en JavaScript y Java son **funciones anónimas** definidas en el lugar donde se usan. Además, las lambdas pueden **capturar variables del entorno** (efectivamente `final` o `effectively final` en Java). En Java, una lambda es una instancia de una **interfaz funcional** (una interfaz con un único método abstracto), como `Function<T,R>`. El compilador de Java convierte la lambda en el equivalente de una clase anónima que implementa dicha interfaz, pero con sintaxis más ligera. Las lambdas son especialmente útiles en combinación con la API de `Stream` para procesar colecciones de datos de forma declarativa (filtrado, mapeo, reducción). En ambos lenguajes, las lambdas promueven un estilo de código más expresivo y reducen la verbosidad comparado con clases anónimas (en Java) o funciones callback tradicionales.


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

El **paradigma funcional** es un estilo de programación que trata la computación como la **evaluación de funciones matemáticas** y evita el cambio de estado y los datos mutables. Sus principios fundamentales incluyen: **funciones de primera clase** (las funciones pueden asignarse a variables, pasarse como argumentos y devolverse como resultados), **inmutabilidad** (los datos no cambian una vez creados), **transparencia referencial** (una función siempre produce el mismo resultado para los mismos argumentos, sin efectos secundarios), y el uso de **funciones de orden superior** (funciones que operan sobre otras funciones). Este paradigma contrasta con la programación imperativa (que se centra en "cómo" hacer las cosas mediante instrucciones paso a paso) y con la orientación a objetos (que organiza el código alrededor de objetos con estado mutable), aunque pueden coexistir.

Java 8 (y versiones posteriores) se considera un lenguaje **multi-paradigma** porque, además de la orientación a objetos (clases, objetos, herencia, polimorfismo), incorporó características del paradigma funcional: **expresiones lambda** (funciones anónimas), **interfaces funcionales**, el paquete `java.util.function` con tipos como `Function`, `Predicate`, `Consumer`, y la **API Stream** para procesar colecciones de forma declarativa, en paralelo y con inmutabilidad. Java no es un lenguaje puramente funcional (sigue permitiendo y fomentando el estado mutable), pero ofrece las herramientas para escribir código funcional cuando sea conveniente, especialmente para operaciones de transformación de datos, filtrado, mapeo y reducción. Esta combinación permite al programador elegir el estilo más adecuado para cada tarea: orientación a objetos para modelar el dominio y encapsular estado, y funcional para transformaciones de datos fluidas y menos propensas a errores.

Que las funciones sean **"ciudadanos de primera clase"** significa que, en un lenguaje, las funciones pueden ser utilizadas como cualquier otro valor: se pueden asignar a variables, almacenar en estructuras de datos, pasar como argumentos a otras funciones (funciones de orden superior) y devolver como resultado de funciones. En Java antes de la versión 8, las funciones no eran ciudadanos de primera clase: la única forma de pasar comportamiento era mediante objetos que implementaban una interfaz (por ejemplo, `Runnable`), lo que resultaba verboso. Con las lambdas e interfaces funcionales, Java elevó las funciones al rango de ciudadanos de primera clase, aunque con la limitación de que deben ajustarse a una interfaz funcional. En lenguajes como JavaScript, Python o los puramente funcionales (Haskell, Scala), esta característica está más integrada y permite una mayor flexibilidad (por ejemplo, funciones sin la necesidad de interfaces contenedoras). Los ciudadanos de primera clase son la base del paradigma funcional y permiten construir abstracciones más poderosas y composición de funciones.


## 4. Explica la sintaxis básica de una función lambda en Java.

La sintaxis básica de una función lambda en Java sigue el patrón: **`(parámetros) -> { cuerpo }`**. Los parámetros se declaran entre paréntesis, sin especificar el tipo (inferido por el compilador) o especificándolo opcionalmente. El operador `->` separa la lista de parámetros del cuerpo. El cuerpo puede ser una **expresión única** (sin llaves ni `return` explícito) o un **bloque de código** entre llaves con múltiples sentencias, que puede incluir `return`. La lambda debe ser compatible con una **interfaz funcional** (una interfaz con un único método abstracto), como `Runnable`, `Function<T,R>`, `Consumer<T>`, `Predicate<T>`, etc. El compilador infiere la interfaz funcional a partir del contexto.

**Casos concretos de sintaxis**:

1. **Sin parámetros**: `() -> expresion` o `() -> { bloque }`
```java
Runnable tarea = () -> System.out.println("Sin parámetros");
```

2. **Un parámetro**: se pueden omitir los paréntesis (opcional en Java, a diferencia de otros lenguajes)
```java
Consumer<String> impresor = s -> System.out.println(s);
// O con paréntesis: (s) -> System.out.println(s)
```

3. **Varios parámetros**: paréntesis obligatorios, tipos opcionales
```java
BinaryOperator<Integer> suma = (a, b) -> a + b;
// Con tipos explícitos: (Integer a, Integer b) -> a + b
```

4. **Cuerpo con bloque**: requiere llaves y `return` si hay valor de retorno
```java
Function<String, Integer> longitud = s -> {
    int l = s.length();
    return l;
};
```

5. **Sin retorno (void)**: con bloque, no se requiere `return`
```java
Consumer<String> mostrar = s -> {
    System.out.print("Valor: ");
    System.out.println(s);
};
```

6. **Referencia a método** (forma ultra-concisa de lambda cuando solo se llama un método existente)
```java
Function<String, Integer> longitud = String::length;  // Equivalente a s -> s.length()
Consumer<String> impresor = System.out::println;     // Equivalente a s -> System.out.println(s)
```

La **inferencia de tipos** permite omitir los tipos de los parámetros cuando el compilador puede deducirlos del contexto de la interfaz funcional. Si hay ambigüedad, se pueden especificar los tipos explícitamente. Las lambdas pueden capturar variables del entorno siempre que sean **efectivamente finales** (no modificadas después de la inicialización). La sintaxis compacta de las lambdas es una mejora significativa respecto a las **clases anónimas** pre-Java 8, que requerían código más verboso. Por ejemplo, en lugar de `new Function<String,String>(){ public String apply(String s){ return s.toUpperCase(); } }`, una lambda permite simplemente `s -> s.toUpperCase()`. Esta sintaxis es clave para trabajar con la API `Stream` y escribir código funcional conciso y legible.


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

**Ejemplo en JavaScript** (las funciones son ciudadanos de primera clase, por lo que pasar una función como argumento es directo):

```javascript
// Función de transformación: lambda que convierte a mayúsculas
const aMayusculas = (texto) => texto.toUpperCase();

// Función de orden superior que recibe un string y una función transformadora
function transformar(texto, transformador) {
    return transformador(texto);
}

// También se puede definir con arrow function
const transformarArrow = (texto, transformador) => transformador(texto);

// Uso del método
let original = "hola mundo";
let resultado = transformar(original, aMayusculas);
console.log(resultado);  // "HOLA MUNDO"

// También se puede pasar la lambda directamente sin variable
let resultadoDirecto = transformar("javascript", (s) => s.toUpperCase());
console.log(resultadoDirecto);  // "JAVASCRIPT"
```

**Ejemplo en Java** (usando interfaz funcional `Function<String, String>`):

```java
import java.util.function.Function;

public class EjemploTransformador {
    
    // Método de orden superior que recibe un String y una función transformadora
    public static String transformar(String texto, Function<String, String> transformador) {
        // Aplica la función recibida al texto
        return transformador.apply(texto);
    }
    
    public static void main(String[] args) {
        // Definición de la función lambda
        Function<String, String> aMayusculas = s -> s.toUpperCase();
        
        // Paso de la función como parámetro
        String original = "hola mundo";
        String resultado = transformar(original, aMayusculas);
        System.out.println(resultado);  // "HOLA MUNDO"
        
        // También se puede pasar la lambda directamente sin variable
        String resultadoDirecto = transformar("java lambdas", s -> s.toUpperCase());
        System.out.println(resultadoDirecto);  // "JAVA LAMBDAS"
        
        // Ejemplo con otra función transformadora (inversa: minúsculas)
        String minusculas = transformar("HOLA MUNDO", s -> s.toLowerCase());
        System.out.println(minusculas);  // "hola mundo"
        
        // Ejemplo con longitud (devuelve String pero es una transformación)
        String longitudComoString = transformar("prueba", s -> String.valueOf(s.length()));
        System.out.println(longitudComoString);  // "6"
    }
}
```

**Explicación**: En ambos lenguajes, el método `transformar` es una **función de orden superior** porque recibe otra función como parámetro. La lambda `aMayusculas` cumple con la interfaz (en Java, la interfaz funcional `Function<String, String>`; en JavaScript, cualquier función que recibe un argumento y devuelve un valor). Dentro de `transformar`, se invoca la función pasada sobre el argumento `texto`. La ventaja de este diseño es que `transformar` es **genérico** en cuanto a la transformación: no necesita saber qué operación se va a aplicar, solo que recibirá una función que toma un `String` y devuelve otro `String`. Esto permite reutilizar el método con diferentes comportamientos (mayúsculas, minúsculas, cálculo de longitud, limpieza de espacios, etc.) sin modificar su código. La capacidad de pasar comportamiento como argumento es central en la programación funcional y es especialmente útil en combinación con APIs como `Stream`, donde operaciones como `map`, `filter` y `reduce` reciben lambdas para transformar o filtrar los datos de forma declarativa.


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

**Ejemplo en JavaScript** (pasando lambda directamente en la llamada):

```javascript
// Función de orden superior
function transformar(texto, transformador) {
    return transformador(texto);
}

// Llamada con lambda que invierte la cadena directamente
let resultado = transformar("hola mundo", (s) => s.split('').reverse().join(''));
console.log(resultado);  // "odnum aloh"

// También se puede usar con diferentes transformaciones inline
console.log(transformar("javascript", s => s.toUpperCase()));          // "JAVASCRIPT"
console.log(transformar("javascript", s => s.length));                 // 10
console.log(transformar("reconocer", s => s === s.split('').reverse().join('') ? "Es palíndromo" : "No es"));  // "Es palíndromo"
```

**Ejemplo en Java** (pasando lambda directamente en la llamada):

```java
import java.util.function.Function;

public class EjemploTransformadorInline {
    
    public static String transformar(String texto, Function<String, String> transformador) {
        return transformador.apply(texto);
    }
    
    public static void main(String[] args) {
        // Llamada con lambda que invierte la cadena directamente
        String resultado = transformar("hola mundo", s -> {
            // Implementación de inversión dentro de la lambda (con bloque)
            return new StringBuilder(s).reverse().toString();
        });
        System.out.println(resultado);  // "odnum aloh"
        
        // Versión más concisa sin bloque (expresión única)
        String resultado2 = transformar("hola mundo", s -> new StringBuilder(s).reverse().toString());
        System.out.println(resultado2);  // "odnum aloh"
        
        // Otras transformaciones inline
        String mayusculas = transformar("java lambdas", s -> s.toUpperCase());
        System.out.println(mayusculas);  // "JAVA LAMBDAS"
        
        // Calcular longitud como String
        String longitud = transformar("prueba", s -> String.valueOf(s.length()));
        System.out.println(longitud);  // "6"
        
        // Inversión con manejo adicional (ejemplo con bloque más complejo)
        String resultadoComplejo = transformar("reconocer", s -> {
            String invertida = new StringBuilder(s).reverse().toString();
            return s.equals(invertida) ? "Es palíndromo: " + s : "No es palíndromo: " + s;
        });
        System.out.println(resultadoComplejo);  // "Es palíndromo: reconocer"
    }
}
```

**Diferencias de implementación**:

- **JavaScript**: La inversión requiere convertir el string a array con `split('')`, invertir con `reverse()`, y unir con `join('')`. Al ser una expresión única, puede escribirse en una sola línea sin llaves, aunque la legibilidad puede ser menor.
- **Java**: Se utiliza `StringBuilder` que tiene el método `reverse()` específico para cadenas (más eficiente). La lambda puede ser una expresión única (sin llaves) o un bloque con múltiples sentencias. Java también permite la **inferencia de tipos**, por lo que no es necesario declarar el tipo de `s`.

En ambos lenguajes, la capacidad de definir la función **en el lugar de la llamada** (inline) elimina la necesidad de crear variables separadas o funciones nombradas para transformaciones simples y de un solo uso. Esto hace que el código sea más conciso y legible, especialmente cuando la transformación es pequeña y claramente relacionada con el contexto de la llamada. En Java, esta característica es muy utilizada con la API `Stream`, por ejemplo: `lista.stream().map(s -> new StringBuilder(s).reverse().toString()).collect(Collectors.toList())`. La sintaxis es más compacta que definir una clase anónima o un método auxiliar, fomentando un estilo de programación más declarativo y funcional.


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

Un **cierre** o **closure** es la capacidad de una función (incluyendo las expresiones lambda) para **capturar y recordar variables del ámbito en el que fue definida**, incluso después de que ese ámbito haya finalizado su ejecución. En lenguajes que soportan closures, la función "cierra" sobre las variables externas que utiliza, manteniendo una referencia a ellas. En Java, las lambdas pueden capturar variables locales siempre que sean **efectivamente finales** (no modificadas después de su inicialización). Esto significa que la lambda puede leer dichas variables, pero no puede modificarlas (a menos que sean atributos de una clase o variables de instancia). El closure permite que la lambda retenga el contexto en el momento de su creación, lo que es especialmente útil para callbacks, funciones de orden superior y programación funcional.

**Ejemplo en Java con captura de variable local**:

```java
import java.util.function.Function;

public class EjemploClosure {
    public static void main(String[] args) {
        // Variable local (efectivamente final porque no se modifica después)
        String sufijo = " [PROCESADO]";
        
        // Lambda que captura la variable local 'sufijo' (cierre)
        Function<String, String> agregarSufijo = s -> s + sufijo;
        
        // Uso de la lambda
        String resultado = agregarSufijo.apply("texto original");
        System.out.println(resultado);  // "texto original [PROCESADO]"
        
        // Ejemplo con transformar (reutilizando el método anterior)
        String resultado2 = transformar("hola mundo", s -> s + sufijo);
        System.out.println(resultado2);  // "hola mundo [PROCESADO]"
        
        // Demostración de que la variable no puede ser modificada (error de compilación)
        // sufijo = " [MODIFICADO]";  // Si se descomenta, ya no es "efectivamente final" y la lambda de arriba no compilaría
    }
    
    public static String transformar(String texto, Function<String, String> transformador) {
        return transformador.apply(texto);
    }
}
```

**Ejemplo más ilustrativo: lambda que concatena con una variable cuyo valor cambia entre diferentes creaciones de lambda**:

```java
import java.util.function.Function;
import java.util.ArrayList;
import java.util.List;

public class EjemploClosureMultiple {
    public static void main(String[] args) {
        List<Function<String, String>> transformadores = new ArrayList<>();
        
        // Creación de múltiples closures, cada uno capturando un sufijo diferente
        String[] sufijos = {" [PRIMERO]", " [SEGUNDO]", " [TERCERO]"};
        
        for (String sufijo : sufijos) {
            // Cada lambda captura el valor actual del sufijo en esa iteración
            // (sufijo es efectivamente final dentro de cada iteración)
            transformadores.add(s -> s + sufijo);
        }
        
        // Aplicar cada transformador
        String texto = "texto base";
        for (Function<String, String> t : transformadores) {
            System.out.println(t.apply(texto));
        }
        // Salida:
        // texto base [PRIMERO]
        // texto base [SEGUNDO]
        // texto base [TERCERO]
        
        // Ejemplo con variable local que cambia (NO compilaría si la lambda la modifica)
        String prefijo = "INICIO: ";
        Function<String, String> agregarPrefijo = s -> {
            // prefijo = "MODIFICADO";  // Error: variable local no puede ser modificada
            return prefijo + s;  // Solo lectura: permitido
        };
        System.out.println(agregarPrefijo.apply("texto"));  // "INICIO: texto"
        
        // Se puede crear una lambda que use una variable de instancia (no local) y sí modificarla
        Contenedor contenedor = new Contenedor();
        Function<String, String> modificador = s -> {
            contenedor.contador++;  // Esto sí está permitido: modificar estado de un objeto capturado
            return s + contenedor.contador;
        };
        System.out.println(modificador.apply("llamada1"));  // "llamada1" + valor del contador
        System.out.println(modificador.apply("llamada2"));  // "llamada2" + (valor incrementado)
    }
    
    static class Contenedor {
        int contador = 0;
    }
}
```

**Resumen de reglas para closures en Java**:
- Las lambdas pueden capturar variables locales del ámbito circundante **si son efectivamente finales** (no se les asigna un nuevo valor después de la inicialización, o son explícitamente `final`).
- La lambda **puede leer** dichas variables, pero **no puede reasignarlas** (el compilador impide la modificación).
- La lambda **puede modificar el estado interno** de un objeto referenciado por una variable capturada (ejemplo: `contenedor.contador++`), porque lo que se captura es la referencia al objeto, no el valor de la variable.
- Este comportamiento garantiza que el closure sea **thread-safe** en cuanto a las variables capturadas (no hay condiciones de carrera por reasignación), aunque la mutabilidad interna del objeto puede seguir siendo problemática.

Los closures son especialmente útiles en APIs de procesamiento de datos (como `Stream`) para encapsular comportamientos personalizados (predicados, mapeos, acumuladores) que dependen del contexto donde se definen, mejorando la expresividad y la reutilización del código.


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

La diferencia fundamental radica en la **capacidad de capturar el contexto circundante**. Un puntero a función en C **no puede capturar variables del entorno** donde fue definido; solo puede apuntar a funciones globales (o estáticas) que reciben todos los datos explícitamente a través de sus parámetros. En cambio, una función lambda en Java (y en muchos lenguajes modernos) es un **closure**: puede capturar y recordar variables del ámbito en el que fue definida (como variables locales efectivamente finales o atributos de instancia), incluso si ese ámbito ha finalizado su ejecución. Esta capacidad permite que las lambdas sean mucho más expresivas y reutilizables, ya que pueden adaptar su comportamiento basándose en datos contextuales sin necesidad de pasarlos explícitamente como parámetros.

En C, para emular un comportamiento similar a un closure, el programador debe recurrir a técnicas manuales como pasar un puntero `void*` que contenga los datos del contexto, y luego cada función callback debe recibir ese puntero y desempaquetarlo. Esto es engorroso, propenso a errores y no ofrece seguridad de tipos. Por ejemplo, si se tiene una función que debe usar un sufijo para transformar cadenas, en C se necesitaría una estructura con el sufijo y pasarla como `void*`; en Java, la lambda captura directamente la variable `sufijo` del entorno. Los punteros a función en C son un mecanismo de bajo nivel, eficiente pero limitado; las lambdas en Java (y otros lenguajes de alto nivel) son abstracciones más poderosas que incluyen el entorno capturado, aunque con una pequeña sobrecarga de creación de objetos (cada lambda que captura variables se convierte en una instancia de una interfaz funcional que guarda los valores capturados en campos).

Otra diferencia importante es la **seguridad de tipos**. En C, un puntero a función no tiene información de tipos más allá de la firma declarada; si se asigna incorrectamente, el compilador puede no detectar el error (o el comportamiento es indefinido si las convenciones de llamada no coinciden). En Java, las lambdas están fuertemente tipadas mediante interfaces funcionales (como `Function<T,R>`), y el compilador verifica que la lambda sea compatible con la interfaz esperada, garantizando seguridad en tiempo de compilación. Además, las lambdas en Java son objetos (instancias de interfaces funcionales) y pueden ser polimórficas, asignarse a variables de supertipos, etc., mientras que los punteros a función en C son simplemente direcciones de memoria.

Por último, la **sintaxis y expresividad** son notablemente diferentes. Las lambdas en Java permiten escribir código conciso y legible directamente en el lugar donde se necesita el comportamiento, mientras que en C, para usar un puntero a función, a menudo se necesita definir la función en otro lugar (a menos que se usen extensiones no estándar como GCC con `nested functions`). Las lambdas también permiten inferencia de tipos, referencias a métodos, y composición funcional (por ejemplo, `andThen`, `compose` en `Function`), características ausentes en los punteros a función de C. En resumen, los punteros a función en C son un mecanismo de bajo nivel para pasar comportamientos, pero sin closure; las lambdas en Java son closures de alto nivel, con captura de contexto, seguridad de tipos y sintaxis integrada.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

```java
import java.util.function.Function;

public class EjemploDevolverFuncion {
    
    // Función que CREA y DEVUELVE otra función (descuento)
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        // La lambda captura la variable 'porcentaje' (closure)
        // Calcula el factor de descuento: (1 - porcentaje/100)
        return cantidad -> cantidad * (1 - porcentaje / 100.0);
    }
    
    public static void main(String[] args) {
        // Crear dos funciones de descuento diferentes usando el método creador
        Function<Double, Double> descuento10 = crearDescuento(10);   // Descuento del 10%
        Function<Double, Double> descuento25 = crearDescuento(25);   // Descuento del 25%
        
        // Precio original
        double precioOriginal = 100.0;
        
        // Aplicar los descuentos
        double precioConDescuento10 = descuento10.apply(precioOriginal);
        double precioConDescuento25 = descuento25.apply(precioOriginal);
        
        System.out.println("Precio original: " + precioOriginal);
        System.out.println("Con descuento del 10%: " + precioConDescuento10);  // 90.0
        System.out.println("Con descuento del 25%: " + precioConDescuento25);  // 75.0
        
        // También se pueden encadenar o combinar descuentos
        double precioConAmbos = descuento10.andThen(descuento25).apply(precioOriginal);
        System.out.println("Aplicando 10% y luego 25%: " + precioConAmbos);  // 100 * 0.9 = 90, 90 * 0.75 = 67.5
        
        // La función es reutilizable para diferentes cantidades
        System.out.println("\nAplicando descuento del 10% a varias cantidades:");
        System.out.println("200 -> " + descuento10.apply(200.0));  // 180.0
        System.out.println("50  -> " + descuento10.apply(50.0));   // 45.0
        System.out.println("75  -> " + descuento10.apply(75.0));   // 67.5
    }
}
```

**Explicación del closure**: Dentro del método `crearDescuento`, la expresión lambda `cantidad -> cantidad * (1 - porcentaje / 100.0)` **captura** la variable local `porcentaje`. Cuando `crearDescuento` termina su ejecución, normalmente la variable `porcentaje` dejaría de existir (está en la pila de llamadas). Sin embargo, la lambda devuelta actúa como un **closure** que mantiene una referencia a esa variable, recordando su valor para cuando se invoque la lambda en el futuro. Cada llamada a `crearDescuento` crea un closure separado con su propio `porcentaje` capturado (10, 25, etc.). Esto es posible gracias a que, en tiempo de compilación, el compilador de Java transforma la lambda en una instancia de una interfaz funcional (en este caso, `Function<Double, Double>`) que almacena en campos los valores de las variables capturadas (efectivamente finales). Así, `descuento10` y `descuento25` son objetos distintos que contienen internamente sus respectivos porcentajes.

**Ventajas de este enfoque funcional**:
- **Generación dinámica de comportamientos**: Se pueden crear nuevas funciones de descuento en tiempo de ejecución con diferentes parámetros, sin necesidad de definir una clase separada para cada uno.
- **Reutilización y composición**: Las funciones devueltas pueden usarse directamente con `apply()`, o combinarse con otras funciones mediante `andThen()` o `compose()`.
- **Separación de la lógica**: La lógica de cálculo del descuento queda encapsulada dentro de la función creada, y el código que la usa solo necesita llamar a `apply()`, sin conocer la fórmula interna.
- **Inmutabilidad**: La función no modifica el estado original; simplemente devuelve un nuevo valor calculado.

Este patrón de **funciones que devuelven funciones** (funciones de orden superior) es característico de la programación funcional y es muy utilizado en combinación con la API `Stream` para construir procesamiento de datos parametrizable y componible. En este ejemplo, se podría extender fácilmente para crear funciones de incremento, impuestos, conversión de unidades, etc., manteniendo el mismo principio. La capacidad de crear closures en Java desde la versión 8 acerca el lenguaje a estilos de programación más expresivos y declarativos, aunque siempre dentro de las limitaciones de que las variables capturadas deben ser efectivamente finales.


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

Una **interfaz funcional** en Java es una interfaz que contiene **exactamente un método abstracto** (sin implementación). Puede contener múltiples métodos `default` o `static` (que sí tienen implementación), así como métodos públicos de `Object` (como `equals`, `toString`, `hashCode`), que no cuentan para la cuenta de métodos abstractos. Las interfaces funcionales son el **tipo destino** de las expresiones lambda y las referencias a métodos; es decir, una lambda solo puede asignarse a una variable o pasarse como argumento si su tipo es una interfaz funcional. Por convención, desde Java 8, se puede anotar una interfaz con `@FunctionalInterface` para que el compilador verifique que cumple la condición (un solo método abstracto) y genere un error si no es así, aunque esta anotación no es obligatoria.

Los requisitos para que una interfaz sea considerada funcional son:

1. **Tener exactamente un único método abstracto** (sin contar los métodos `default` y `static`, y sin contar los métodos públicos de `Object`).
2. Puede tener cualquier número de métodos `default` o `static` (implementados).
3. Puede declarar métodos abstractos que sobrescriban métodos de `Object` (como `boolean equals(Object obj)`), pero estos no cuentan porque cualquier clase que implemente la interfaz ya tendrá una implementación de `Object`.
4. Puede extender otras interfaces: si la interfaz padre tiene un método abstracto, la interfaz hija hereda ese método y no puede declarar otro método abstracto adicional (a menos que sea una sobrescritura con la misma firma, en cuyo caso sigue siendo un único método abstracto).

**Ejemplos de interfaces funcionales** (incluidas en `java.util.function`):

```java
@FunctionalInterface
public interface Runnable {
    void run();  // Un único método abstracto
}

@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);  // Un único método abstracto
    // Además tiene métodos default y static, pero eso no afecta
}

@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);  // Un único método abstracto
    // También tiene default methods como andThen, compose
}

@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);  // Un único método abstracto
    // default methods: and, or, negate
}

// Ejemplo con herencia
@FunctionalInterface
public interface MiInterfaz extends Function<String, Integer> {
    // Hereda el método apply de Function, no declara nuevos abstractos
    // Válido: sigue teniendo un solo método abstracto
}
```

**Contraejemplo (no es interfaz funcional)**:

```java
public interface NoFuncional {
    void metodo1();  // abstracto
    void metodo2();  // segundo abstracto -> NO es funcional
}
```

La importancia de las interfaces funcionales radica en que permiten que las expresiones lambda se integren perfectamente con el sistema de tipos estático de Java. El compilador infiere el tipo de la lambda basándose en la interfaz funcional esperada en el contexto (asignación, parámetro de método, etc.). Por ejemplo, al escribir `Function<String, Integer> f = s -> s.length();`, el compilador sabe que la lambda debe ser compatible con el método `apply(String)` de `Function`. Sin las interfaces funcionales, las lambdas no tendrían un tipo estático y no podrían coexistir con el resto del sistema de tipos de Java, fuertemente tipado. Además, `@FunctionalInterface` ayuda a documentar la intención y previene la adición accidental de métodos abstractos en el futuro, lo que rompería la compatibilidad con las lambdas existentes. Java 8 introdujo muchas interfaces funcionales predefinidas en el paquete `java.util.function` (por ejemplo, `Consumer`, `Supplier`, `Predicate`, `Function`, `UnaryOperator`, `BinaryOperator`, etc.) que cubren la mayoría de los casos comunes de uso de lambdas.


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

```java
// Declaración de la interfaz funcional con anotación @FunctionalInterface
@FunctionalInterface
public interface Transformador {
    
    // Único método abstracto: recibe un String y devuelve otro String
    String transformar(String texto);
    
    // Puede tener métodos default (opcional)
    default Transformador yLuego(Transformador siguiente) {
        return texto -> siguiente.transformar(this.transformar(texto));
    }
    
    // Puede tener métodos estáticos (opcional)
    static Transformador identidad() {
        return texto -> texto;
    }
}
```

```java
// Ejemplo de uso de la interfaz funcional Transformador
public class EjemploTransformadorPropio {
    
    // Método que acepta un Transformador como parámetro
    public static String procesar(String texto, Transformador t) {
        return t.transformar(texto);
    }
    
    public static void main(String[] args) {
        // Usar la interfaz funcional con lambdas
        Transformador aMayusculas = texto -> texto.toUpperCase();
        Transformador invertir = texto -> new StringBuilder(texto).reverse().toString();
        Transformador agregarSaludo = texto -> "Hola, " + texto;
        
        // Aplicar transformadores individualmente
        System.out.println(aMayusculas.transformar("hola mundo"));      // "HOLA MUNDO"
        System.out.println(invertir.transformar("hola mundo"));         // "odnum aloh"
        System.out.println(agregarSaludo.transformar("Juan"));          // "Hola, Juan"
        
        // Composición usando el método default yLuego
        Transformador primeroMayusculasLuegoInvertir = aMayusculas.yLuego(invertir);
        System.out.println(primeroMayusculasLuegoInvertir.transformar("hola mundo")); 
        // "HOLA MUNDO" -> invertido -> "ODNUM ALOH"
        
        // Usar el método estático identidad
        Transformador identidad = Transformador.identidad();
        System.out.println(identidad.transformar("sin cambios"));  // "sin cambios"
        
        // Pasar lambda directamente al método procesar
        String resultado = procesar("java", texto -> texto.toUpperCase() + "!!!");
        System.out.println(resultado);  // "JAVA!!!"
        
        // También se pueden usar referencias a métodos existentes
        Transformador referencia = String::trim;
        System.out.println(referencia.transformar("  texto con espacios  "));  // "texto con espacios"
    }
}
```

**Explicación de la interfaz funcional `Transformador`**:

- La anotación `@FunctionalInterface` no es estrictamente necesaria, pero es muy recomendable porque:
  - Indica la intención al compilador y a otros desarrolladores.
  - El compilador verifica que la interfaz tenga exactamente un método abstracto; si accidentalmente se añade otro, producirá un error.
  - Mejora la documentación y la mantenibilidad.

- El único método abstracto es `String transformar(String texto)`. Cualquier lambda asignable a `Transformador` debe ser compatible con esta firma: recibir un `String` y devolver un `String`.

- La interfaz incluye un **método default** `yLuego(Transformador siguiente)` que permite **componer transformadores** (aplicar uno después de otro). Este método recibe otro `Transformador` y devuelve un nuevo `Transformador` que aplica el actual y luego el siguiente. Esto demuestra cómo las interfaces funcionales pueden aprovechar los métodos `default` para enriquecer su API funcional sin romper el requisito de un único método abstracto.

- El **método estático** `identidad()` devuelve un `Transformador` que no modifica el texto (función identidad). Es un ejemplo de fábrica de transformadores comunes.

**Ventajas de definir interfaces funcionales personalizadas**:

- **Semántica clara**: `Transformador` es más específico que `Function<String,String>` y expresa mejor la intención del dominio.
- **Extensibilidad**: Se pueden añadir métodos `default` útiles (como `yLuego`, `componer`, `repetir`, etc.) específicos para transformadores de texto.
- **Control de tipos**: Se puede limitar el uso de lambdas solo a transformaciones de `String` a `String`, evitando confusiones con otros tipos.
- **Menor dependencia de la API estándar**: Útil cuando se necesita una semántica muy concreta o se trabaja en un contexto donde no se pueden usar las interfaces de `java.util.function` (por compatibilidad o por restricciones).

En general, la creación de interfaces funcionales personalizadas es una buena práctica cuando la interfaz representa un concepto específico del dominio y se espera enriquecerla con comportamientos compuestos o utilitarios. No obstante, para casos genéricos (transformaciones, predicados, consumidores, proveedores), Java ya proporciona `Function<T,R>`, `Predicate<T>`, `Consumer<T>`, `Supplier<T>`, etc., que cubren la mayoría de necesidades.


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

```java
// Interfaz funcional genérica: transforma un valor de tipo T en otro de tipo R
@FunctionalInterface
public interface Transformador<T, R> {
    
    // Único método abstracto: transforma un valor de tipo T en tipo R
    R transformar(T valor);
    
    // Método default para composición: aplicar este transformador y luego otro
    default <V> Transformador<T, V> yLuego(Transformador<? super R, ? extends V> siguiente) {
        return valor -> siguiente.transformar(this.transformar(valor));
    }
    
    // Método estático para crear un transformador identidad (mismo tipo de entrada y salida)
    static <T> Transformador<T, T> identidad() {
        return valor -> valor;
    }
}
```

```java
// Ejemplo de uso de Transformador<T, R>
public class EjemploTransformadorGenerico {
    
    // Transformador que redondea un Double a Integer
    private static final Transformador<Double, Integer> redondear = 
        (Double valor) -> (int) Math.round(valor);
    
    // Transformador que convierte Integer a String
    private static final Transformador<Integer, String> intAString = 
        Object::toString;  // Referencia a método equivalente a i -> i.toString()
    
    // Composición: primero redondear Double a Integer, luego Integer a String
    private static final Transformador<Double, String> redondearYString = 
        redondear.yLuego(intAString);
    
    public static void main(String[] args) {
        // Aplicar transformadores individuales
        double valor = 3.14159;
        Integer redondeado = redondear.transformar(valor);
        String comoString = intAString.transformar(123);
        
        System.out.println("Original: " + valor);
        System.out.println("Redondeado (Double -> Integer): " + redondeado);  // 3
        System.out.println("Entero a String: " + comoString);  // "123"
        
        // Transformación compuesta: Double -> String
        String resultadoFinal = redondearYString.transformar(3.14159);
        System.out.println("Composición (Double -> String): " + resultadoFinal);  // "3"
        
        // Uso con métodos de orden superior
        String aplicar = procesar(5.7, redondearYString);
        System.out.println("Procesar: " + aplicar);  // "6"
        
        // Ejemplo con función lambda inline
        Transformador<Double, Integer> truncar = d -> d.intValue();
        System.out.println("Truncado: " + truncar.transformar(3.9));  // 3
    }
    
    // Método de orden superior que recibe un Transformador
    public static String procesar(Double numero, Transformador<Double, String> t) {
        return "Resultado: " + t.transformar(numero);
    }
}
```

**Explicación de la interfaz genérica `Transformador<T, R>`**:

- **Parámetros de tipo**: `T` es el tipo de entrada (input), `R` es el tipo de salida (output). Esto permite mayor flexibilidad que la versión específica para `String`. Por ejemplo, se puede transformar `Double` a `Integer`, `String` a `Boolean`, `LocalDate` a `Long`, etc.

- **Método `yLuego`**: Ahora con genéricos más sofisticados. La firma `default <V> Transformador<T, V> yLuego(Transformador<? super R, ? extends V> siguiente)` utiliza **wildcards** para permitir composiciones más flexibles:
  - `? super R` significa que el transformador siguiente puede aceptar un tipo `R` o cualquier supertipo de `R` (contravarianza). Esto es necesario porque el transformador actual produce un valor de tipo `R`, y el siguiente transformador debe poder consumir ese valor.
  - `? extends V` significa que el transformador siguiente produce un valor que es `V` o un subtipo de `V` (covarianza). Esto permite trabajar con subtipos.

- **Método estático `identidad`**: Devuelve un transformador que deja el valor sin cambios. Solo funciona cuando `T` y `R` son el mismo tipo.

**Ventajas de esta interfaz genérica**:

1. **Reutilización**: Un mismo `Transformador<T,R>` puede usarse para cualquier par de tipos, evitando duplicación de código.
2. **Composición segura**: El método `yLuego` permite encadenar transformadores de manera segura en tiempo de compilación, manteniendo la compatibilidad de tipos.
3. **Expresividad**: Las lambdas se asignan directamente a `Transformador` sin necesidad de usar las interfaces estándar de `java.util.function`, aunque son conceptualmente equivalentes a `Function<T,R>`.
4. **Aprendizaje**: Este ejemplo ayuda a entender cómo funciona `Function<T,R>` por debajo, ya que es una interfaz funcional similar.

**Relación con la API estándar**: Java ya proporciona `Function<T,R>` en `java.util.function`, que es equivalente a `Transformador<T,R>` pero con una nomenclatura más estándar. Por lo tanto, en código real es preferible usar `Function<T,R>` a menos que se necesiten métodos adicionales específicos del dominio o una semántica más clara. No obstante, construir interfaces funcionales personalizadas (genéricas o no) es una herramienta útil cuando se quiere crear un DSL interno o añadir métodos auxiliares específicos al contexto de uso. El ejemplo de composición (`yLuego`) demuestra cómo se pueden enriquecer las interfaces funcionales para facilitar la programación funcional en Java.


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

Java 8 introdujo el paquete `java.util.function` que contiene una colección de **interfaces funcionales predefinidas** para cubrir los casos más comunes de uso de lambdas. Estas interfaces están diseñadas para trabajar con tipos referencia (objetos) y se organizan en categorías según el número de parámetros y el tipo de retorno. A continuación se presentan las más importantes:

## **Interfaces básicas (1 parámetro)**

| Interfaz | Método abstracto | Propósito | Ejemplo lambda |
|----------|-----------------|-----------|----------------|
| `Function<T, R>` | `R apply(T t)` | Transforma un valor `T` en `R` | `s -> s.length()` (Function<String,Integer>) |
| `Predicate<T>` | `boolean test(T t)` | Prueba una condición booleana | `s -> s.isEmpty()` |
| `Consumer<T>` | `void accept(T t)` | Consume un valor sin retorno | `s -> System.out.println(s)` |
| `Supplier<T>` | `T get()` | Provee un valor sin entrada | `() -> new ArrayList<>()` |
| `UnaryOperator<T>` | `T apply(T t)` | Función que transforma `T` en `T` (subinterfaz de Function) | `s -> s.toUpperCase()` |
| `IntFunction<R>` | `R apply(int value)` | Especialización de `Function` con `int` como entrada | `i -> new int[i]` |

## **Interfaces con 2 parámetros**

| Interfaz | Método abstracto | Propósito | Ejemplo lambda |
|----------|-----------------|-----------|----------------|
| `BiFunction<T, U, R>` | `R apply(T t, U u)` | Transforma dos valores en `R` | `(a,b) -> a + b` (BiFunction<Integer,Integer,Integer>) |
| `BiPredicate<T, U>` | `boolean test(T t, U u)` | Prueba condición con dos argumentos | `(s, prefix) -> s.startsWith(prefix)` |
| `BiConsumer<T, U>` | `void accept(T t, U u)` | Consume dos valores sin retorno | `(m, s) -> map.put(m, s)` |
| `BinaryOperator<T>` | `T apply(T t, T u)` | Operación binaria sobre dos `T` que devuelve `T` | `(a,b) -> a + b` |

## **Especializaciones para tipos primitivos**

Para evitar el *boxing/unboxing* (conversión entre primitivos y wrappers) y mejorar el rendimiento, Java proporciona versiones especializadas para `int`, `long`, `double`:

- **Para `int`**: `IntPredicate`, `IntConsumer`, `IntFunction<R>`, `IntSupplier`, `IntUnaryOperator`, `IntBinaryOperator`, `IntToDoubleFunction`, `IntToLongFunction`, etc.
- **Para `long`**: `LongPredicate`, `LongConsumer`, `LongFunction<R>`, `LongSupplier`, `LongUnaryOperator`, `LongBinaryOperator`, etc.
- **Para `double`**: `DoublePredicate`, `DoubleConsumer`, `DoubleFunction<R>`, `DoubleSupplier`, `DoubleUnaryOperator`, `DoubleBinaryOperator`, `DoubleToIntFunction`, etc.

**Ejemplo de uso de las interfaces estándar**:

```java
import java.util.function.*;
import java.util.*;

public class InterfacesFuncionalesEstándar {
    public static void main(String[] args) {
        // Function<T,R> - transformación
        Function<String, Integer> longitud = s -> s.length();
        System.out.println(longitud.apply("Hola"));  // 4
        
        // Predicate<T> - condición
        Predicate<String> noVacio = s -> !s.isEmpty();
        System.out.println(noVacio.test("Hola"));   // true
        System.out.println(noVacio.test(""));       // false
        
        // Consumer<T> - consume sin retorno
        Consumer<String> imprimir = s -> System.out.println("Mensaje: " + s);
        imprimir.accept("Java 8");  // "Mensaje: Java 8"
        
        // Supplier<T> - provee valor
        Supplier<Double> aleatorio = () -> Math.random();
        System.out.println(aleatorio.get());  // número aleatorio
        
        // UnaryOperator<T> - operación unaria
        UnaryOperator<String> aMayusculas = s -> s.toUpperCase();
        System.out.println(aMayusculas.apply("texto"));  // "TEXTO"
        
        // BinaryOperator<T> - operación binaria
        BinaryOperator<Integer> suma = (a, b) -> a + b;
        System.out.println(suma.apply(5, 3));  // 8
        
        // BiFunction<T,U,R> - dos entradas
        BiFunction<String, String, Integer> comparar = (s1, s2) -> s1.compareTo(s2);
        System.out.println(comparar.apply("abc", "abd"));  // -1
        
        // Especializaciones para primitivos (sin boxing)
        IntUnaryOperator cuadrado = x -> x * x;
        System.out.println(cuadrado.applyAsInt(5));  // 25 (int, no Integer)
        
        // Uso combinado con Streams
        List<String> nombres = Arrays.asList("Ana", "Luis", "Carlos");
        nombres.stream()
               .filter(s -> s.length() > 3)           // Predicate
               .map(String::toUpperCase)               // Function
               .forEach(System.out::println);          // Consumer
        // Salida: LUIS, CARLOS
    }
}
```

## **¿Cuándo usar interfaces personalizadas vs predefinidas?**

- **Usar las predefinidas** (`Function<T,R>`, `Predicate<T>`, etc.) en la mayoría de los casos, ya que son ampliamente conocidas, interoperan con la API `Stream` y otras APIs de Java, y evitan crear tipos redundantes.
- **Crear una interfaz funcional personalizada** cuando:
  - Se necesita una semántica de dominio más clara (ej: `Validador<Usuario>` en lugar de `Predicate<Usuario>`).
  - Se requieren métodos `default` o `static` específicos para ese dominio (ej: `andThen` pero con nombres más expresivos).
  - Se trabaja con tipos primitivos y se desea evitar el boxing sin usar las especializaciones estándar.
  - Se tiene una restricción de compatibilidad con versiones anteriores a Java 8.

En la práctica, la API de `java.util.function` es lo suficientemente completa para la mayoría de escenarios, y su uso es consistente con las prácticas estándar de Java moderno. Por tanto, a menos que haya una razón específica, se recomienda utilizar las interfaces funcionales predefinidas.


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

```java
import java.util.Arrays;
import java.util.List;

public class EjemploForEach {
    public static void main(String[] args) {
        // Lista de enteros con algunos positivos, negativos y cero
        List<Integer> numeros = Arrays.asList(-5, 0, 3, -2, 8, -1, 10);
        
        // Versión tradicional con bucle for-each
        System.out.println("=== Versión tradicional ===");
        for (Integer n : numeros) {
            if (n > 0) {
                System.out.println(n + " es positivo");
            }
        }
        
        // Versión funcional con List.forEach usando lambda
        System.out.println("\n=== Versión funcional con lambda ===");
        numeros.forEach(n -> {
            if (n > 0) {
                System.out.println(n + " es positivo");
            }
        });
        
        // Versión funcional más concisa con referencia a método y filtro previo (usando Stream)
        System.out.println("\n=== Versión con Stream (más declarativa) ===");
        numeros.stream()
               .filter(n -> n > 0)
               .forEach(n -> System.out.println(n + " es positivo"));
        
        // Versión con Predicate y Consumer separados (más explícito)
        System.out.println("\n=== Versión con variables separadas ===");
        java.util.function.Predicate<Integer> esPositivo = n -> n > 0;
        java.util.function.Consumer<Integer> mostrarMensaje = n -> System.out.println(n + " es positivo");
        
        numeros.stream()
               .filter(esPositivo)
               .forEach(mostrarMensaje);
    }
}
```

**Salida del programa**:
```
=== Versión tradicional ===
3 es positivo
8 es positivo
10 es positivo

=== Versión funcional con lambda ===
3 es positivo
8 es positivo
10 es positivo

=== Versión con Stream (más declarativa) ===
3 es positivo
8 es positivo
10 es positivo

=== Versión con variables separadas ===
3 es positivo
8 es positivo
10 es positivo
```

**Explicación de `List.forEach`**:

El método `forEach` de `List` (y de `Iterable` en general) es una **operación terminal** que recibe un `Consumer<? super T>` (una interfaz funcional) y aplica esa acción a cada elemento de la colección. Su definición es:

```java
default void forEach(Consumer<? super T> action)
```

La lambda `n -> { if (n > 0) System.out.println(n + " es positivo"); }` es compatible con `Consumer<Integer>` porque:
- `Consumer` tiene un único método abstracto: `void accept(T t)`
- La lambda recibe un parámetro (`n`) y devuelve `void` (no tiene `return` explícito)

**Ventajas del enfoque funcional**:

1. **Declarativo vs Imperativo**: El bucle `for` tradicional dice *cómo* iterar (índice, condición, incremento). `forEach` dice *qué* hacer con cada elemento, ocultando el mecanismo de iteración.
2. **Composición**: Se puede combinar `forEach` con otras operaciones como `filter`, `map`, `sorted`, etc. (como se muestra con `stream()`).
3. **Concisión**: Menos código boilerplate.
4. **Paralelización**: Cambiando `stream()` por `parallelStream()`, la misma lógica puede ejecutarse en múltiples hilos sin modificar el código de la acción.
5. **Expresividad**: La intención del código se lee más claramente ("para cada número, si es positivo, muestra un mensaje").

**Limitaciones**:
- No se puede modificar la estructura de la colección mientras se itera (como en los bucles for-each tradicionales, lanzaría `ConcurrentModificationException`).
- Para operaciones con efectos secundarios (como imprimir), `forEach` es adecuado, pero para transformaciones o reducciones se prefieren `map`, `reduce`, `collect`, etc.

**Comparación con otras formas**:

| Método | Ventaja | Desventaja |
|--------|---------|-------------|
| `for (int i=0; i<lista.size(); i++)` | Control explícito del índice | Verboso, propenso a errores de índices |
| `for (T elemento : lista)` | Sintaxis limpia, legible | No se puede acceder al índice fácilmente |
| `lista.forEach(lambda)` | Funcional, componible, paralelizable | Menos familiar para programadores imperativos |

La introducción de `forEach` (junto con la API `Stream`) representa un cambio de paradigma en Java, permitiendo escribir código más expresivo y cercano a la programación funcional, sin abandonar la orientación a objetos cuando sea más adecuada.

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

La firma `void forEach(Consumer<? super T> action)` utiliza `Consumer<? super T>` en lugar de `Consumer<T>` para **maximizar la flexibilidad** del método. El uso de `? super T` (contravarianza) permite que `forEach` acepte un `Consumer` que pueda manejar no solo elementos de tipo `T`, sino también de cualquier **superclase** de `T`. Por ejemplo, si se tiene una `List<Integer>`, `forEach` puede aceptar un `Consumer<Number>` (ya que `Number` es superclase de `Integer`), o incluso un `Consumer<Object>`. Esto es útil porque el consumidor solo necesita leer el elemento y realizar una acción (como `System.out.println` que acepta `Object`); no necesita conocer el tipo exacto `T`. Gracias a la contravarianza, se pueden reutilizar consumidores más genéricos en colecciones de tipos específicos.

**PECS** es un acrónimo mnemotécnico que significa **"Producer Extends, Consumer Super"** y resume las reglas para usar wildcards en genéricos en Java:
- **Producer Extends**: Si un parámetro de tipo **produce** (devuelve) elementos de tipo `T`, se debe usar `? extends T` (covarianza). Ejemplo: `List<? extends Number>`: se puede leer elementos como `Number`, pero no se pueden añadir (excepto `null`).
- **Consumer Super**: Si un parámetro de tipo **consume** (recibe/usa) elementos de tipo `T`, se debe usar `? super T` (contravarianza). Ejemplo: `Consumer<? super Integer>`: se puede consumir enteros, pero al leer solo se garantiza `Object`.

Aplicando PECS al método `forEach`: `Consumer` es un **consumidor** (su método `accept` recibe un valor, no produce), por tanto se usa `? super T`. Aplicando PECS al método `transformar` (o `Function<T,R>`): `Function` es **productor y consumidor a la vez**:
- El primer parámetro de tipo `T` es **consumido** por `apply` (entrada), por lo que en contextos donde se quiera máxima flexibilidad para el tipo de entrada, se debería usar `? super T`.
- El segundo parámetro de tipo `R` es **producido** por `apply` (salida), por lo que se debería usar `? extends R`.

**Ejemplo mejorado del método `transformar` aplicando PECS**:

```java
import java.util.function.Function;
import java.util.List;
import java.util.Arrays;

public class EjemploPECS {
    
    // Método transformador genérico aplicando PECS
    // La función puede consumir cualquier supertipo de T (Consumer Super)
    // y producir cualquier subtipo de R (Producer Extends)
    public static <T, R> List<R> transformar(List<? extends T> lista, 
                                             Function<? super T, ? extends R> funcion) {
        // List<? extends T> es Producer de T (solo lectura)
        // Function<? super T, ? extends R> consume T y produce R
        List<R> resultado = new java.util.ArrayList<>();
        for (T elemento : lista) {
            resultado.add(funcion.apply(elemento));
        }
        return resultado;
    }
    
    public static void main(String[] args) {
        // Ejemplo con números
        List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5);
        
        // La función puede consumir Number (super de Integer) y producir String
        Function<Number, String> formateador = n -> "Valor: " + n;
        
        // Aunque la lista es de Integer y la función espera Number (supertipo)
        List<String> cadenas = transformar(numeros, formateador);
        System.out.println(cadenas);  // [Valor: 1, Valor: 2, Valor: 3, Valor: 4, Valor: 5]
        
        // También funciona con función específica para Integer
        Function<Integer, Double> raiz = i -> Math.sqrt(i);
        List<Double> raices = transformar(numeros, raiz);
        System.out.println(raices);
        
        // Demostración de por qué se necesita ? super T en el transformador
        // Si la firma fuera Function<T, R>, esto no funcionaría con formateador que espera Number
    }
}
```

**Por qué PECS mejora el ejemplo de `transformar`**:

1. **Entrada (`List<? extends T>`)**: La lista es un **productor** de elementos de tipo `T`. Usar `? extends T` permite pasar listas de cualquier subtipo de `T` (por ejemplo, `List<Integer>` donde `T=Number`). Esto es seguro porque solo se leerá de la lista, no se escribirá.

2. **Función (`Function<? super T, ? extends R>`)**: 
   - El primer tipo (entrada) es **consumido** por la función. Por tanto, `? super T` permite pasar una función que acepte un tipo más general (superclase) que `T`. En el ejemplo, `Function<Number, String>` puede consumir `Number`, que es superclase de `Integer`.
   - El segundo tipo (salida) es **producido** por la función. Por tanto, `? extends R` permite que la función devuelva un subtipo de `R`, lo que da flexibilidad al productor.

**Resumen de PECS**:

| Rol | Wildcard | Ejemplo | Operaciones permitidas |
|-----|----------|---------|------------------------|
| **Producer** | `? extends T` | `List<? extends Number>` | Se puede leer (`Number n = list.get(0)`). No se puede escribir (excepto `null`). |
| **Consumer** | `? super T` | `Consumer<? super Integer>` | Se puede escribir (`consumer.accept(5)`). No se puede leer como `Integer` (solo como `Object`). |
| **Both (Producer & Consumer)** | No wildcard (tipo concreto) | `List<T>` o `Function<T,T>` | Se puede leer y escribir, pero con tipo fijo. |

Aplicar PECS correctamente en APIs genéricas es fundamental para lograr la máxima flexibilidad y reutilización sin sacrificar la seguridad de tipos. El método `forEach` de `List` y el método `transformar` mejorado son ejemplos canónicos de aplicación de este principio.

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

## Ejemplo en JavaScript

```javascript
// Clase Persona (definición con sintaxis de clase)
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }
    
    saludar() {
        console.log(`Hola, soy ${this.nombre}`);
    }
}

// Crear una instancia
const ana = new Persona("Ana");

// Obtener referencia al método saludar (¡cuidado con el contexto de 'this'!)
const referenciaSaludar = ana.saludar;

// Invocar la función mediante la referencia (pierde el 'this' original)
// referenciaSaludar(); // "Hola, soy undefined" (problema clásico de JavaScript)

// Forma correcta: bind para mantener el contexto
const referenciaBind = ana.saludar.bind(ana);
referenciaBind(); // "Hola, soy Ana"

// En JavaScript moderno, también se puede usar una arrow function que captura 'this'
const referenciaArrow = () => ana.saludar();
referenciaArrow(); // "Hola, soy Ana"

// También se puede obtener referencia al método de la clase sin instancia (método estático sería diferente)
```

## Ejemplo en Java

```java
import java.util.function.Consumer;

public class Persona {
    private final String nombre;
    
    public Persona(String nombre) {
        this.nombre = nombre;
    }
    
    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
    
    public static void main(String[] args) {
        // Crear una instancia
        Persona ana = new Persona("Ana");
        
        // Referencia a método de instancia (objeto específico)
        // Sintaxis: objeto::nombreMetodo
        Runnable referenciaSaludar = ana::saludar;
        
        // Invocar mediante la referencia
        referenciaSaludar.run();  // "Hola, soy Ana"
        
        // También se puede usar con Consumer si el método aceptara parámetros
        // Por ejemplo, supongamos un método saludarCon(String saludo)
        // Consumer<String> saludarCon = ana::saludarCon;
        
        // Referencia a método de instancia de un tipo arbitrario (requiere un objeto como parámetro)
        // Por ejemplo, para ser usado en Stream.map
        java.util.function.Function<String, Persona> creador = Persona::new;  // referencia a constructor
        Persona juan = creador.apply("Juan");
        juan.saludar();  // "Hola, soy Juan"
        
        // Referencia a método de clase (estático)
        // Sintaxis: Clase::nombreMetodoEstatico
        java.util.function.Function<String, Integer> aLongitud = String::length;
        System.out.println(aLongitud.apply("Hola"));  // 4
    }
}
```

## Comparación entre lenguajes

**JavaScript**:
- Las referencias a métodos son **funciones independientes** que pierden el contexto `this` al ser extraídas.
- Es necesario usar `.bind()`, arrow functions, o closures para preservar el contexto.
- Las funciones son ciudadanos de primera clase, por lo que pasar métodos como referencias es muy común.

**Java**:
- Las referencias a métodos son **objetos de interfaces funcionales** (`Runnable`, `Function`, `Consumer`, etc.).
- No hay problema de contexto: la referencia `ana::saludar` captura tanto el objeto (`ana`) como el método.
- La sintaxis es `objeto::metodo` para métodos de instancia, `Clase::metodo` para métodos estáticos, y `Clase::new` para constructores.
- Las referencias a métodos son una alternativa más concisa a las lambdas cuando el método ya existe (`s -> s.length()` se convierte en `String::length`).

## Formas de referencia a métodos en Java

| Tipo | Sintaxis | Ejemplo | Equivalente lambda |
|------|----------|---------|-------------------|
| Método estático | `Clase::metodoEstatico` | `Math::sqrt` | `x -> Math.sqrt(x)` |
| Método de instancia de un objeto específico | `objeto::metodoInstancia` | `ana::saludar` | `() -> ana.saludar()` |
| Método de instancia de un tipo arbitrario | `Clase::metodoInstancia` | `String::length` | `s -> s.length()` |
| Constructor | `Clase::new` | `Persona::new` | `nombre -> new Persona(nombre)` |

**Ejemplo adicional con Stream** (demostrando la potencia de las referencias a métodos):

```java
import java.util.List;
import java.util.Arrays;

public class EjemploReferenciasMetodos {
    public static void main(String[] args) {
        List<String> nombres = Arrays.asList("ana", "luis", "carlos");
        
        // Con lambda
        nombres.stream().map(s -> s.toUpperCase()).forEach(s -> System.out.println(s));
        
        // Con referencias a métodos (más conciso)
        nombres.stream().map(String::toUpperCase).forEach(System.out::println);
        // Explicación: 
        // - String::toUpperCase es referencia a método de instancia de tipo arbitrario
        // - System.out::println es referencia a método de instancia de objeto específico (System.out)
    }
}
```

Las referencias a métodos son **azúcar sintáctico** que hace que el código sea más legible y expresivo, especialmente cuando se combinan con la API `Stream`. En Java, no se puede acceder a una referencia de método "cruda" como en JavaScript; siempre están vinculadas a una interfaz funcional específica (como `Runnable`, `Consumer`, etc.). Esto proporciona seguridad de tipos, pero también limita la flexibilidad comparado con JavaScript, donde las referencias a métodos son funciones de primera clase más puras.


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

En Java existen **cuatro tipos** de referencias a métodos, cada una con una sintaxis específica y un propósito diferente. Estas referencias son una forma más concisa de escribir expresiones lambda cuando el cuerpo de la lambda ya existe como un método nombrado. Todas ellas se basan en interfaces funcionales y son verificadas estáticamente por el compilador.

## 1. Referencia a método estático
**Sintaxis**: `Clase::metodoEstatico`

```java
import java.util.function.Function;

public class EjemploReferencias {
    // Método estático de ejemplo
    public static String formatearMayusculas(String texto) {
        return texto.toUpperCase();
    }
    
    public static void main(String[] args) {
        // Usando lambda
        Function<String, String> conLambda = s -> formatearMayusculas(s);
        
        // Usando referencia a método estático (más conciso)
        Function<String, String> conReferencia = EjemploReferencias::formatearMayusculas;
        
        System.out.println(conReferencia.apply("hola"));  // "HOLA"
        
        // Ejemplo con método estático de la API estándar
        Function<Double, Double> raizCuadrada = Math::sqrt;
        System.out.println(raizCuadrada.apply(25.0));  // 5.0
    }
}
```

## 2. Referencia a constructor
**Sintaxis**: `Clase::new`

```java
import java.util.function.Function;
import java.util.function.Supplier;
import java.util.function.BiFunction;

public class EjemploReferenciaConstructor {
    static class Persona {
        private final String nombre;
        private final int edad;
        
        // Constructor sin parámetros
        public Persona() {
            this.nombre = "Anónimo";
            this.edad = 0;
        }
        
        // Constructor con un parámetro
        public Persona(String nombre) {
            this.nombre = nombre;
            this.edad = 0;
        }
        
        // Constructor con dos parámetros
        public Persona(String nombre, int edad) {
            this.nombre = nombre;
            this.edad = edad;
        }
        
        @Override
        public String toString() {
            return nombre + " (" + edad + " años)";
        }
    }
    
    public static void main(String[] args) {
        // Referencia a constructor sin argumentos (Supplier)
        Supplier<Persona> creadorVacio = Persona::new;
        Persona p1 = creadorVacio.get();
        System.out.println(p1);  // "Anónimo (0 años)"
        
        // Referencia a constructor con 1 argumento (Function)
        Function<String, Persona> creadorConNombre = Persona::new;
        Persona p2 = creadorConNombre.apply("Ana");
        System.out.println(p2);  // "Ana (0 años)"
        
        // Referencia a constructor con 2 argumentos (BiFunction)
        BiFunction<String, Integer, Persona> creadorCompleto = Persona::new;
        Persona p3 = creadorCompleto.apply("Luis", 30);
        System.out.println(p3);  // "Luis (30 años)"
    }
}
```

## 3. Referencia a método de instancia de una instancia concreta
**Sintaxis**: `instancia::metodoInstancia` (captura el objeto específico)

```java
import java.util.function.Consumer;
import java.util.function.Supplier;

public class EjemploReferenciaInstanciaConcreta {
    static class Calculadora {
        private final int factor;
        
        public Calculadora(int factor) {
            this.factor = factor;
        }
        
        public int multiplicar(int valor) {
            return valor * factor;
        }
        
        public void mostrar(String mensaje) {
            System.out.println(mensaje + " -> " + this);
        }
        
        @Override
        public String toString() {
            return "Calculadora{factor=" + factor + "}";
        }
    }
    
    public static void main(String[] args) {
        Calculadora calc5 = new Calculadora(5);
        Calculadora calc10 = new Calculadora(10);
        
        // Referencia al método multiplicar de un objeto específico (calc5)
        // La lambda sería: x -> calc5.multiplicar(x)
        java.util.function.Function<Integer, Integer> multiplicarPor5 = calc5::multiplicar;
        System.out.println(multiplicarPor5.apply(3));  // 15
        
        // Referencia al método mostrar de calc10 (Consumer de String)
        Consumer<String> mostrarMensaje = calc10::mostrar;
        mostrarMensaje.accept("Resultado");  // "Resultado -> Calculadora{factor=10}"
        
        // También con Runnable si el método no tiene parámetros
        // Supongamos que Calculadora tuviera un método void reiniciar()
        // Runnable reiniciar = calc5::reiniciar;
    }
}
```

## 4. Referencia a método de instancia sobre cualquier instancia
**Sintaxis**: `Clase::metodoInstancia` (el primer parámetro de la lambda se convierte en el receptor)

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.BiFunction;
import java.util.function.Function;

public class EjemploReferenciaInstanciaCualquiera {
    
    static class Punto {
        private final double x;
        private final double y;
        
        public Punto(double x, double y) {
            this.x = x;
            this.y = y;
        }
        
        public double distanciaAlOrigen() {
            return Math.sqrt(x*x + y*y);
        }
        
        public double distanciaA(Punto otro) {
            double dx = this.x - otro.x;
            double dy = this.y - otro.y;
            return Math.sqrt(dx*dx + dy*dy);
        }
        
        @Override
        public String toString() {
            return "(" + x + "," + y + ")";
        }
    }
    
    public static void main(String[] args) {
        // Referencia a método de instancia sobre cualquier String (el objeto es el primer parámetro)
        // Equivalente a lambda: s -> s.toUpperCase()
        Function<String, String> aMayusculas = String::toUpperCase;
        System.out.println(aMayusculas.apply("hola"));  // "HOLA"
        
        // Referencia a método de instancia con parámetros
        // Equivalente a lambda: (p1, p2) -> p1.distanciaA(p2)
        BiFunction<Punto, Punto, Double> distancia = Punto::distanciaA;
        
        Punto p1 = new Punto(0, 0);
        Punto p2 = new Punto(3, 4);
        System.out.println(distancia.apply(p1, p2));  // 5.0
        
        // Uso en Stream (típico ejemplo)
        List<String> palabras = Arrays.asList("hola", "mundo", "java");
        
        // Convertir cada String a su longitud usando referencia a método de instancia
        palabras.stream()
               .map(String::length)  // Referencia a método de instancia (cualquier String)
               .forEach(System.out::println);  // 4, 5, 4
    }
}
```

## Resumen y tabla comparativa

| Tipo | Sintaxis | Ejemplo | Equivalente lambda | Contexto del `this`/objeto |
|------|----------|---------|-------------------|---------------------------|
| Estático | `Clase::metodoEstatico` | `Math::sqrt` | `x -> Math.sqrt(x)` | No aplica |
| Constructor | `Clase::new` | `Persona::new` | `() -> new Persona()` o `x -> new Persona(x)` | No aplica |
| Instancia específica | `objeto::metodo` | `calc5::multiplicar` | `x -> calc5.multiplicar(x)` | Objeto capturado al crear referencia |
| Instancia cualquiera | `Clase::metodoInstancia` | `String::length` | `s -> s.length()` | Primer parámetro de la lambda |

**Reglas prácticas**:
- Si la lambda es `(args) -> objeto.metodo(args)`, se puede usar `objeto::metodo`.
- Si la lambda es `(param1, otros) -> param1.metodo(otros)`, se puede usar `TipoDeParam1::metodo`.
- Si la lambda es `() -> new Clase(args)`, se puede usar `Clase::new`.
- Si la lambda es `(args) -> Clase.metodoEstatico(args)`, se puede usar `Clase::metodoEstatico`.

Las referencias a métodos hacen que el código sea más legible y expresivo, especialmente en combinación con `Stream` y otras APIs funcionales, y son siempre preferibles a una lambda equivalente cuando el método ya existe, ya que comunican mejor la intención y eliminan ruido sintáctico.


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

```java
import java.util.*;

public class OrdenarPersonas {
    
    static class Persona {
        private final String nombre;
        private final int edad;
        
        public Persona(String nombre, int edad) {
            this.nombre = nombre;
            this.edad = edad;
        }
        
        public String getNombre() { return nombre; }
        public int getEdad() { return edad; }
        
        @Override
        public String toString() {
            return nombre + " (" + edad + " años)";
        }
    }
    
    public static void main(String[] args) {
        // Lista inicial de personas
        List<Persona> personas = new ArrayList<>();
        personas.add(new Persona("Ana", 25));
        personas.add(new Persona("Luis", 30));
        personas.add(new Persona("Carlos", 25));
        personas.add(new Persona("Ana", 22));
        personas.add(new Persona("Beatriz", 30));
        
        // ========== VERSIÓN 1: Comparador manual con lógica completa ==========
        List<Persona> copia1 = new ArrayList<>(personas);
        Collections.sort(copia1, (p1, p2) -> {
            // Primero por edad
            int comparacionEdad = Integer.compare(p1.getEdad(), p2.getEdad());
            if (comparacionEdad != 0) {
                return comparacionEdad;
            }
            // Si misma edad, por nombre alfabético
            return p1.getNombre().compareTo(p2.getNombre());
        });
        
        System.out.println("=== Versión manual (lambda con bloque) ===");
        copia1.forEach(System.out::println);
        
        // ========== VERSIÓN 2: Usando Comparator con métodos encadenados ==========
        List<Persona> copia2 = new ArrayList<>(personas);
        Comparator<Persona> comparador = Comparator
            .comparingInt(Persona::getEdad)          // Primero por edad
            .thenComparing(Persona::getNombre);       // Luego por nombre
        
        Collections.sort(copia2, comparador);
        
        System.out.println("\n=== Versión con Comparator encadenado ===");
        copia2.forEach(System.out::println);
        
        // ========== VERSIÓN 3: Aún más concisa con List.sort (Java 8+) ==========
        List<Persona> copia3 = new ArrayList<>(personas);
        copia3.sort(Comparator
            .comparingInt(Persona::getEdad)
            .thenComparing(Persona::getNombre));
        
        System.out.println("\n=== Versión con List.sort y Comparator estático ===");
        copia3.forEach(System.out::println);
        
        // ========== VERSIÓN 4: Orden inverso (edad descendente) ==========
        List<Persona> copia4 = new ArrayList<>(personas);
        copia4.sort(Comparator
            .comparingInt(Persona::getEdad)
            .reversed()
            .thenComparing(Persona::getNombre));
        
        System.out.println("\n=== Edad descendente, luego nombre ascendente ===");
        copia4.forEach(System.out::println);
    }
}
```

**Salida del programa**:

```
=== Versión manual (lambda con bloque) ===
Ana (22 años)
Ana (25 años)
Carlos (25 años)
Beatriz (30 años)
Luis (30 años)

=== Versión con Comparator encadenado ===
Ana (22 años)
Ana (25 años)
Carlos (25 años)
Beatriz (30 años)
Luis (30 años)

=== Versión con List.sort y Comparator estático ===
Ana (22 años)
Ana (25 años)
Carlos (25 años)
Beatriz (30 años)
Luis (30 años)

=== Edad descendente, luego nombre ascendente ===
Beatriz (30 años)
Luis (30 años)
Ana (25 años)
Carlos (25 años)
Ana (22 años)
```

## Explicación de las versiones

**Versión 1 (manual con lambda de bloque)**:
- Se usa `Collections.sort(lista, lambda)` donde la lambda implementa `Comparator<Persona>`.
- La lambda contiene lógica explícita: primero compara edades con `Integer.compare()`, y si son iguales, compara nombres con `compareTo()`.
- El bloque `{ ... }` permite múltiples sentencias y `return` explícito.

**Versión 2 (Comparator encadenado)**:
- Se construye un `Comparator<Persona>` mediante métodos estáticos y default de la interfaz `Comparator`.
- `Comparator.comparingInt(Persona::getEdad)` crea un comparador que ordena por edad (ascendente).
- `.thenComparing(Persona::getNombre)` añade un criterio secundario (por nombre) que solo se usa si las edades son iguales.
- Es más declarativo: dice **qué** comparar, no **cómo** comparar.

**Ventajas del enfoque funcional con Comparator**:
1. **Legibilidad**: El código expresa directamente la intención ("ordenar por edad y luego por nombre").
2. **Reutilización**: El comparador se puede almacenar en una variable estática y usar en múltiples lugares.
3. **Composición**: Los comparadores se pueden encadenar (`thenComparing`), combinar (`reversed()`, `thenComparingInt`, etc.).
4. **Seguridad de tipos**: `comparingInt` evita el *boxing* innecesario (usa `int` primitivo).
5. **Métodos de fábrica**: `Comparator` ofrece métodos como `naturalOrder()`, `nullsFirst()`, `nullsLast()`, etc.

**Métodos útiles de `Comparator`** (Java 8+):

| Método | Descripción | Ejemplo |
|--------|-------------|---------|
| `comparing(Function<T,U>)` | Crea comparador por una clave (con boxing) | `Comparator.comparing(Persona::getNombre)` |
| `comparingInt(ToIntFunction<T>)` | Crea comparador por clave `int` (sin boxing) | `Comparator.comparingInt(Persona::getEdad)` |
| `comparingLong`, `comparingDouble` | Similar para `long` y `double` | `Comparator.comparingLong(Producto::getCodigo)` |
| `thenComparing(Comparator)` | Añade criterio secundario | `primero.thenComparing(Persona::getNombre)` |
| `reversed()` | Invierte el orden | `comparador.reversed()` |
| `nullsFirst(Comparator)` | Maneja valores nulos (primeros) | `Comparator.nullsFirst(naturalOrder())` |

**Versión adicional con orden descendente**:

```java
// Ordenar por edad descendente, luego nombre ascendente
copia4.sort(Comparator
    .comparingInt(Persona::getEdad)
    .reversed()
    .thenComparing(Persona::getNombre));
```

**Nota histórica**: `Collections.sort(lista, comparador)` fue la forma estándar hasta Java 7. Desde Java 8, `List` tiene su propio método `sort(Comparator)` que es más natural y conciso (`lista.sort(comparador)`). Además, los comparadores construidos con métodos de fábrica son más expresivos y menos propensos a errores que las implementaciones manuales, especialmente en criterios de ordenación múltiples o con tratamientos especiales (nulos, reversos, etc.). Siempre que sea posible, se recomienda usar `Comparator.comparing` y sus derivados en lugar de lambdas manuales con lógica condicional.