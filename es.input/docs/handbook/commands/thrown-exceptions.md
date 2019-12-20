# Thrown Exceptions

## Caso base
http://stackoverflow.com/questions/26219105/what-is-the-reactiveui-way-to-handle-exceptions-when-executing-inferior-reactive

### Pregunta

    var reactiveCommandA = ReactiveCommand.CreateAsyncTask(_ => CanPossiblyThrowAsync());
    reactiveCommandA.ThrownExceptions
                    .Subscribe(ex => UserError.Throw("Oh no A", ex));
    
    var reactiveCommandB = ReactiveCommand.CreateAsyncTask(_ => CanAlsoPossiblyThrowAsync());
    reactiveCommandB.ThrownExceptions
                    .Subscribe(ex => UserError.Throw("Oh no B", ex));
    
    var reactiveCommandC = ReactiveCommand.CreateAsyncTask
       (
         async _ =>
                   {
                     await reactiveCommandA.ExecuteAsync(); // <= Could throw here
                     await reactiveCommandB.ExecuteAsync();
                     DoSomethingElse();
                   }
        );
    
    reactiveCommandC.ThrownExceptions
                    .Subscribe(ex => UserError.Throw("Oh no C", ex));

Asumiendo que la implementación en background para reactiveCommandA debería lanzar una excepción, Esto está bien, ya que me he suscrito a .ThrownExecptions y teóricamente se notificará al usuario el reintento/fallo/cancelación (no se muestra por brevedad). No es lanzado al dispatcher.

Esto está bien cuando reactiveCommandA es ejecutado por si mismo. Sin embargo, tengo reactiveCommandC, el cual ejecuta reactiveCommandA y reactiveCommandB. También me he suscrito a .ThrownExceptions. El problema es que estoy ejecutando dentro y que al ejecutar las implementaciones de reactiveCommandC y reactiveCommandA también causa error en reactiveCommandC. Entonces estoy notificando al usuario dos veces por el mismo error, porque reactiveCommandA también hace sus cosas en .ThrownExceptions y lo mismo para reactiveCommandC.

Hay alguna aproximación estandard para este tipo de situaciones? Preferiblemente algo elegante, ya que he encontrado código bastante limpio y no quiero meter código spaghetti.

Cosas que he pensado:

* Rodear la línea de "await..." con un bloque try/catch y capturando las excepciones. Parece feo si tengo que hacerlo a menudo.
* Utiliza await reactiveCommandA.ExecuteAsync().Catch(Observable.Never<Unit>()); aunque creo que eso causará que reactiveCommandC nunca se complete so no puede ser ejecutado de nuevo.

* Utilizando la misma aproximación con el método .Catch() pero devolciendo un bool basado en si la ejecución fue completada satisfactoriamente o no (e.g. .Catch(Observable.Return(false))). Tendría que seguir comprobando si podemos continuar en cada paso por await.

### Solución:

    Observable.Merge(rxCmdA.ThrownExceptions, rxCmdB.ThrownExceptions, rxCmdC.ThrownExceptions)
        .Throttle(TimeSpan.FromMilliseconds(250), RxApp.MainThreadScheduler)
        .Subscribe(ex => UserError.Throw("Oh no C", ex));
