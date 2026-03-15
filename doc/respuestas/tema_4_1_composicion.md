# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

En C, la composición de estructuras se implementa mediante la inclusión de unas estructuras dentro de otras como campos. Este enfoque "tiene-un" permite construir tipos de datos complejos a partir de tipos más simples. La estructura `Punto` contiene las coordenadas, y la estructura `Linea` contiene dos campos de tipo `Punto` (inicio y fin). La distancia entre puntos se calcula mediante una función independiente que recibe dos estructuras `Punto` por valor (o por referencia para eficiencia), aplicando el teorema de Pitágoras. La longitud de la línea se obtiene simplemente llamando a la función de distancia con los puntos que la componen.

```c
#include <stdio.h>
#include <math.h>

// Definición de las estructuras
typedef struct {
    double x;
    double y;
} Punto;

typedef struct {
    Punto inicio;
    Punto fin;
} Linea;

// Función para calcular distancia entre dos puntos
double distancia(Punto p1, Punto p2) {
    double dx = p1.x - p2.x;
    double dy = p1.y - p2.y;
    return sqrt(dx*dx + dy*dy);
}

// Función para calcular longitud de una línea
double longitudLinea(Linea l) {
    return distancia(l.inicio, l.fin);
}

int main() {
    // Crear puntos
    Punto p1 = {0.0, 0.0};
    Punto p2 = {3.0, 4.0};
    
    // Crear línea compuesta por los dos puntos
    Linea miLinea = {p1, p2};
    
    printf("Distancia entre puntos: %f\n", distancia(p1, p2));
    printf("Longitud de la línea: %f\n", longitudLinea(miLinea));
    
    return 0;
}
```

La composición en C, aunque funcional, presenta limitaciones importantes desde la perspectiva de la programación orientada a objetos. No hay encapsulación real, ya que todos los campos son públicamente accesibles, y las operaciones (funciones) están desligadas de los datos. Las funciones `distancia` y `longitudLinea` son entidades independientes que operan sobre las estructuras, a diferencia de los métodos de instancia en Java. Además, no hay mecanismos para ocultar la representación interna ni para garantizar invariantes, como que una línea no pueda tener puntos nulos, lo que en Java se lograría mediante constructores y validaciones en los métodos. Sin embargo, este ejemplo ilustra claramente la base conceptual de la composición: construir entidades complejas mediante el ensamblaje de componentes más simples.


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.

En Java, la composición se implementa mediante el uso de atributos de tipo objeto dentro de una clase, pero con la ventaja de la encapsulación y el control de acceso. La clase `Punto` puede diseñarse como **inmutable**, declarando sus atributos como `private final` y proporcionando únicamente métodos getter y un constructor que inicialice las coordenadas. La clase `Linea`, a su vez, contendrá dos referencias a objetos `Punto` como atributos privados y finales, garantizando que una vez construida, los puntos que la forman no pueden ser reemplazados. Esta inmutabilidad proporciona seguridad y simplicidad, ya que una línea siempre representará el mismo segmento durante toda su vida.

```java
public class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public double distancia(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

public class Linea {
    private final Punto inicio;
    private final Punto fin;

    public Linea(Punto inicio, Punto fin) {
        // Se podría añadir validación para evitar puntos nulos
        if (inicio == null || fin == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser nulos");
        }
        this.inicio = inicio;
        this.fin = fin;
    }

    public Punto getInicio() { return inicio; }
    public Punto getFin() { return fin; }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

La superioridad de este diseño sobre C es notable en varios aspectos. La **inmutabilidad** garantiza que, una vez creada una línea, sus puntos no pueden ser alterados ni la línea reconfigurada, lo que previene errores por cambios laterales. La **encapsulación** asegura que los atributos `x` e `y` de `Punto` solo son accesibles mediante métodos controlados, y la validación en el constructor de `Linea` evita estados inválidos (como puntos nulos). El método `longitud()` delega en el método `distancia` del propio objeto `Punto`, demostrando cómo los objetos colaboran enviándose mensajes entre sí. Además, el uso de excepciones (`IllegalArgumentException`) permite comunicar errores de construcción de manera limpia, algo que en C requeriría códigos de retorno o variables globales. Este ejemplo ilustra cómo la composición en POO, combinada con encapsulación e inmutabilidad, produce código más robusto, autoexplicativo y difícil de usar incorrectamente.


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

La **multiplicidad** en la composición (y en las relaciones entre clases en general) indica **cuántos objetos de una clase pueden estar asociados con un objeto de la otra clase**. Especifica las cardinalidades de la relación, respondiendo a preguntas como "¿cuántos objetos de tipo B puede contener o referenciar un objeto de tipo A?" y, en la dirección inversa, "¿cuántos objetos de tipo A pueden referenciar a un mismo objeto de tipo B?". La multiplicidad se expresa típicamente con números o rangos (como 1, 0..1, 0..*, 1..*, etc.) y es fundamental para comprender y documentar el diseño de las relaciones entre clases.

En el ejemplo de `Linea` y `Punto`, la multiplicidad es **asimétrica** y claramente definida. Desde la perspectiva de **`Linea` hacia `Punto`**, la multiplicidad es **exactamente 2** (o "dos"), ya que cada línea está compuesta precisamente por dos puntos: inicio y fin. No puede tener menos ni más; el constructor de `Linea` exige ambos puntos. Desde la perspectiva inversa, de **`Punto` hacia `Linea`**, la multiplicidad es **0..*** (cero o muchos), es decir, **no hay límite superior**. Un mismo punto puede ser el inicio de muchas líneas, el fin de muchas líneas, o incluso no pertenecer a ninguna línea en absoluto. En el diseño actual, los puntos no tienen conocimiento de las líneas a las que pertenecen; la relación es unidireccional desde `Linea` hacia `Punto`.

Esta asimetría refleja una decisión de diseño común en composición: el contenedor (`Linea`) conoce a sus componentes (`Punto`), pero los componentes no necesitan conocer a sus contenedores. Esto mantiene el acoplamiento bajo y facilita la reutilización de los objetos `Punto` en múltiples contextos sin que estén atados a una línea específica. Si se necesitara la navegación inversa (saber qué líneas contienen un punto dado), habría que añadir una estructura de datos adicional, como una colección en `Punto`, lo que aumentaría la complejidad y el acoplamiento. La elección de la multiplicidad y la direccionalidad es una decisión de diseño crucial que afecta a la flexibilidad, la complejidad y el rendimiento del sistema.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta
