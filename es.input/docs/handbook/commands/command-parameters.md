# Command Parameters

Opcionalmente, la lógica de ejecución de tus commands pueden tener un parámetro. Para hacerlo, sólo necesitas utilizar la sobrecarga apropiada de `Create*` cuando crees tu `ReactiveCommand`:

```cs
// command síncrono recibiendo un parámetro
var command1 = ReactiveCommand.Create<int>(param => Console.WriteLine("Received parameter with type {0}: {1}.", param.GetType().Name, param);
// esto resuelve "Received parameter with type Int32: 42"
command1.Execute(42);

// command asíncrono recibiendo un parámetro
var command2 = ReactiveCommand.CreateFromObservable<int>(param => Observable.Return(param).Do(p => Console.WriteLine("Received parameter with type {0}: {1}.", p.GetType().Name, p)));
// esto resuelve "Received parameter with type Int32: 42"
command2.Execute(42);
```

El tipo de parámetro es capturado como `TParam` en `ReactiveCommand<TParam, TResult>`. El tipo de ambos `command1` y `command2` de arriba es `ReactiveCommand<int, Unit>`.

Generalmente, deberías evitar el uso de parámetros en command. Habitualmente es más apropiado para tu ViewModel definir propiedades en las que, en base a su estado, se ejecuta la lógica de tus commands.