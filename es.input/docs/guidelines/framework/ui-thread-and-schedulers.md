# UI Thread y Schedulers
Siempre queremos estar seguros de actualizar la UI en el `RxApp.MainThreadScheduler` para asegurarnos de que los cambios que suceden en nuestra UI se hacen en el hilo de la UI. En la práctica, esto significa que nos aseguramos de actualizar el ViewModel en el main thread scheduler.

## Así sí
```csharp
FetchStuffAsync()
  .ObserveOn(RxApp.MainThreadScheduler)
  .Subscribe(x => this.SomeViewModelProperty = x);
```

## Mejor
Todavía mejor, pasa el scheduler a la operación asíncrina - a menudo es necesario para tareas mas complejas.

```csharp
FetchStuffAsync(RxApp.MainThreadScheduler)
  .Subscribe(x => this.SomeViewModelProperty = x);
```

## Así no
```csharp
FetchStuffAsync()
  .Subscribe(x => this.SomeViewModelProperty = x);
```

