# Command Values

La ejecución de un command produce un valor, que es capturado como `TResult` en `ReactiveCommand<TParam, TResult>`. Si no necesitas devolver nada especial, puedes utilizar `Unit` para `TResult`. En efecto, esto sucederá automáticamente si utilizas alguna sobrecarga del método `Create*` que no devuelva valor:

```cs
// un command síncrono que no devuelve un valor
var command1 = ReactiveCommand.Create(() => { });

// un command asíncrono basado en observable que no devuelve un valor 
var command2 = ReactiveCommand.CreateFromObservable(() => Observable.Return(Unit.Default));

// un command asíncrono basado en task que no devuelve un valor
var command3 = ReactiveCommand.CreateFromTask(async () => await Task.Delay(TimeSpan.FromSeconds(2)));
```

Todos los commands de arriba son de tipo `ReactiveCommand<Unit, Unit>`. Nota que `CreateFromObservable` es necesario que eventualmente devuelva un `IObservable<T>`, por lo que `T` debe ser conocido. Por lo tanto, para crear un `ReactiveCommand<Unit, Unit>` utilizando `CreateFromObservable`, debes asegurarte que tu observable es de tipo `IObservable<Unit>`.

Si tu _quieres_ devolver algo cada vez que tu command se ejecuta, sólo necesitas utilizar el método `Create*` apropiado:

```cs
// un command síncrono que siempre devuelve 42 tras la ajecución
var command1 = ReactiveCommand.Create(() => 42);

// un command asíncrono basado en observable que siempre devuelve 42 tras la ajecución
var command2 = ReactiveCommand.CreateFromObservable(() => Observable.Return(42));

// un command asíncrono basado en Task que siempre devuelve 42 tras la ajecución
var command3 = ReactiveCommand.CreateFromTask(() => Task.FromResult(42));
```

Aquí, todos los commands son de tipo `ReactiveCommand<Unit, int>`. Subscribiéndos al observable devuelto por `Execute` emitirá el valor `42`.
