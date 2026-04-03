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

En el ámbito de la orientación a objetos, la distinción entre composición **fuerte** y **débil** radica en la **propiedad del ciclo de vida** y la **pertenencia existencial** entre los objetos relacionados. La **composición fuerte** (llamada simplemente **composición** en la terminología UML y en la literatura de POO) se caracteriza por una relación de "todo-parte" donde las partes **no tienen existencia independiente** del todo. Si el objeto contenedor es destruido, sus partes constituyentes también lo son. Esta relación se representa en UML con un rombo relleno en el lado del contenedor. Por ejemplo, en una clase `Casa`, las instancias de `Habitación` probablemente no tienen sentido fuera de esa casa; al destruir la casa, las habitaciones desaparecen con ella.

La **composición débil** (comúnmente denominada **agregación** o **asociación**, dependiendo de la especificidad) es aquella donde las partes **tienen un ciclo de vida independiente** del contenedor. El objeto contenedor referencia a otros objetos que pueden existir y tener sentido por sí mismos, incluso después de que el contenedor sea eliminado. En UML, la agregación se representa con un rombo vacío en el lado del contenedor, mientras que la asociación simple no utiliza rombo. Por ejemplo, una clase `Universidad` puede tener una agregación de `Estudiante`, pero los estudiantes siguen existiendo como personas independientemente de que la universidad cierre o dejen de estar matriculados.

En la práctica, la **composición fuerte** (composición propiamente dicha) implica que la creación y destrucción de las partes es **responsabilidad exclusiva del todo**. El todo controla el ciclo de vida de sus partes: típicamente las crea internamente (o las recibe en el constructor y las "toma posesión") y se asegura de que no sean compartidas con otros objetos. La **composición débil** (agregación o asociación) solo indica que un objeto conoce y utiliza a otro, pero no es propietario de su existencia; las partes pueden ser creadas externamente, compartidas entre múltiples contenedores y sobrevivir a ellos.

Esta distinción tiene importantes implicaciones para el diseño de software. La composición fuerte simplifica la gestión de memoria y recursos, especialmente en lenguajes con recolección de basura, ya que las partes no tienen referencias externas que prolonguen su vida más allá de lo deseado. También refuerza la encapsulación, pues los detalles de composición interna quedan ocultos. La agregación o asociación, por su parte, ofrece mayor flexibilidad y reutilización, permitiendo que objetos compartan recursos o que las relaciones se establezcan y modifiquen dinámicamente en tiempo de ejecución. La elección entre uno u otro tipo de composición debe basarse en si la relación refleja una pertenencia existencial (composición fuerte) o simplemente una colaboración o uso (agregación/asociación).


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

Cuando una clase utiliza a otra exclusivamente a través de parámetros de métodos, variables locales o como valor de retorno, sin mantener una referencia persistente como atributo, se habla de **dependencia** y no de composición ni agregación. La dependencia es el tipo de relación más débil entre clases, ya que indica que una clase "conoce" o "utiliza" a otra de manera puntual y transitoria, sin que exista una asociación estructural que perdure más allá de la ejecución del método en cuestión.

En términos de UML, la dependencia se representa con una línea punteada y una flecha abierta desde la clase dependiente hacia la clase de la que depende. Por ejemplo, una clase `Impresora` que recibe un objeto `Documento` como parámetro en un método `imprimir(Documento doc)` tiene una dependencia con `Documento`, pero no necesariamente un atributo que almacene un documento de forma permanente. Otro ejemplo es cuando un método crea internamente un objeto con `new` y lo utiliza localmente, sin almacenar ninguna referencia después de que el método termine; esa creación y uso instantáneo también constituye una dependencia.

La distinción es importante porque afecta al **acoplamiento** y al **ciclo de vida** de los objetos. La composición (fuerte o débil) implica que la relación es estructural: la clase contenedora tiene un atributo que referencia a otra clase, y esa referencia tiene una multiplicidad y una semántica de pertenencia. La dependencia, en cambio, es un vínculo temporal y más flexible; indica que una clase simplemente "necesita" a otra para realizar alguna operación, pero no guarda memoria de esa relación más allá de la llamada al método. En el diseño orientado a objetos, minimizar las dependencias innecesarias es un objetivo común, ya que reduce el acoplamiento y facilita el mantenimiento y las pruebas unitarias.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

```java
// === COMPOSICIÓN FUERTE ===
// Los puntos son creados internamente por Linea y no pueden existir independientemente
public class LineaFuerte {
    private final Punto inicio;
    private final Punto fin;

    // Constructor recibe coordenadas, no puntos externos
    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.inicio = new Punto(x1, y1); // Creación interna
        this.fin = new Punto(x2, y2);     // Creación interna
    }

    public double longitud() {
        return inicio.distancia(fin);
    }

    // No hay getters que expongan los puntos directamente (o si se exponen,
    // se debe devolver copias defensivas para evitar modificaciones externas)
    public Punto getInicio() {
        return new Punto(inicio.getX(), inicio.getY()); // Copia defensiva
    }

    public Punto getFin() {
        return new Punto(fin.getX(), fin.getY()); // Copia defensiva
    }
}
```

```java
// === COMPOSICIÓN DÉBIL (AGREGACIÓN) ===
// Los puntos existen independientemente y se reciben desde el exterior
public class LineaDebil {
    private final Punto inicio;
    private final Punto fin;

    // Constructor recibe referencias a puntos ya existentes
    public LineaDebil(Punto inicio, Punto fin) {
        if (inicio == null || fin == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser nulos");
        }
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }

    // Los getters devuelven las referencias directamente
    // (o podrían devolver copias según el nivel de encapsulación deseado)
    public Punto getInicio() {
        return inicio;
    }

    public Punto getFin() {
        return fin;
    }
}
```

En la **composición fuerte**, la clase `LineaFuerte` es la **única responsable del ciclo de vida** de sus puntos constituyentes. Los puntos se crean internamente dentro del constructor a partir de coordenadas, y no se reciben desde el exterior. Esto garantiza que ningún otro objeto pueda tener una referencia a los mismos puntos, por lo que cuando la línea sea elegible para recolección de basura, sus puntos también lo serán (si no hay otras referencias). Además, los getters devuelven **copias defensivas** de los puntos, impidiendo que código externo pueda almacenar una referencia al punto interno y mantenerlo vivo artificialmente o modificarlo (aunque en este caso los puntos son inmutables, la copia defensiva sigue siendo una buena práctica).

En la **composición débil** (agregación), la clase `LineaDebil` **comparte** los puntos con quien los haya creado. Los puntos existen independientemente de la línea y pueden ser reutilizados por múltiples líneas u otros objetos. El ciclo de vida de los puntos no está ligado al de la línea; si la línea desaparece, los puntos pueden seguir existiendo si otras partes del programa mantienen referencias a ellos. Esta versión es más flexible para compartir objetos, pero introduce la responsabilidad de gestionar la consistencia: si un punto es modificado por otra parte del programa (si no fuera inmutable), todas las líneas que lo usan se verían afectadas, lo que puede ser deseable o problemático según el diseño.

La elección entre ambas formas depende de la semántica del dominio. Si el punto solo tiene sentido como parte de una línea (por ejemplo, los vértices de un polígono en un sistema de CAD), la composición fuerte es más apropiada. Si el punto representa una ubicación geográfica que puede ser compartida por múltiples entidades (como una dirección compartida por una persona y una oficina), la composición débil refleja mejor la realidad del dominio.


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

En Java, la **destrucción explícita de objetos no existe** como concepto. La memoria es gestionada automáticamente por el **recolector de basura** (*garbage collector*). Cuando un objeto deja de ser alcanzable desde cualquier referencia activa en el programa, la JVM determina que puede ser recuperado y eventualmente libera su memoria. En la composición fuerte, la "destrucción" de los objetos componentes no es un acto explícito que realice el contenedor, sino una **consecuencia de la inalcanzabilidad** que se produce cuando el contenedor mismo deja de ser alcanzable.

En el ejemplo de `LineaFuerte`, los puntos `inicio` y `fin` son referencias almacenadas como atributos privados de la línea. Mientras la línea sea alcanzable (es decir, mientras exista al menos una referencia a ella desde alguna variable activa, como una variable local en un método o un atributo de otro objeto alcanzable), los puntos también lo son, porque están referenciados desde la línea. Cuando la línea deja de ser alcanzable (por ejemplo, la variable que la referenciaba sale de ámbito o se le asigna `null`), automáticamente sus atributos `inicio` y `fin` también dejan de ser alcanzables **si no existen otras referencias** a esos mismos puntos desde otras partes del programa.

La clave de la composición fuerte es precisamente **evitar que existan referencias externas a los componentes**. En `LineaFuerte` se logra creando los puntos internamente en el constructor y devolviendo copias defensivas en los getters en lugar de las referencias originales. Esto asegura que ningún código externo pueda almacenar una referencia a `inicio` o `fin`. Por lo tanto, cuando la línea es abandonada, los puntos quedan sin referencias y son elegibles para ser recolectados. No hay necesidad de una llamada explícita a `destroy()` o `delete`; el recolector de basura se encarga automáticamente de liberar la memoria en un momento posterior indeterminado.

Esta es una diferencia fundamental con lenguajes como C++, donde la composición fuerte requeriría la destrucción explícita de los objetos miembros en el destructor del contenedor (o mediante punteros inteligentes como `unique_ptr`). En Java, la recolección automática de basura simplifica enormemente la gestión de memoria en relaciones de composición, pero impone al diseñador la responsabilidad de controlar las referencias externas para que la "pertenencia" existencial sea efectiva. Si se filtran referencias a los componentes, estos podrían sobrevivir al contenedor, convirtiendo la composición fuerte en una agregación de facto.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

```java
public class Profesor {
    private final String nombre;
    private final String id;

    public Profesor(String nombre, String id) {
        if (nombre == null || id == null) {
            throw new IllegalArgumentException("Nombre e ID no pueden ser nulos");
        }
        this.nombre = nombre;
        this.id = id;
    }

    public String getNombre() { return nombre; }
    public String getId() { return id; }

    @Override
    public String toString() {
        return nombre + " (" + id + ")";
    }
}
```

```java
public class Departamento {
    // Composición débil: los profesores existen independientemente
    private Profesor[] profesores;
    private int cantidadProfesores;      // Contador de profesores actuales
    private Profesor director;           // Referencia al profesor director

    private static final int MAX_PROFESORES = 50;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("El director inicial no puede ser nulo");
        }
        this.profesores = new Profesor[MAX_PROFESORES];
        this.cantidadProfesores = 0;
        this.director = directorInicial;
        // El director debe estar en la lista de profesores
        añadirProfesor(directorInicial);
    }

    // Método para añadir un profesor al final de la lista
    public void añadirProfesor(Profesor profesor) {
        if (profesor == null) {
            throw new IllegalArgumentException("El profesor no puede ser nulo");
        }
        if (cantidadProfesores >= MAX_PROFESORES) {
            throw new IllegalStateException("No se pueden añadir más profesores. Límite: " + MAX_PROFESORES);
        }
        profesores[cantidadProfesores] = profesor;
        cantidadProfesores++;
    }

    // Método para eliminar un profesor por su posición (índice)
    public void eliminarProfesor(int posicion) {
        if (posicion < 0 || posicion >= cantidadProfesores) {
            throw new IllegalArgumentException("Posición inválida: " + posicion);
        }
        
        Profesor profesorAEliminar = profesores[posicion];
        
        // Invariante: no se puede eliminar al director si es el único profesor
        if (profesorAEliminar == director && cantidadProfesores == 1) {
            throw new IllegalStateException("No se puede eliminar al director si es el único profesor del departamento");
        }
        
        // Desplazar los elementos hacia la izquierda
        for (int i = posicion; i < cantidadProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[cantidadProfesores - 1] = null; // Limpiar la última referencia
        cantidadProfesores--;
        
        // Si el profesor eliminado era el director, se asigna un nuevo director
        if (profesorAEliminar == director) {
            // Se asigna al primer profesor de la lista (ahora hay al menos uno)
            director = profesores[0];
        }
    }

    // Método para cambiar el director por otro profesor del departamento
    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El nuevo director no puede ser nulo");
        }
        
        // Verificar que el nuevo director pertenece al departamento
        boolean pertenece = false;
        for (int i = 0; i < cantidadProfesores; i++) {
            if (profesores[i] == nuevoDirector) {
                pertenece = true;
                break;
            }
        }
        
        if (!pertenece) {
            throw new IllegalArgumentException("El nuevo director debe ser un profesor del departamento");
        }
        
        this.director = nuevoDirector;
    }

    // Método para obtener la cantidad de profesores
    public int getCantidadProfesores() {
        return cantidadProfesores;
    }

    // Método para obtener un profesor por posición (copia defensiva de la referencia)
    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= cantidadProfesores) {
            throw new IllegalArgumentException("Posición inválida: " + posicion);
        }
        return profesores[posicion];
    }

    // Método para obtener el director actual
    public Profesor getDirector() {
        return director;
    }

    // Método para verificar si un profesor pertenece al departamento
    public boolean contieneProfesor(Profesor profesor) {
        if (profesor == null) return false;
        for (int i = 0; i < cantidadProfesores; i++) {
            if (profesores[i] == profesor) {
                return true;
            }
        }
        return false;
    }
}
```

En este diseño se implementa una **composición débil** (agregación) porque los objetos `Profesor` tienen un ciclo de vida independiente del `Departamento`. Un profesor puede existir sin pertenecer a un departamento y puede ser compartido entre diferentes estructuras (aunque aquí no se implementa esa posibilidad). La relación entre `Departamento` y sus profesores es una **agregación** (composición débil), mientras que la relación con el director es también débil pero con una **restricción adicional**: el director debe ser siempre uno de los profesores del departamento.

La **encapsulación** se preserva ocultando el array interno (`profesores`) y exponiendo únicamente los métodos necesarios para interactuar con la colección. Las **invariantes de clase** se protegen mediante validaciones:
- El director nunca puede ser `null`.
- El director siempre debe estar en la lista de profesores.
- No se puede eliminar al director si es el único profesor del departamento.
- Al cambiar el director, el nuevo debe pertenecer al departamento.
- Al eliminar al director, se asigna automáticamente un nuevo director (el primero de la lista).

Las **excepciones** (`IllegalArgumentException`, `IllegalStateException`) se lanzan cuando se intenta violar alguna invariante o condición de uso, siguiendo el patrón de excepciones no controladas para errores de programación (precondiciones violadas) y controladas implícitamente para estados inválidos del objeto. Esta aproximación garantiza que el objeto `Departamento` siempre se mantenga en un estado consistente según las reglas de negocio definidas.


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class DepartamentoList {
    // Composición débil usando List en lugar de array primitivo
    private List<Profesor> profesores;
    private Profesor director;

    public DepartamentoList(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("El director inicial no puede ser nulo");
        }
        this.profesores = new ArrayList<>();
        this.director = directorInicial;
        this.profesores.add(directorInicial);
    }

    public void añadirProfesor(Profesor profesor) {
        if (profesor == null) {
            throw new IllegalArgumentException("El profesor no puede ser nulo");
        }
        profesores.add(profesor);
    }

    public void eliminarProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IllegalArgumentException("Posición inválida: " + posicion);
        }
        
        Profesor profesorAEliminar = profesores.get(posicion);
        
        // Invariante: no se puede eliminar al director si es el único profesor
        if (profesorAEliminar == director && profesores.size() == 1) {
            throw new IllegalStateException("No se puede eliminar al director si es el único profesor del departamento");
        }
        
        profesores.remove(posicion);
        
        // Si el profesor eliminado era el director, se asigna un nuevo director
        if (profesorAEliminar == director) {
            director = profesores.get(0);
        }
    }

    public void cambiarDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El nuevo director no puede ser nulo");
        }
        
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException("El nuevo director debe ser un profesor del departamento");
        }
        
        this.director = nuevoDirector;
    }

    public int getCantidadProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int posicion) {
        if (posicion < 0 || posicion >= profesores.size()) {
            throw new IllegalArgumentException("Posición inválida: " + posicion);
        }
        return profesores.get(posicion);
    }

    public Profesor getDirector() {
        return director;
    }

    public boolean contieneProfesor(Profesor profesor) {
        return profesores.contains(profesor);
    }
}
```

Al usar `List` (en concreto `ArrayList`), el código original se ha **ahorrado** varias tareas de gestión manual que eran necesarias con arrays primitivos. En primer lugar, la **declaración del tamaño máximo** (`MAX_PROFESORES`) y las comprobaciones de capacidad ya no son necesarias, ya que `ArrayList` crece dinámicamente. En segundo lugar, la **gestión del contador** (`cantidadProfesores`) desaparece, pues `List` proporciona el método `size()`. En tercer lugar, la **lógica de desplazamiento** al eliminar un elemento (el bucle `for` que movía referencias) se elimina por completo, ya que `remove(posicion)` lo hace internamente. También se elimina la necesidad de **limpiar manualmente la última posición** con `null`. Por último, la comprobación de pertenencia se simplifica drásticamente usando `contains()` en lugar de un bucle manual.

Si existiera un método que devolviera **todos los profesores a la vez**, como `public List<Profesor> getTodosLosProfesores() { return profesores; }`, el problema sería que se **rompe la encapsulación** de manera peligrosa. El código externo recibiría una referencia directa a la lista interna del departamento, lo que permitiría modificaciones no controladas como `departamento.getTodosLosProfesores().add(profesorExterno)` o `departamento.getTodosLosProfesores().remove(0)`, saltándose por completo las validaciones y la lógica de negocio (como la invariante del director). Esto podría dejar al departamento en un estado inconsistente, con un director que ya no está en la lista o con profesores añadidos sin control.

Para resolverlo, se debe devolver una **copia defensiva** o una **vista no modificable** de la lista interna. La solución más común es usar `Collections.unmodifiableList()`:

```java
public List<Profesor> getTodosLosProfesores() {
    return Collections.unmodifiableList(profesores);
}
```

Esta vista lanza `UnsupportedOperationException` si el código externo intenta modificarla, protegiendo así la integridad del objeto. Alternativamente, se podría devolver una **copia** explícita: `return new ArrayList<>(profesores);`, pero esto tiene el coste de crear un nuevo objeto y duplicar la lista. La elección depende de si se prefiere la inmutabilidad de la vista (sin copia) o una copia completamente independiente (que refleja un momento en el tiempo). En cualquier caso, **nunca** se debe devolver la referencia directa a la estructura interna mutable.


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

```java
public final class Persona {
    private final String nombre;
    private final Persona madre; // Composición recursiva: una Persona tiene una madre que es otra Persona

    public Persona(String nombre, Persona madre) {
        this.nombre = nombre;
        this.madre = madre;
    }

    // Constructor para una persona sin madre (raíz de la jerarquía)
    public Persona(String nombre) {
        this(nombre, null);
    }

    public String getNombre() {
        return nombre;
    }

    public Persona getMadre() {
        return madre;
    }

    // Método para obtener la abuela materna (recursivo)
    public Persona getAbuelaMaterna() {
        if (madre == null) {
            return null;
        }
        return madre.getMadre();
    }

    // Método para obtener el árbol genealógico como String (recursivo)
    public String getArbolGenealogico() {
        if (madre == null) {
            return nombre;
        }
        return nombre + " (hijo/a de " + madre.getArbolGenealogico() + ")";
    }

    @Override
    public String toString() {
        return nombre;
    }
}
```

```java
public class MainFamiliar {
    public static void main(String[] args) {
        // Construcción desde la abuela hacia el nieto (inmutable)
        Persona abuela = new Persona("María González");
        Persona madre = new Persona("Ana López", abuela);
        Persona hijo = new Persona("Carlos López", madre);
        Persona nieta = new Persona("Lucía López", hijo);

        System.out.println("Árbol genealógico de Lucía:");
        System.out.println(nieta.getArbolGenealogico());
        System.out.println("\nMadre de Lucía: " + nieta.getMadre());
        System.out.println("Abuela materna de Lucía: " + nieta.getAbuelaMaterna());
        System.out.println("Bisabuela materna de Lucía: " + 
                           (nieta.getAbuelaMaterna() != null ? 
                            nieta.getAbuelaMaterna().getMadre() : "No existe"));
    }
}
```

Este ejemplo de **composición recursiva** (o recursiva estructural) muestra cómo una clase `Persona` contiene una referencia a otra `Persona` (su madre). Esta estructura es recursiva porque el tipo `Persona` se define en términos de sí mismo, creando una jerarquía que puede ser tan profunda como se desee. La **inmutabilidad** se garantiza mediante `final` en la clase y sus atributos, y la ausencia de setters. Para construir una cadena familiar, es necesario hacerlo desde la raíz (la abuela) hacia los descendientes, ya que una vez creada, la relación madre-hijo no puede modificarse. Los métodos recursivos como `getAbuelaMaterna()` y `getArbolGenealogico()` recorren esta estructura naturalmente, siguiendo las referencias hasta llegar a `null` (caso base).

Otros ejemplos clásicos de composiciones recursivas en programación orientada a objetos incluyen:

1. **Árboles binarios**: un `Nodo` tiene referencia a un `Nodo` izquierdo y a un `Nodo` derecho (ambos del mismo tipo), formando una estructura jerárquica donde cada nodo puede contener subárboles completos.

2. **Sistemas de archivos**: una `Carpeta` contiene una colección de `Elemento`, y cada `Elemento` puede ser un `Archivo` (hoja) o una `Carpeta` (nodo interno), creando una estructura recursiva de directorios.

3. **Expresiones aritméticas**: una `Expresion` puede ser un `Numero` (hoja), una `Suma` (compuesta por dos `Expresion`), una `Multiplicacion` (compuesta por dos `Expresion`), etc., permitiendo representar expresiones anidadas arbitrariamente.

4. **Listas enlazadas**: un `NodoLista` contiene un valor y una referencia a otro `NodoLista` (el siguiente), formando una cadena recursiva lineal.

La composición recursiva es una herramienta poderosa para modelar estructuras jerárquicas y recursivas del dominio del problema, aprovechando la capacidad de los lenguajes orientados a objetos para que una clase pueda contener referencias a sí misma, siempre que se respeten los principios de encapsulación y se maneje correctamente el caso base (generalmente `null`) para evitar recursiones infinitas o desbordamientos de pila.

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

Las relaciones de composición **bidireccionales** son aquellas en las que **ambos extremos de la relación conocen al otro**, es decir, cada objeto tiene una referencia (o colección de referencias) hacia el objeto o los objetos relacionados en el otro lado. Esto permite la navegación en ambos sentidos: desde `Departamento` se puede acceder a sus `Profesor`es, y desde un `Profesor` se puede saber a qué `Departamento` pertenece (o perteneció). En el ámbito de la composición débil (agregación), la bidireccionalidad es común cuando las reglas del dominio requieren que ambas entidades tengan conocimiento mutuo para mantener invariantes o facilitar operaciones.

Para implementar esta relación bidireccional en el ejemplo de `Profesor` y `Departamento`, se debería **añadir una referencia desde `Profesor` hacia su `Departamento`**. Sin embargo, esto introduce complejidad adicional porque hay que mantener la **consistencia** entre ambas direcciones: si un profesor se añade a un departamento, su referencia al departamento debe actualizarse; si se elimina, debe limpiarse. Además, surgen decisiones de diseño sobre la multiplicidad inversa: un profesor típicamente pertenece a un solo departamento (multiplicidad 1), aunque podría pertenecer a varios (0..*). El siguiente código ilustra una implementación bidireccional donde cada profesor conoce su departamento:

```java
public class Profesor {
    private final String nombre;
    private final String id;
    private Departamento departamento;  // Referencia bidireccional (nullable)

    public Profesor(String nombre, String id) {
        this.nombre = nombre;
        this.id = id;
        this.departamento = null;  // Inicialmente sin departamento
    }

    public String getNombre() { return nombre; }
    public String getId() { return id; }
    
    public Departamento getDepartamento() { return departamento; }
    
    // Método de paquete o público restringido para establecer la relación
    void setDepartamento(Departamento dept) {
        this.departamento = dept;
    }
}

public class Departamento {
    private List<Profesor> profesores;
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        this.profesores = new ArrayList<>();
        this.director = directorInicial;
        añadirProfesor(directorInicial);
    }

    public void añadirProfesor(Profesor profesor) {
        if (profesor == null) {
            throw new IllegalArgumentException("El profesor no puede ser nulo");
        }
        // Mantener consistencia bidireccional
        if (profesor.getDepartamento() != null) {
            profesor.getDepartamento().removerProfesor(profesor);
        }
        profesores.add(profesor);
        profesor.setDepartamento(this);
    }

    private void removerProfesor(Profesor profesor) {
        if (profesores.remove(profesor)) {
            profesor.setDepartamento(null);
        }
    }

    public void eliminarProfesor(int posicion) {
        Profesor profesorAEliminar = profesores.get(posicion);
        if (profesorAEliminar == director && profesores.size() == 1) {
            throw new IllegalStateException("No se puede eliminar al director si es el único profesor");
        }
        removerProfesor(profesorAEliminar);
        if (profesorAEliminar == director) {
            director = profesores.get(0);
        }
    }

    // Resto de métodos similares (cambiarDirector, getters, etc.)
}
```

Implementar bidireccionalidad requiere **acciones de mantenimiento en ambos lados** cada vez que se establece o rompe la relación. En el ejemplo, el método `añadirProfesor` verifica si el profesor ya pertenecía a otro departamento y lo desvincula primero, luego añade la referencia en la lista del departamento y actualiza la referencia inversa con `profesor.setDepartamento(this)`. El método `removerProfesor` (privado) limpia ambas referencias. Para evitar que la bidireccionalidad sea rota por código externo, los métodos de actualización de la referencia inversa deben tener visibilidad restringida (por ejemplo, `protected` o de paquete), para que solo las clases del mismo paquete (o el departamento) puedan modificarlos.

La principal **desventaja** de las relaciones bidireccionales es que aumentan el **acoplamiento** y la complejidad de mantenimiento, pudiendo introducir errores si no se actualizan ambas direcciones consistentemente. También pueden crear **ciclos de referencia** que dificulten la recolección de basura (aunque en Java esto no es un problema, ya que el recolector maneja ciclos). Por ello, la bidireccionalidad debe usarse solo cuando sea estrictamente necesaria por requisitos del dominio, como cuando un profesor necesita saber su departamento para mostrar su adscripción o para validar reglas de negocio que dependen de ese conocimiento. En muchos casos, es preferible mantener la relación **unidireccional** y, si se necesita navegar en sentido inverso, hacer una búsqueda en el contexto adecuado (por ejemplo, preguntar a un servicio centralizado).