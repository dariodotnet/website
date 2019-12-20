## Cancelando

Si la lógica de tus commands toma mucho tiempo en ejecutarse, puede ser muy útil permiter que se cancele la ejecución. Este soporte a la cancelación puede ser utilizada internamente por tu ViewModel, o expuesta para que el usuario pueda decidir.

### Cancelación básica

El modo más primitivo para cancelar la ejecución de un command, es haciendo un dispose de la subscripción:

```cs
var subscription = someReactiveCommand
    .Execute()
    .Subscribe();

// esto cancela la ejecución del command
subscription.Dispose();
```

Sin embargo, esto requiere que obtengas y retengas la subscripción. Si estás utilizando bindings para ejecutar tus commands, no tienes acceso a la subscripción.

### Cancelando via otro observable

Rx en si mismo tiene soporte para cancelar un observable cuando otro observable emite una señal. Esto se provee a través del operador `TakeUntil`:

```cs
var cancel = new Subject<Unit>();
var command = ReactiveCommand
    .CreateFromObservable(
        () => Observable
            .Return(Unit.Default)
            .Delay(TimeSpan.FromSeconds(3))
            .TakeUntil(cancel));

// en algún otro lugar
command.Execute().Subscribe();

// esto cancela la ejecución de arriba
cancel.OnNext(Unit.Default);
```

Por supuesto, normalmente no querrás crear un subject específicamente para la cancelación. Normalmente tendrás algun otro observable que querrás utilizar como señal para cancelar. Un ejemplo obvio es tener un command que cancela a otro:

```cs
public class SomeViewModel : ReactiveObject
{
    public SomeViewModel()
    {
        this.CancelableCommand = ReactiveCommand
            .CreateFromObservable(
                () => Observable
                    .Return(Unit.Default)
                    .Delay(TimeSpan.FromSeconds(3))
                    .TakeUntil(this.CancelCommand));
        this.CancelCommand = ReactiveCommand.Create(
            () => { },
            this.CancellableCommand.IsExecuting);
    }

    public ReactiveCommand<Unit, Unit> CancelableCommand
    {
        get;
        private set;
    }

    public ReactiveCommand<Unit, Unit> CancelCommand
    {
        get;
        private set;
    }
}
```

Aquí tenemos un ViewModel con un command, `CancelableCommand`, que puede ser cancelado al ejecutar otro command, `CancelCommand`. Revisa como `CancelCommand` sólo puede ser ejecutado cuando `CancelableCommand` se está ejecutando.

> **Nota** a primera vista puede parecer que tenemos una depedencia circular irresoluble entre `CancelableCommand` y `CancelCommand`. Sin embargo, fíjate que `CancelableCommand` no necesita resolver su flujo hasta que está en ejecución. Como `CancelCommand` existe antes que `CancelableCommand` es ejecutado, la dependencia circular está resuelta.

### Cancelación utilizando Task Parallel Library

La cancelación en TPL es manejada con `CancellationToken` y `CancellationTokenSource`. Los operadores de Rx proveen ingración con TPL y te permitirán utilizar las sobrecargas para pasar un `CancellationToken` con el que crear tu `Task`. La idea de esta sobrecarga es que el `CancellationTOken` recibirá cancelado si haces dispose de la subscripción. Deberías pasa el token para todas las operaciones asíncronas relevantes. `ReactiveCommand` te provee de sobrecarcas similares para `CreateFromTask`.

Considera el siguiente ejemplo:

```cs
public class SomeViewModel : ReactiveObject
{
    public SomeViewModel()
    {
        this.CancelableCommand = ReactiveCommand
            .CreateFromTask(
                ct => this.DoSomethingAsync(ct));
    }

    public ReactiveCommand<Unit, Unit> CancelableCommand
    {
        get;
        private set;
    }

    private async Task DoSomethingAsync(CancellationToken ct)
    {
        await Task.Delay(TimeSpan.FromSeconds(3), ct);
    }
}
```

Hay muchas cosas importantes en las que fijarse aquí:

1. Nuestro métodp `DoSomethingAsync` recibe un `CancellationToken`
2. Este token es pasado a su vez a `Task.Delay`, y hará que el delay termine si el token es cancelado.
3. Utilizamos una sobrecarga apropiada de `CreateFromTask` que tiene un token como parámetro parar pasarlo a `DoSomethingAsync`. Este token cancelará automáticamente si hacemos dispose a la subscripción de la ejecución.

El código de arriba nos permite hacer algo así:

```cs
var subscription = viewModel
    .CancelableCommand
    .Execute()
    .Subscribe();

// esto cancela la subscripción
subscription.Dispose();
```

Pero que pasa si queremos cancelar la ejecución basándonos en un factor externo, como hicimos con observables? Como sólo tenemos acceso al `CancellationToken` y no al `CancellationTokenSource`, esto no es tan obvio de conseguir.

Además de renunciar completamente a TPL \(lo cual es remendado si es posible, pero no siempre lo más prácticao\), tenemos algunos caminos para conseguirlo. Quizás el camino más sencillo es utilizar `CreateFromObservable` en su lugar:

```cs
public class SomeViewModel : ReactiveObject
{
    public SomeViewModel()
    {
        this.CancelableCommand = ReactiveCommand
            .CreateFromObservable(
                () => Observable
                    .StartAsync(ct => this.DoSomethingAsync(ct))
                    .TakeUntil(this.CancelCommand));
        this.CancelCommand = ReactiveCommand.Create(
            () => { },
            this.CancellableCommand.IsExecuting);
    }

    public ReactiveCommand<Unit, Unit> CancelableCommand
    {
        get;
        private set;
    }

    public ReactiveCommand<Unit, Unit> CancelCommand
    {
        get;
        private set;
    }

    private async Task DoSomethingAsync(CancellationToken ct)
    {
        await Task.Delay(TimeSpan.FromSeconds(3), ct);
    }
}
```

Esta aproximación nos permite utilizar exactamente la misma técnica como Rx puro discutido arriba. La diferencia es que nuestro flujo de ejecución incluye código basado en TPL asíncrono.

