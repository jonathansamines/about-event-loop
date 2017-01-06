# Sobre funciones asíncronas
### Cómo Javascript maneja las operaciones asíncronas

¿Alguna vez te has preguntando, como es que en **javascript** podemos llevar a cabo operaciones pesadas de forma asíncrona? ¿Sabes como en realidad de ejecutan los callbacks en javascript y cuando en realidad son asíncronos y cuando simplemente es una secuencia de llamadas a una función?

Javascript es un lenguaje realmente interesante conceptualmente hablando, es un lenguaje de los que llamamos ["multiparadigma"](https://es.wikipedia.org/wiki/Lenguaje_de_programaci%C3%B3n_multiparadigma) pues nos permite programar funcionalmente, con objetos e incluso gran parte del diseño del lenguaje funciona mediante eventos.


Una de las características determinantes del lenguaje, y sin la cual éste no tendría el éxito que ostenta, son las llamadas asíncronas, la capacidad de invocar a una función al término de una operación o tarea; mecanismo en el cual se aplica la ley de hollywood "No nos llames, nosotros te llamaremos", afortunadamente en javascript, éstas llamadas posteriores si ocurren en algún momento futuro ya que si fuera como en la vida real, de nada serviría éste mecanismo.

Pero entonces, ¿Cómo es que funciona? Pues bien, a diferencia de lo que la mayor parte de nosotros creemos, un entorno de **browser** en javascript, no sólo está compuesto por el propio runtime de javascript, sino que adicionalmente existen otros [componentes interesantes](#howbrowserswork).

Voy a tratar de explicar de forma simplicada los componentes que conforman un entorno cliente javascript completo:

En primer lugar, está el ya mencionado runtime o entorno de ejecución del lenguaje, que es el encargado de interpretar toda la información del lenguaje javascript, tal y como ésta definido en el Estándar ECMASCRIPT. El papel de éste componente lo toman los ya conocidos V8 ( motor javascript para chrome ), rhino ( motor para firefox ) o más recientemente spartan ( motor para el nuevo navegador de microsoft ).

Por otro lado, si uno se detiene a analizar el estándar ecmascript, en ninguna parte encontraremos funciones como setTimeout, setInterval e incluso otro tipo de utilidades como getElementById o en general cualquier otro tipo de función que interactúe con el DOM ( Document Object Model ). Pero entonces, ¿si no están definidas éstas funciones en el estándar, como es que podemos utilizarlas en nuestro navegador favorito? ¿no era todo ésto de html5 parte de la evolución de javascript como lenguaje? Despues de todo existen entornos como nodejs, que no son un navegador, en los cuales no disponemos de todas éstas utilidades para manipular el DOM, que al cabo que ni necesarias son dado el entorno.

Pues la respuesta está en que toda ésta información se define en un conjunto de APIS totalmente independientes, denominadas DOM ( Document Object Model ). Y la información de éste, junto con las hojas de estilo es procesada de forma separada a través de lo que se conoce como un motor de renderizado.

Esto quiere decir, que éstas operaciones no son procesadas por el mismo proceso central ( runtime ) que procesa otras operaciones del lenguaje. Aaa, esto ya va cobrando un poco de sentido!!, si no es un solo componente el que procesa todas las operaciones del lenguaje, entonces tiene más sentido que ciertas operaciones se puedan efectuar de forma asíncrona.

El papel de éste componente normalmente es representado por motores de renderizado como webkit o blink ( éste último un fork del anterior ).

Pero entonces, ¿como exactamente se procesan todas las operaciones que son definidas a través de callbacks?

Pues sucede que existe un tercer componente que es la cola de llamadas ( queue ), cuya única función es ir encolando todas las funciones pendientes de ejecutar.

Y en éste relajo, en donde entra a trabajar el famoso EventLoop?

Pues es algo en realidad bastante simple, el eventloop o loop de eventos como lo llamamos en español, es simplemente un componente cuya única tarea es buscar en la cola de llamadas si existen funciones pendientes por ejecutar, en cuyo caso simplemente ejecuta la función siguiente en cola. Este proceso lo realiza periodicamente, en búsqueda de tareas (funciones) pendientes por ejecutar y a cada una de éstas búsquedas periódicas se de denomina tick.

Y entonces, si simplemente en estamos encolando nuestras tareas pendientes (callbacks), no estamos ejecutando todo de forma síncrona de todas formas?

Pues exacto!, la mayor parte de las operaciones en javascript, se realizan de forma totalmente síncrona, pero ojo, no todas las tareas se ejecutan de ésta forma.

Ya habíamos dicho que las tareas relacionadas al renderizado eran gestionadas por un componente separado no? esto incluye tanto tareas de renderizado, como la propia gestión de eventos del lenguaje. Pues dada ésta característica, resulta que éstas tareas son llevadas a cabo de forma paralela al proceso central (runtime). Y pues nos permite entre otras cosas, ejecutar tareas pesadas en el proceso central ( runtime ), sin que nuestra interfaz de usuario se congele ¿genial no? y todo esto sin meternos a líos con threads. Mención a parte a Web Workers, que son el equivalente del lenguaje para los threads, que por cierto lo hacen de una forma más limpia y elegante que éstos.

Pero entonces, es posible ejecutar tareas que normalmente ejecutaría el proceso central ( runtime ) de forma asíncrona? Pues si existen formas de ejecutar código de forma asíncrona, o más bien sin que interfiera con el proceso central ( y por lo tanto lo bloquee ). El método más común es utilizar la función setTimeout(callback, 0), lo cual situa a nuestra función 'callback' como la primer tarea en nuestra cola de tareas, con lo cual ésta será ejecutada sin problemas dado que será ejecutada hasta el próximo tick del EventLoop y con ésto evitará bloquear el flujo de nuestra aplicación.

Bueno, espero que ésta pequeña introducción al funcionamiento del eventloop, les haya sido de alguna utilidad.

Para una explicación del funcionamiento de los navegadores en general y del eventloop, pueden visitar los siguientes enlaces :

-
- how browsers work?
