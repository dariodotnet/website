Title: Subscriptions
---

# Subscribing


# Unsubscribing


# OnError and OnCompleted


# Lifecycle

El comportamiento por defecto de los operadores Observable es hacer un dispose de las subscripciones tan pronto como sea posible (i.e. cuando un mensaje OnCompleted o OnError es publicado) _ pero necesitas considerar que la secuencia nunca terman (por OnCompleted o OnError)_

Si llamas al método Subscribe e ignoras el valor devuelto, estás perdiendo la posibilidad de desuscribirte. La subscripción quedará activa y efectivamente pierdes el acceso a este recurso, el cual podría resultar en un memory leak y seguir procesando procesos no esperados.

El método Subscribe devuelve un IDisposable, por lo que te puedes desuscribir y hacer un dispose de manera sencilla. Cuando llamas al método Dispose de una sequencia observable, el observador parará de escuchar los datos del observable.

> Muchas gente ha oído hablar del patrón Dispose como un primer complemento para cuando el GC no está haciendo su trabajo. Piensan que deberíam recoger recursos y que esto es justo lo que harías en un mundo no maejado. Lo cierto es que el GC nunca tuvo la intención de maejar recursos. Fue diseñado para manejar la memoria y es excelente haciéndolo. - [Krzysztof Cwalina](http://blogs.msdn.com/b/kcwalina/) from [Joe Duffy's blog](http://www.bluebytesoftware.com/blog/2005/04/08/DGUpdateDisposeFinalizationAndResourceManagement.aspx).

Puedes utilizar la interface IDisposable para algo más que el uso común de determinar cuando finalizamos recursos no majados. Esto es una herramienta muy útil para manejar el tiempo de vida o el alcance de cualquier cosa.

ReactiveUI te provee con [WhenActivated](/docs/handbook/when-activated) que te ayuda a maneja el ciclo de vida y las Reactive Extensions te proveen de diferentes opciones que te ayudan a manejar [lifetime, scope and resources](disposables).

## Ver también

* https://msdn.microsoft.com/en-us/library/hh242977(v=vs.103).aspx
* http://www.introtorx.com/content/v1.0.10621.0/03_LifetimeManagement.html

