# Commands Asíncronos

Es preferible utilizar `ReactiveCommand` asíncrono en lugar del `ReactiveCommand`más básico para todo, incluso para las tareas mas simples. En ReactiveUI, nunca debes ponser código Interesante™ dentro del bloque Subscribe - Subscribe es solamente para capturar los resultados de operación con Log's o para conectar propiedades con otras propiedades.

## Así sí

```csharp
// In XAML
<Button Command="{Binding Delete}" .../>

public class RepositoryViewModel : ReactiveObject
{
  public RepositoryViewModel() 
  {
    Delete = ReactiveCommand.CreateAsyncObservable(x => DeleteImpl());
    Delete.IsExecuting.ToProperty(this, x => x.IsDeleting, out _isDeleting);
    Delete.ThrownExceptions.Subscribe(ex => this.Log().ErrorException("Something went wrong", ex));
  }

  public ReactiveCommand<Unit> Delete { get; private set; }

  readonly ObservableAsPropertyHelper<bool> _isDeleting;
  public bool IsDeleting { get { return _isDeleting.Value; } }

  public IObservable<Unit> DeleteImpl()
  {
    return Observable.Start(() => /* ... */);
  }
}
```

## Así no

```csharp
// In XAML
<Button Command="{Binding Delete}" .../>

public class RepositoryViewModel : ReactiveObject
{
  public RepositoryViewModel() 
  {
    Delete = ReactiveCommand.Create();
    // This will block the UI thread while DeleteImpl runs
    Delete.Subscribe(async _ => await DeleteImpl());
    // These will not do what you expect
    Delete.IsExecuting.ToProperty(this, x => x.IsDeleting, out _isDeleting);
    Delete.ThrownExceptions.Subscribe(ex => this.Log().ErrorException("Something went wrong", ex));
  }

  public ReactiveCommand<object> Delete { get; private set; }

  readonly ObservableAsPropertyHelper<bool> _isDeleting;
  public bool IsDeleting { get { return _isDeleting.Value; } }

  public IObservable<Unit> DeleteImpl()
  {
    return Observable.Start(() => /* ... */);
  }
}
```

## Porqué?

Gran parte del poder de `ReactiveCommand`viene de la versión asíncrona. En la versión básica las siguientes características no funcionan como se espera:

* `IsExecuting` este observalbe no reportará en tu método asíncrono cuando está dentro de `Subscribe`
* `ThrownExceptions` no capturará nada.
* `CanExecute`no es afectado si el Command está actualmente ejecutándose, permitiendo la posibilidad de múltiples ejecucions al mismo tiempo.



