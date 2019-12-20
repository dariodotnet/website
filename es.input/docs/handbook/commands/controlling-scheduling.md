# Controlando Scheduling

Por defecto, `ReactiveCommand` utiliza `RxApp.MainThreadScheduler` para elevar los events. Esto es, los valores emitidos para `CanExecute`, `IsExecuting`, `ThrownExceptions`, y el propio resultado del command. Típicamente, los componentes de la UI están suscritos a estos observables, esto es sensible por defecto. Sin embargo, cuando escribies pruebas unitarias para tus ViewModels, debes querer más controls sobre el scheduling.

> **Atención** Es importante entender que la lógica de ejecución para un `ReactiveCommand` *no* es ejecutada en el scheduler provisto (es como el caso del observable provisto para `canExecute`). En su lugar, permite al método que lo llama implementar un scheduler requerido dentro del flujo de ejecución. Esto significa que es totalmente posible ejecutar tu lógica en un hilo distinto al propuesto por el propietario del scheduler:
>
> ```cs
> var command = ReactiveCommand.Create(() => Console.WriteLine(Environment.CurrentManagedThreadId), outputScheduler: RxApp.MainThreadScheduler);
>
> // esto mostrará el ID del hilo desde el que se hace la llamada, no necesariamente el ID del main thread
> command.Execute().Subscribe();
> ```

Todos los métodos `Create*` tiene un parámetro opcional `outputScheduler`, que puedes pasar un scheduler si lo necesitas:

```cs
var command = ReactiveCommand.Create(() => {}, outputScheduler: someScheduler);
```

> **Nota** Si estás utilizando el método de extensión `With` de ReactiveUI en tus pruebas, puedes crear commands utilizando el comportamiento del scheuling por defecto. Esto es porque el método de extensión `With` cambiará el scheduler que proveas a `RxApp.MainThreadScheduler`.
