# Commands

Es preferible que las interacciones de tu usuarios hagan Binding a Commands en lugar de métodos.

## Así sí

```csharp
// In XAML
<Button Command="{Binding Delete}" .../>;

public class RepositoryViewModel : ReactiveObject
{
  public RepositoryViewModel() 
  {
    Delete = ReactiveCommand.CreateAsyncObservable(x => DeleteImpl());
    Delete.ThrownExceptions.Subscribe(ex => /*...*/);
  }

  public ReactiveAsyncCommand Delete { get; private set; }

  public IObservable<Unit> DeleteImpl() {...}
}
```

## Así no

Utiliza las convenciones de Caliburn.Micro para asociar botones y Commands:

```csharp
// In XAML
<Button x:Name="Delete" .../>

public class RepositoryViewModel : PropertyChangedBase
{
  public void Delete() {...}    
}
```

## Porqué?

* `ReactiveCommand` expone la propiedad `CanExecute` para habilitar 
comportamientos adicionales en nuestras aplicaciones.
* Manejamos que el resultado vuelva al hilo de nuestra UI.
* Manejamos elementos al vuelo.



