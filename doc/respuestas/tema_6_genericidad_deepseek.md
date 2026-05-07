# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

En C, se puede utilizar `void*` (puntero a cualquier tipo) para implementar una estructura de datos genérica. A continuación se muestra un ejemplo de un contenedor simple basado en array que puede almacenar cualquier tipo de dato:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    void** elementos;  // Array de punteros a void
    int capacidad;
    int cantidad;
} Contenedor;

Contenedor* crearContenedor(int capacidad) {
    Contenedor* c = (Contenedor*)malloc(sizeof(Contenedor));
    c->elementos = (void**)malloc(capacidad * sizeof(void*));
    c->capacidad = capacidad;
    c->cantidad = 0;
    return c;
}

void agregar(Contenedor* c, void* elemento) {
    if (c->cantidad < c->capacidad) {
        c->elementos[c->cantidad++] = elemento;
    }
}

void* obtener(Contenedor* c, int indice) {
    if (indice >= 0 && indice < c->cantidad) {
        return c->elementos[indice];
    }
    return NULL;
}
```

En Java, se puede utilizar `Object` (la clase base de todos los objetos) para lograr un efecto similar. Cualquier objeto, al ser un subtipo de `Object`, puede ser almacenado en un array de `Object`. El siguiente ejemplo muestra un contenedor genérico basado en array primitivo:

```java
public class ContenedorObject {
    private Object[] elementos;
    private int cantidad;
    private static final int CAPACIDAD_INICIAL = 10;

    public ContenedorObject() {
        this.elementos = new Object[CAPACIDAD_INICIAL];
        this.cantidad = 0;
    }

    public void agregar(Object elemento) {
        if (cantidad == elementos.length) {
            // Redimensionar el array (simplificado)
            Object[] nuevo = new Object[elementos.length * 2];
            System.arraycopy(elementos, 0, nuevo, 0, cantidad);
            elementos = nuevo;
        }
        elementos[cantidad++] = elemento;
    }

    public Object obtener(int indice) {
        if (indice >= 0 && indice < cantidad) {
            return elementos[indice];
        }
        return null;
    }

    public int tamaño() {
        return cantidad;
    }
}
```

Ambos enfoques comparten la misma limitación: **pérdida de información de tipo**. Tanto `void*` en C como `Object` en Java obligan al programador a realizar conversiones explícitas (casts) al recuperar un elemento para usarlo con su tipo original. En C, se debe hacer `(Tipo*)obtener(contenedor, i)`, y en Java, `(Tipo) contenedor.obtener(i)`. Si se comete un error y se convierte a un tipo incorrecto, en C el comportamiento es indefinido (posible fallo silencioso), mientras que en Java se lanza una excepción en tiempo de ejecución (`ClassCastException`). Además, los tipos primitivos en Java (como `int`, `double`) no pueden almacenarse directamente en un `ContenedorObject` porque no son objetos; deben ser envueltos en sus clases wrapper (`Integer`, `Double`), lo que añade sobrecarga de memoria y rendimiento. La genericidad en Java (introducida en Java 5) resuelve estos problemas proporcionando **seguridad de tipos en tiempo de compilación** y eliminando la necesidad de casts explícitos, como se verá en preguntas posteriores.

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica?

La **programación genérica** es un paradigma que permite escribir código (clases, interfaces y métodos) parametrizado por **tipos**, de manera que el mismo código pueda funcionar con diferentes tipos de datos sin perder la seguridad de tipos en tiempo de compilación. El objetivo es poder reutilizar algoritmos y estructuras de datos para múltiples tipos, trasladando la detección de errores de tipos desde tiempo de ejecución (como las excepciones `ClassCastException`) a tiempo de compilación, y eliminando la necesidad de conversiones explícitas (casts) que ensucian el código y son propensas a errores.

El ejemplo anterior (con `void*` en C o `Object` en Java) es **un ejemplo básico de programación parametrizada por tipos**, pero **no es programación genérica en el sentido moderno y seguro del término**. Se trata de una aproximación mediante **polimorfismo universal** (todos los tipos son subtipos de `void*` o de `Object`), que sacrifica la seguridad de tipos (type safety). En estos enfoques, el programador es responsable de recordar qué tipo se almacenó en cada posición y realizar el cast correspondiente, con el riesgo de errores que solo se detectan en tiempo de ejecución (si es que se detectan). Este método es propenso a fallos y se considera una técnica de bajo nivel o heredada de lenguajes no genéricos.

La programación genérica verdadera, como la implementada en Java a partir de la versión 5 (con el mecanismo de **tipos parametrizados**), C++ (con **plantillas**), C# (con **genéricos**) o Rust, permite declarar clases como `Contenedor<T>` donde `T` es un parámetro de tipo. Al instanciar el contenedor con un tipo concreto (por ejemplo, `Contenedor<String>`), el compilador verifica que solo se agreguen `String`, devuelve `String` sin necesidad de cast, y rechaza cualquier intento de usar un tipo incorrecto. Esto proporciona **seguridad de tipos en tiempo de compilación** y **legibilidad** del código, siendo la principal ventaja de la programación genérica sobre el uso de `Object`. Por tanto, el ejemplo con `Object` es un precursor no seguro de la genericidad, útil para entender el problema que resuelven los genéricos.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas.

El principal problema de emplear `void*` en C o `Object` en Java para crear estructuras de datos genéricas es la **pérdida de información de tipo en tiempo de compilación**, lo que traslada la responsabilidad del chequeo de tipos al programador y a tiempo de ejecución. El compilador no puede verificar si el tipo real de los objetos almacenados coincide con el tipo esperado cuando se recuperan. Esto significa que cualquier error de tipo (por ejemplo, almacenar un `Integer` y luego tratarlo como `String`) solo se detecta cuando el programa ya está ejecutándose. En Java, esto produce una `ClassCastException` en el momento del cast; en C, el comportamiento es indefinido (desde resultados incorrectos hasta fallos de memoria silenciosos o corrupción de datos), lo cual es mucho más peligroso.

Un segundo problema es la **verbosidad y propensión a errores del código cliente**. Cada vez que se recupera un elemento de la estructura, el programador debe realizar un **cast explícito** al tipo deseado, lo que ensucia el código y duplica información de tipo. Por ejemplo, `String s = (String) contenedor.obtener(i);`. Si el programador se equivoca y escribe un cast incorrecto (por ejemplo, `Integer` en lugar de `String`), el compilador no lo detecta y se produce un error en tiempo de ejecución. Esto es especialmente problemático en sistemas grandes o cuando múltiples programadores trabajan sobre el mismo contenedor, ya que no hay una forma documentada y verificable por el compilador de qué tipo de objetos contiene realmente la estructura.

Un tercer problema es la **falta de restricciones de tipos en tiempo de inserción**. Un contenedor basado en `Object` permite almacenar cualquier mezcla de tipos: un `Integer`, un `String`, una `Lista`, etc., todos en la misma instancia. Aunque a veces esto es deseable (por ejemplo, para heterogeneidad controlada), en la mayoría de los casos se desea que un contenedor sea **homogéneo** (contenga solo elementos de un tipo específico). Con `Object`, no hay forma de declarar esa intención; el programador debe confiar en la disciplina del equipo o añadir comprobaciones manuales en tiempo de ejecución (con `instanceof`), lo que es costoso y propenso a omisiones. Además, en Java, el uso de `Object` con tipos primitivos requiere **boxing/unboxing** manual (envolver `int` en `Integer`, etc.), con la correspondiente penalización de rendimiento y consumo de memoria adicional. Los genéricos resuelven estos problemas permitiendo que el contenedor sea parametrizado por un tipo, de forma que el compilador verifica la coherencia de tipos en tiempo de compilación y elimina la necesidad de casts explícitos.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**?

Los **parámetros de tipo** (type parameters) son marcadores que actúan como "variables de tipo" en la definición de clases, interfaces o métodos genéricos. En Java, se declaran entre ángulos (`<>`) justo después del nombre de la clase o del método, y convencionalmente se nombran con una sola letra mayúscula (como `T` de Type, `E` de Element, `K` de Key, `V` de Value, etc.). Permiten que el código se escriba de forma genérica, es decir, sin especificar los tipos concretos que utilizará, posponiendo esa decisión hasta el momento de la instanciación o invocación. Esta parametrización es lo que permite a una misma definición de clase funcionar con múltiples tipos diferentes, manteniendo la seguridad de tipos en tiempo de compilación.

Por ejemplo, al definir `public class Contenedor<T> { ... }`, el parámetro `T` representa un tipo desconocido que se concretará más adelante, cuando el código cliente cree una instancia (por ejemplo, `Contenedor<String> c = new Contenedor<>();`). Dentro de la clase, se puede declarar atributos de tipo `T`, métodos que reciben o devuelven `T`, etc. El compilador de Java trata `T` como un tipo real, aplicando todas las comprobaciones de tipos correspondientes: no se puede asignar un `String` a un atributo `T` si `T` se ha instanciado como `Integer`, y se rechazarán usos incorrectos en tiempo de compilación. Además, se elimina la necesidad de casts explícitos al recuperar elementos del contenedor.

Los parámetros de tipo son la base de la genericidad en Java. A diferencia del uso de `Object`, donde cualquier objeto puede almacenarse y el programador debe recordar y castear manualmente, los parámetros de tipo trasladan la responsabilidad de la gestión de tipos al compilador, que puede verificar que el código es consistente y generar errores antes de que el programa se ejecute. Esto mejora drásticamente la robustez, la legibilidad y la mantenibilidad del código, siendo la razón por la que las colecciones estándar de Java (como `ArrayList`, `HashMap`, etc.) están parametrizadas con tipos desde Java 5. Es importante señalar que los parámetros de tipo en Java solo aceptan **tipos referencia** (objetos), no tipos primitivos como `int` o `double` (para estos se deben usar sus wrappers: `Integer`, `Double`). El mecanismo subyacente se implementa mediante **type erasure** (borrado de tipos), que se explicará en otra pregunta.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

En **Java**, los genéricos permiten crear una clase parametrizada por un tipo. El siguiente ejemplo utiliza `ArrayList<T>` (una colección estándar genérica) para almacenar exclusivamente `String`:

```java
import java.util.ArrayList;
import java.util.List;

public class EjemploGenericoJava {
    public static void main(String[] args) {
        // Instanciación con tipo concreto: String
        List<String> lista = new ArrayList<>();
        
        // Inserción de elementos (solo String, seguro en compilación)
        lista.add("Hola");
        lista.add("Mundo");
        // lista.add(123); // Error de compilación: Integer no es String
        
        // Recorrido sin necesidad de casts
        for (String s : lista) {
            System.out.println("Elemento: " + s + ", longitud: " + s.length());
        }
        
        // También con índice
        for (int i = 0; i < lista.size(); i++) {
            String elemento = lista.get(i); // No requiere cast
            System.out.println("Pos " + i + ": " + elemento);
        }
    }
}
```

En **C++**, las plantillas (templates) permiten una funcionalidad similar, aunque con un mecanismo de generación de código diferente (instanciación en tiempo de compilación). El siguiente ejemplo utiliza `std::vector<T>`:

```cpp
#include <iostream>
#include <vector>
#include <string>

int main() {
    // Instanciación con tipo concreto: std::string
    std::vector<std::string> lista;
    
    // Inserción de elementos (solo std::string)
    lista.push_back("Hola");
    lista.push_back("Mundo");
    // lista.push_back(123); // Error de compilación (excepto si hay conversión implícita)
    
    // Recorrido sin necesidad de casts
    for (const std::string& s : lista) {
        std::cout << "Elemento: " << s << ", longitud: " << s.length() << std::endl;
    }
    
    // También con índice tradicional
    for (size_t i = 0; i < lista.size(); ++i) {
        std::string elemento = lista[i]; // No requiere cast
        std::cout << "Pos " << i << ": " << elemento << std::endl;
    }
    
    return 0;
}
```

**Diferencias destacadas**: En ambos lenguajes, la genericidad proporciona **seguridad de tipos en tiempo de compilación**: cualquier intento de insertar un tipo no compatible (como un entero en una lista de `String`) produce un error de compilación. Al recuperar elementos, no se requieren casts explícitos porque el compilador ya sabe que el tipo es `String`. Sin embargo, los mecanismos subyacentes son diferentes: En Java, los genéricos se implementan mediante **type erasure** (el compilador elimina la información de tipo genérico en tiempo de ejecución, reemplazándola por `Object` y añadiendo casts automáticos), lo que permite compatibilidad con código pre-genérico pero impide ciertos usos (como `instanceof` con tipos parametrizados o crear arrays de tipos genéricos). En C++, las plantillas generan **código independiente para cada tipo de instanciación** (el compilador genera una versión especializada de la clase para `std::string`), lo que puede producir mayor tamaño de código pero permite mayor flexibilidad (por ejemplo, usar tipos primitivos directamente sin wrappers, y operadores como `+` funcionan si el tipo los soporta). Ambos enfoques cumplen el objetivo principal: escribir código genérico seguro y reutilizable.


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

Cuando se instancia una clase genérica en **Java**, el compilador aplica el mecanismo de **"type erasure"** (borrado de tipos). En tiempo de compilación, el compilador verifica que el código sea seguro respecto a los tipos (por ejemplo, que no se inserten `Integer` en una `List<String>`). Una vez verificada la corrección, el compilador **elimina toda la información de tipo genérico** reemplazando los parámetros de tipo por su **límite superior** (normalmente `Object` si no se especifica otro). Además, inserta automáticamente los **casts necesarios** donde los recupera, y añade métodos "puente" (bridge methods) para preservar el polimorfismo. El resultado es un código bytecode equivalente al que se habría escrito manualmente usando `Object` y casts, pero con la garantía de que el compilador ha verificado la seguridad de tipos. Esto significa que, en tiempo de ejecución, una `List<String>` y una `List<Integer>` son ambas simplemente `List` (sin parámetros de tipo); la JVM no conoce los argumentos de tipo. Esta decisión se tomó para mantener la **compatibilidad binaria** con código Java anterior a la versión 5 (que no tenía genéricos). Sin embargo, tiene limitaciones: no se puede hacer `instanceof` con tipos parametrizados (`if (lista instanceof List<String>)` no compila), no se pueden crear arrays de tipos genéricos (`new T[10]` no compila), y la información de tipo se pierde en tiempo de ejecución.

En **C++**, el mecanismo es completamente diferente y se denomina **instanciación de plantillas** (template instantiation). El compilador de C++ trata las plantillas como **"plantillas para generar código"**, no como tipos. Cuando se instancia una plantilla con un tipo concreto (por ejemplo, `std::vector<std::string>`), el compilador **genera una clase completamente nueva y separada** sustituyendo el parámetro de tipo `T` por `std::string` en la definición de la plantilla. Luego compila esa clase generada como si hubiera sido escrita manualmente. Si en el mismo programa se usa `std::vector<int>` y `std::vector<double>`, el compilador genera y compila **tres clases independientes**: una para `std::string`, otra para `int` y otra para `double`. Esto ocurre en tiempo de compilación y no hay pérdida de información de tipos: en tiempo de ejecución, cada vector sabe exactamente de qué tipo es y los métodos (como `operator[]`) devuelven referencias al tipo concreto sin necesidad de casts. Este enfoque ofrece mayor flexibilidad (las plantillas pueden usar operadores del tipo, como `+` o `*`, y los tipos primitivos funcionan directamente) y eficiencia (código optimizado para cada tipo), pero a costa de un mayor tiempo de compilación y mayor tamaño del código binario (cada instanciación genera código nuevo). También permite cosas imposibles en Java, como especialización de plantillas y tipos dependientes complejos.

En resumen: **Java** usa **type erasure** (un solo código bytecode, pérdida de tipos en tiempo de ejecución, verificación estática) para mantener compatibilidad binaria. **C++** usa **instanciación de plantillas** (código separado por tipo, tipos preservados en tiempo de ejecución, generación de código especializado) priorizando rendimiento y flexibilidad. Ambos enfoques son válidos para sus respectivos ecosistemas.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`.

```java
// Clase genérica Par con dos parámetros de tipo diferentes
public class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() {
        return primero;
    }

    public U getSegundo() {
        return segundo;
    }

    @Override
    public String toString() {
        return "Par{" + primero + ", " + segundo + "}";
    }
}
```

```java
// Ejemplo de uso: función que devuelve media y desviación típica en un Par
import java.util.Arrays;

public class Estadisticas {
    
    public static Par<Double, Double> calcularMediaYDesviacion(double[] datos) {
        if (datos == null || datos.length == 0) {
            throw new IllegalArgumentException("El array no puede estar vacío");
        }
        
        double suma = 0.0;
        for (double d : datos) {
            suma += d;
        }
        double media = suma / datos.length;
        
        // Cálculo de la desviación típica
        double sumaCuadrados = 0.0;
        for (double d : datos) {
            sumaCuadrados += Math.pow(d - media, 2);
        }
        double desviacion = Math.sqrt(sumaCuadrados / datos.length); // Desviación poblacional
        
        return new Par<>(media, desviacion);
    }
    
    public static void main(String[] args) {
        double[] valores = {2.0, 4.0, 6.0, 8.0, 10.0};
        Par<Double, Double> resultado = calcularMediaYDesviacion(valores);
        
        System.out.println("Datos: " + Arrays.toString(valores));
        System.out.println("Media: " + resultado.getPrimero());      // 6.0
        System.out.println("Desviación típica: " + resultado.getSegundo()); // Aprox 2.828
        
        // Los tipos están seguros: el primer getter siempre devuelve Double
        double media = resultado.getPrimero();
        double desviacion = resultado.getSegundo();
        
        // También se pueden crear Páres de otros tipos
        Par<String, Integer> persona = new Par<>("Ana", 30);
        System.out.println("Nombre: " + persona.getPrimero() + ", Edad: " + persona.getSegundo());
    }
}
```

La clase `Par<T, U>` es un ejemplo clásico de **programación genérica** con dos parámetros de tipo independientes. Permite agrupar dos valores de tipos posiblemente diferentes, manteniendo la seguridad de tipos en tiempo de compilación. El constructor recibe los dos valores, y los getters devuelven los tipos específicos (`T` y `U`) sin necesidad de casts. Esta estructura es útil para retornar múltiples valores de una función sin crear una clase específica para cada caso, o para implementar pares clave-valor, coordenadas heterogéneas, etc. Es importante notar que, debido al **type erasure**, en tiempo de ejecución no se distingue entre `Par<Double, Double>` y `Par<String, Integer>`, pero el compilador garantiza que el código las use correctamente. Para una mayor claridad, los atributos se han declarado `final`, haciendo que la clase sea inmutable, lo cual es una buena práctica para objetos que solo contienen datos. El método `calcularMediaYDesviacion` demuestra una aplicación práctica: devuelve dos valores calculados a partir del mismo conjunto de datos, ambos de tipo `Double`, aunque podrían ser de tipos diferentes (por ejemplo, `Par<Double, String>` para media y una calificación textual). Desde Java 16, se pueden usar **records** para este propósito (`record Par<T,U>(T primero, U segundo) {}`), que proporcionan automáticamente constructor, getters, `equals`, `hashCode` y `toString`, pero el ejemplo con clase tradicional es más didáctico para entender la genericidad.


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo.

```java
import java.util.Random;

public class Utilidades {
    private static final Random random = new Random();

    // Versión con Object (sin genericidad)
    public static Object seleccionaUnoObject(Object a, Object b) {
        return random.nextBoolean() ? a : b;
    }

    // Versión genérica con parámetro de tipo T
    public static <T> T seleccionaUnoGenerico(T a, T b) {
        return random.nextBoolean() ? a : b;
    }

    public static void main(String[] args) {
        // Ejemplo con la versión Object
        String str1 = "Hola";
        String str2 = "Mundo";
        
        Object resultadoObj = Utilidades.seleccionaUnoObject(str1, str2);
        // Es necesario downcasting explícito para usar métodos de String
        if (resultadoObj instanceof String) {
            String resultadoStr = (String) resultadoObj;
            System.out.println("Resultado (Object): " + resultadoStr.toUpperCase());
        }
        
        // En la versión no genérica, no se fuerza que sean del mismo tipo:
        Object mezclado = Utilidades.seleccionaUnoObject("Texto", 123); // Compila, pero peligroso
        System.out.println("Mezclado: " + mezclado); // Funciona, pero si se castea mal, error
        
        // Ejemplo con la versión genérica
        String resultadoGen = Utilidades.seleccionaUnoGenerico(str1, str2);
        // No necesita downcasting: resultadoGen es directamente String
        System.out.println("Resultado (Genérico): " + resultadoGen.toUpperCase());
        
        // Intento de llamada con tipos diferentes: error de compilación
        // String error = Utilidades.seleccionaUnoGenerico("Texto", 123); // No compila
        
        // También funciona con otros tipos
        Integer num1 = 10;
        Integer num2 = 20;
        Integer numeroAleatorio = Utilidades.seleccionaUnoGenerico(num1, num2);
        System.out.println("Número aleatorio: " + numeroAleatorio);
        
        // Parámetro de tipo inferido automáticamente por el compilador
        Double d1 = 3.14;
        Double d2 = 2.71;
        Double resultadoDouble = Utilidades.seleccionaUnoGenerico(d1, d2);
        System.out.println("Double aleatorio: " + resultadoDouble);
    }
}
```

La versión con `Object` presenta dos problemas graves que la versión genérica resuelve. En primer lugar, **falta de seguridad de tipos**: el método `seleccionaUnoObject` puede recibir dos objetos de tipos completamente diferentes (como `String` e `Integer`), y el compilador lo permite. Esto es peligroso porque el código cliente podría asumir que ambos son del mismo tipo y el error solo se manifestaría en tiempo de ejecución con una `ClassCastException`. Además, obliga a **downcasting explícito** cada vez que se desea usar el valor devuelto como su tipo original, lo que ensucia el código y añade la necesidad de comprobaciones con `instanceof` para evitar excepciones.

La versión genérica con parámetro de tipo `<T>` resuelve ambos problemas. El parámetro `T` captura el tipo específico de los argumentos en el momento de la llamada, y el compilador infiere automáticamente ese tipo (en el ejemplo, `T` es `String` o `Integer` según el contexto). Esto garantiza que **ambos argumentos deben ser exactamente del mismo tipo** (o de subtipos compatibles, pero con la misma referencia de tipo base). Si se intenta pasar un `String` y un `Integer`, el compilador produce un error en tiempo de compilación. Además, el valor de retorno es de tipo `T`, lo que significa que se puede asignar directamente a una variable del tipo concreto (por ejemplo, `String`) **sin ningún cast**. El código es más limpio, más seguro y las intenciones quedan claras: la función opera sobre dos elementos del mismo tipo y devuelve uno de ellos. Es importante señalar que la sintaxis para métodos genéricos requiere declarar el parámetro de tipo (`<T>`) antes del tipo de retorno. El compilador infiere automáticamente `T` a partir de los argumentos, aunque también se puede especificar explícitamente: `Utilidades.<String>seleccionaUnoGenerico(str1, str2)`. Esta capacidad de definir métodos genéricos es muy útil para algoritmos que no dependen del tipo concreto, como comparaciones, intercambios, selecciones aleatorias, etc., manteniendo la seguridad de tipos.


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

Sí, Java permite establecer **restricciones** (bounded type parameters) en los parámetros de tipo mediante la palabra clave `extends`. Por ejemplo, `public class Punto<T extends Number> { ... }` indica que `T` debe ser una subclase de `Number` (o `Number` mismo), lo que garantiza que las coordenadas sean algún tipo numérico (`Integer`, `Double`, `Float`, `Long`, etc.) y permite invocar métodos como `doubleValue()` o `intValue()` sobre ellas. Sin esta restricción, los parámetros de tipo solo pueden ser tratados como `Object` (con métodos muy limitados). Las restricciones aumentan la seguridad de tipos y la expresividad del código, permitiendo que el código genérico aproveche las capacidades del tipo acotado.

A continuación se presentan dos soluciones para la clase `Punto`:

**Solución 1: Simple usando `Number` (sin genéricos)** - Menos segura en cuanto al tipo de coordenadas individuales:

```java
public class PuntoNumber {
    private final Number x;
    private final Number y;

    public PuntoNumber(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() { return x; }
    public Number getY() { return y; }

    public double calcularDistanciaA(PuntoNumber otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

**Solución 2: Genérica con restricción `T extends Number`** - Más segura y flexible:

```java
public class Punto<T extends Number> {
    private final T x;
    private final T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() { return x; }
    public T getY() { return y; }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = this.x.doubleValue() - otro.x.doubleValue();
        double dy = this.y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

**Ejemplo de uso mostrando la diferencia**:

```java
public class Main {
    public static void main(String[] args) {
        // Con la versión Number (sin genéricos) - menos segura
        PuntoNumber pn1 = new PuntoNumber(3, 4);        // int -> Integer
        PuntoNumber pn2 = new PuntoNumber(3.0, 4.0);    // double -> Double
        // Compila aunque x sea Integer y y Double, pero ambos son Number
        System.out.println("Distancia (Number): " + pn1.calcularDistanciaA(pn2));
        
        // Con la versión genérica - segura y homogénea
        Punto<Integer> pi1 = new Punto<>(3, 4);     // T es Integer
        Punto<Integer> pi2 = new Punto<>(0, 0);     // T es Integer
        
        // Punto<Integer> piError = new Punto<>(3, 4.0); // No compila: tipos mezclados
        
        System.out.println("Distancia (Integer): " + pi1.calcularDistanciaA(pi2));
        
        Punto<Double> pd1 = new Punto<>(3.0, 4.0);
        Punto<Double> pd2 = new Punto<>(0.0, 0.0);
        System.out.println("Distancia (Double): " + pd1.calcularDistanciaA(pd2));
        
        // Ventaja: se conserva el tipo específico en el getter
        Integer xInt = pi1.getX();   // Sin cast, directamente Integer
        Double xDouble = pd1.getX(); // Sin cast, directamente Double
        System.out.println("xInt: " + xInt + ", xDouble: " + xDouble);
    }
}
```

**Sobre el type erasure**: En la versión genérica `Punto<T extends Number>`, después de la compilación (type erasure), el parámetro de tipo `T` se reemplaza por su **límite superior**, que es `Number`. El compilador genera una única clase `Punto` (no una por cada tipo) donde todos los usos de `T` se convierten en `Number`. Además, inserta casts automáticamente cuando sea necesario (por ejemplo, al asignar `pi1.getX()` a una variable `Integer`, se añade un cast a `Integer`). En tiempo de ejecución, un `Punto<Integer>` y un `Punto<Double>` son indistinguibles; ambos son simplemente objetos de la clase `Punto` con atributos de tipo `Number`. Esto es así debido al **type erasure** de Java. La restricción `extends Number` permite al compilador saber que puede tratar `T` como `Number` de forma segura, y también permite al programador invocar métodos de `Number` (como `doubleValue()`) dentro de la clase. Si no se hubiera puesto restricción (`Punto<T>`), el límite superior sería `Object`, y no se podría acceder a `doubleValue()` sin un cast inseguro. La versión con `Number` simple (sin genéricos) no tiene parámetros de tipo, por lo que no hay type erasure que aplicar; simplemente se usa `Number` directamente. La ventaja de la versión genérica es que **cada instancia homogénea** (todos los `Integer` o todos los `Double`) mantiene la información de tipo específico en tiempo de compilación y en el código fuente (en los getters), pero no en tiempo de ejecución. La seguridad y la comodidad (sin casts) son las principales ganancias.


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

La solución sin genéricos (con `Number`) **sí permite** crear un punto con una coordenada de tipo entero (`Integer`) y la otra de tipo real (`Double`), porque ambas son subclases de `Number`. Por ejemplo, `new PuntoNumber(3, 4.5)` compila perfectamente, ya que el primer argumento es un `Integer` y el segundo un `Double`. El compilador no impide esta heterogeneidad dentro de la misma instancia, ya que el tipo declarado de los atributos es `Number` (la superclase común). Esto puede ser deseable en algunos contextos (como cálculos mixtos), pero también puede introducir sutilezas: la precisión y el comportamiento de métodos como `doubleValue()` son consistentes (ambos devuelven `double`), pero el tipo concreto original se pierde (se almacena como `Number`). La flexibilidad es máxima pero a costa de no tener garantías sobre la homogeneidad de los tipos de coordenadas.

La solución con genéricos (`Punto<T extends Number>`) **no permite** crear un punto con coordenadas de tipos diferentes dentro de la misma instancia, porque el parámetro `T` debe ser único para ambas coordenadas. Por ejemplo, `new Punto<Integer, Integer>` (necesitaría dos parámetros, pero aquí solo hay uno) o mejor dicho, `new Punto<Integer>(3, 4)` funciona con dos `Integer`, pero `new Punto<Number>(3, 4.5)` sí funcionaría porque `T` sería `Number`, permitiendo mezcla. Sin embargo, si se declara `Punto<Integer>`, ambas coordenadas deben ser `Integer`; si se declara `Punto<Double>`, ambas deben ser `Double`. Esta restricción fuerza a que dentro de una instancia las coordenadas sean del **mismo tipo concreto** (homogéneas), aunque ese tipo pueda variar entre distintas instancias (unos puntos con `Integer`, otros con `Double`). Esto refuerza la seguridad de tipos: se evitan mezclas accidentales que podrían llevar a errores sutiles, y se garantiza que `getX()` y `getY()` devuelven exactamente el mismo tipo específico.

En cuanto a lo que devuelve `getX()`:
- **Sin genéricos (`PuntoNumber`)**: `getX()` devuelve `Number`. El código cliente recibe un `Number` y debe convertirlo al tipo deseado (por ejemplo, `Integer x = (Integer) punto.getX()`) o usar métodos como `intValue()`. El tipo concreto subyacente se ha perdido en la firma del método.
- **Con genéricos (`Punto<T>`)**: `getX()` devuelve exactamente `T`. Si la instancia es `Punto<Integer>`, `getX()` devuelve `Integer` directamente; si es `Punto<Double>`, devuelve `Double`. No es necesario ningún cast en el código cliente, y el compilador conoce el tipo exacto. Esto proporciona una experiencia de programación más segura y cómoda.

En resumen, la solución sin genéricos ofrece máxima flexibilidad (permitiendo mezclar tipos) a costa de perder información de tipo concreta y requerir casts o conversiones. La solución genérica ofrece **seguridad de tipos** (homogeneidad forzada) y **comodidad** (sin casts, tipo específico preservado), si bien se pierde la posibilidad de mezclar tipos en la misma instancia (a menos que se use `Punto<Number>`, que vuelve a ser como la versión sin genéricos pero con sintaxis de genéricos). La elección depende del caso de uso: si se necesita mezclar enteros y reales en un mismo punto (por ejemplo, coordenadas de un sistema mixto), puede ser adecuado usar `Number` o una interfaz común. Si se prefiere la homogeneidad y la seguridad de tipos (por ejemplo, todas las coordenadas de un punto son del mismo tipo numérico), la versión genérica es superior.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

```java
// Interfaz genérica con parámetro de tipo auto-referenciado
public interface Punto<T extends Punto<T>> {
    double distanciaA(T otro);
}

// Punto2D implementa Punto<Punto2D> (T = Punto2D)
public class Punto2D implements Punto<Punto2D> {
    private final double x;
    private final double y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    @Override
    public double distanciaA(Punto2D otro) {
        // No se necesita instanceof ni downcasting: otro es Punto2D directamente
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

// Punto3D implementa Punto<Punto3D>
public class Punto3D implements Punto<Punto3D> {
    private final double x;
    private final double y;
    private final double z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    public double getX() { return x; }
    public double getY() { return y; }
    public double getZ() { return z; }

    @Override
    public double distanciaA(Punto3D otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        double dz = this.z - otro.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```

```java
// Demostración de uso
public class Main {
    public static void main(String[] args) {
        Punto2D p2a = new Punto2D(0, 0);
        Punto2D p2b = new Punto2D(3, 4);
        
        // Correcto: ambos son Punto2D
        System.out.println("Distancia 2D: " + p2a.distanciaA(p2b));
        
        // El compilador impide mezclar tipos
        Punto3D p3a = new Punto3D(0, 0, 0);
        Punto3D p3b = new Punto3D(1, 2, 2);
        System.out.println("Distancia 3D: " + p3a.distanciaA(p3b));
        
        // Error de compilación: no se puede pasar Punto3D a distanciaA de Punto2D
        // p2a.distanciaA(p3a); // No compila
        
        // Polimorfismo con tipos seguros: se puede usar el método solo con el tipo correcto
        Punto<Punto2D> puntoPolimorfico = p2a;
        System.out.println("Polimórfico: " + puntoPolimorfico.distanciaA(p2b));
        
        // El siguiente código no compilaría, mostrando la seguridad de tipos
        // Punto<Punto2D> puntoPolimorfico2 = p3a; // Error: tipos incompatibles
    }
}
```

La técnica utilizada se denomina **"tipo auto-referenciado"** (self-referential type) o **"recursive generic"**. La interfaz `Punto<T extends Punto<T>>` es una declaración genérica que fuerza a que el parámetro `T` sea siempre un subtipo de `Punto<T>`. Cuando `Punto2D` implementa `Punto<Punto2D>`, queda establecido que el método `distanciaA` debe recibir un parámetro de tipo `Punto2D` y no de cualquier `Punto`. Esto elimina completamente la necesidad de `instanceof` y downcasting, ya que el compilador garantiza en tiempo de compilación que solo se puedan pasar objetos del tipo correcto. Si se intenta llamar a `distanciaA` de un `Punto2D` con un `Punto3D`, el compilador produce un error, detectando el problema estáticamente.

Esta técnica es ampliamente utilizada en APIs de Java, por ejemplo en `java.lang.Comparable<T>` (donde las clases implementan `Comparable<MismaClase>`) o en `Enum<E extends Enum<E>>`. Las ventajas son claras: se elimina la lógica de verificación en tiempo de ejecución (más segura y generalmente más eficiente), se fuerza al compilador a garantizar la corrección de tipos, y el código resultante es más limpio y autodocumentado. La única pequeña complicación es la declaración genérica algo críptica, pero una vez comprendida, se convierte en una herramienta muy potente para jerarquías en las que los métodos operan sobre objetos del mismo subtipo. Como limitación, el parámetro de tipo `T` debe ser un tipo concreto conocido en tiempo de compilación, lo que impide mezclar dinámicamente `Punto2D` con `Punto3D` en el mismo método polimórfico, pero esa es precisamente la restricción deseada para evitar la incompatibilidad de dimensiones.


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

La respuesta es **no**, `List<String>` **no** es subtipo de `List<Object>`, aunque `String` sea subtipo de `Object`. En Java, los tipos genéricos son **invariantes**: aunque `S` sea subtipo de `T`, `List<S>` no es subtipo ni supertipo de `List<T>` (a menos que se utilicen comodines como `? extends` o `? super`). Esta decisión de diseño previene problemas de seguridad de tipos. Si se permitiera que `List<String>` fuese subtipo de `List<Object>`, se podría añadir un `Integer` a una lista declarada como `List<Object>` que realmente apunte a una `List<String>`, rompiendo la seguridad de tipos en tiempo de ejecución. El compilador de Java rechaza esta asignación directamente.

En cambio, **los arrays en Java son covariantes**: `String[]` **sí** es subtipo de `Object[]`. Esta es una decisión de diseño heredada de versiones tempranas de Java, anterior a la existencia de genéricos. Permite asignar un array de un tipo específico a una variable de un array de su supertipo: `Object[] objetos = new String[10];`. Sin embargo, esta covarianza conlleva un problema grave: se puede producir una **excepción en tiempo de ejecución** (`ArrayStoreException`). Por ejemplo, `objetos[0] = 123;` (asignar un `Integer` a un array que en realidad es de `String`) compila correctamente porque `objetos` es de tipo `Object[]`, pero en tiempo de ejecución la JVM detecta que el array subyacente es de `String` y lanza `ArrayStoreException`. Este problema no puede ser detectado por el compilador debido a la covarianza, a diferencia de los genéricos invariantes que lo detectan estáticamente.

A partir de estos ejemplos, se definen los conceptos:

- **Covariante**: Si `S` es subtipo de `T`, entonces `C<S>` es subtipo de `C<T>`. En Java, los **arrays** son covariantes (`String[]` → `Object[]`). También el retorno de métodos puede ser covariante (una subclase puede devolver un subtipo más específico). En genéricos con comodines, se usa `? extends T` para lograr covarianza (pero solo para lectura segura).

- **Contravariante**: Si `S` es subtipo de `T`, entonces `C<T>` es subtipo de `C<S>` (es decir, la dirección de herencia se invierte). En Java genéricos, se logra con `? super T`. Es útil para estructuras que solo escriben (consumen) datos de tipo `T`, permitiendo pasar contenedores de tipos más específicos.

- **Invariante**: `C<S>` y `C<T>` no tienen relación de subtipado, incluso si `S` y `T` la tienen. Es el comportamiento por defecto de los tipos genéricos en Java sin comodines. `List<String>` no es ni subtipo ni supertipo de `List<Object>`.

La tabla comparativa:
| Concepto | Arrays (Java) | Genéricos (Java sin comodines) | Genéricos con `? extends T` | Genéricos con `? super T` |
|----------|---------------|-------------------------------|----------------------------|---------------------------|
| Covarianza | Sí (`String[]` → `Object[]`) | No (invariante) | Sí (producción/lectura) | No |
| Contravarianza | No | No (invariante) | No | Sí (consumo/escritura) |
| Seguridad | Problema en tiempo de ejecución (`ArrayStoreException`) | Seguro en compilación | Seguro en compilación | Seguro en compilación |

La lección es: los genéricos con invariabilidad y comodines son más seguros que los arrays covariantes, ya que trasladan los errores de tipos al momento de compilación. Por esta razón, en código Java moderno se prefieren las colecciones genéricas (`List<String>`) sobre los arrays heterogéneos cuando sea posible, y cuando se usan arrays, se debe tener mucho cuidado con las asignaciones covariantes.


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

Un **wildcard** (comodín) en Java, representado por `?`, es un marcador de posición que indica un tipo desconocido en los parámetros de tipo. Se utiliza para expresar **covarianza** (`? extends T`) o **contravarianza** (`? super T`) en tipos genéricos, permitiendo mayor flexibilidad en las asignaciones y en la definición de métodos que trabajan con colecciones de tipos relacionados, sin perder la seguridad de tipos que proporcionan los genéricos. Sin wildcards, `List<Number>` solo aceptaría exactamente `Number`, pero con `List<? extends Number>` se puede aceptar cualquier lista de subtipos de `Number` (como `List<Integer>` o `List<Double>`). Los wildcards son útiles para hacer APIs más generales y reutilizables, especialmente cuando el método solo necesita leer (`extends`) o solo escribir (`super`) en la estructura.

**`List<? extends T>` (covarianza)** indica una lista de algún tipo desconocido que es **subtipo de T** (incluido T mismo). Con esta declaración, se puede **leer** de la lista y asignar los elementos a variables de tipo `T` (porque cualquier elemento será al menos un `T`), pero **no se puede escribir** en la lista (excepto `null`) porque no se sabe el tipo exacto concreto. Es útil para métodos que **consumen datos** de una colección sin modificarla.

**`List<? super T>` (contravarianza)** indica una lista de algún tipo desconocido que es **supertipo de T** (incluido T mismo). Con esta declaración, se puede **escribir** en la lista elementos de tipo `T` (y subtipos) con seguridad, porque cualquier lista que admita supertipos de T admitirá también instancias de T. Sin embargo, al **leer**, solo se puede garantizar que los elementos serán `Object`, no `T`. Es útil para métodos que **llenan o añaden datos** a una colección.

**Ejemplo (i): método que suma números usando `? extends Number`**

```java
import java.util.List;

public class Calculadora {
    // Covarianza: puede aceptar List<Integer>, List<Double>, List<Number>, etc.
    public static double sumarLista(List<? extends Number> lista) {
        double suma = 0.0;
        for (Number n : lista) {
            suma += n.doubleValue();
        }
        return suma;
    }
    
    public static void main(String[] args) {
        List<Integer> enteros = List.of(1, 2, 3);
        List<Double> decimales = List.of(1.5, 2.5, 3.5);
        List<Number> numeros = List.of(1, 2.5, 3);
        
        System.out.println("Suma enteros: " + sumarLista(enteros));   // 6.0
        System.out.println("Suma decimales: " + sumarLista(decimales)); // 7.5
        System.out.println("Suma números mixtos: " + sumarLista(numeros)); // 6.5
        
        // No se puede añadir (escribir) a la lista dentro del método
        // lista.add(10); // Error de compilación: no se permite
    }
}
```

**Ejemplo (ii): método que añade enteros a una lista usando `? super Integer`**

```java
import java.util.ArrayList;
import java.util.List;

public class Llenador {
    // Contravarianza: puede aceptar List<Integer>, List<Number>, List<Object>
    public static void anadirEnteros(List<? super Integer> lista) {
        for (int i = 1; i <= 5; i++) {
            lista.add(i);  // Se puede escribir: Integer es subtipo de ? super Integer
        }
        // Al leer, solo se puede usar Object
        // Integer x = lista.get(0); // Error de compilación
        Object obj = lista.get(0); // Válido
    }
    
    public static void main(String[] args) {
        List<Number> listaNum = new ArrayList<>();
        List<Object> listaObj = new ArrayList<>();
        List<Integer> listaInt = new ArrayList<>();
        
        anadirEnteros(listaNum);   // Válido: List<Number> es List<? super Integer>
        anadirEnteros(listaObj);   // Válido: List<Object> es List<? super Integer>
        anadirEnteros(listaInt);   // Válido: List<Integer> también
        
        System.out.println("listaNum: " + listaNum); // [1, 2, 3, 4, 5]
        System.out.println("listaObj: " + listaObj);
        System.out.println("listaInt: " + listaInt);
        
        // List<String> no funcionaría: String no es supertipo de Integer
        // List<String> listaStr = new ArrayList<>();
        // anadirEnteros(listaStr); // Error de compilación
    }
}
```

**Resumen de reglas mnemotécnicas (PECS: Producer Extends, Consumer Super)**: 
- **Producer Extends**: Si un método **lee** (`produce`) elementos de una estructura, usa `? extends T`. Se puede leer como `T`, pero no escribir nada que no sea `null`.
- **Consumer Super**: Si un método **escribe** (`consume`) elementos en una estructura, usa `? super T`. Se puede escribir elementos de tipo `T` (y subtipos), pero al leer solo se garantiza `Object`.
- Si ambos (lee y escribe), no se usa wildcard, sino un parámetro de tipo concreto.

Esta distinción es fundamental para diseñar APIs genéricas flexibles y seguras, maximizando la reutilización sin comprometer la integridad de tipos en tiempo de compilación.