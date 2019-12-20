## Command asíncronos vs síncronos

Partiendo de que la API de `ReactiveCommand` es asíncrona, no necesariamente tienes que hacer una ejecución de tu lógica asíncrona. Si tu command no hace uso intensivo de la CPU o es para manejo de archivos (I/O), entonces es problable pensar en ejecutar la lógica de forma síncrona. Pudes hacerlo creando el command vía `ReactiveCommand.Create`:

```cs
var command = ReactiveCommand.Create(() => Console.WriteLine("a synchronous reactive command));
```

Tienes muchas opciones para `Create` commands que tomen parámetros y devuevan valores interesante cuando sean ejecutados. Lo veremos más en detalle a continuación.

Por otro lado, si la lógica de tu command hace uso intensivo de la CPU o incluso en operaciones I/O, querrás utilizar `CreateFromObservable` o `CreateFromTask`:

```cs
// Aquí utilizamos observable para modelar asíncrono
var command1 = ReactiveCommand.CreateFromObservable(() => Observable.Return(Unit.Default).Delay(TimeSpan.FromSeconds(3)));

// Aquí ustilizamos TPL para modelar asíncrono
var command2 = ReactiveCommand.CreateFromTask(async () =>
    {
        await Task.Delay(TimeSpan.FromSeconds(3)); 
    });
```

De nuevo, muchas de las opciones disponibles para commands reciben y devuelven parámetros.

Independientemente de si tu command es de naturaleza síncrona o asíncrona, lo ejecutas con el método `Execute`. Recibes un observable haciendo que el command emita señales cuando termine su ejecución. Los commands síncronos se ejecutarán inmediatamente, y recibirás el observable cuando la ejecución haya sido completada. El observable devuelto es conductual, por lo que la subscripción a su finalización marcará el resultado final.

> **Atención** Como suele ser el caso de Rx, el observable devuelto por `Execute` es frío. Esto es, no ocurrirá nada a menos que te subscribas al command. Esta subscripción a menudo es instigada por la infraestructura de Binding. Pero en esos caso donde estás llamado directamente a `Execute`, es muy importante recordar que esto es perezoso.



