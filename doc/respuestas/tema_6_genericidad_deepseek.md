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

### Respuesta


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta


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

### Respuesta


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta
