# Es preferible ObservableAsPropertyHelpers en lugar de establecer propiedades explícitamente

Cuando el valor de una propiedad, un conjunto de propiedades o un stream observable, depende de otra propiedad, en lugar de establecer el valor explítiamente, utiliza `ObservableAsPropertyHelper` con `WhenAny`siempre que puedas.

## Así sí

```csharp
public class RepositoryViewModel : ReactiveObject
{
  public RepositoryViewModel()
  {
    canDoIt = this.WhenAny(x => x.StuffFetched, y => y.OtherStuffNotBusy, (x, y) => x && y)
      .ToProperty(this, x => x.CanDoIt);
  }

  readonly ObservableAsPropertyHelper<bool> canDoIt;
  public bool CanDoIt
  {
    get { return canDoIt.Value; }  
  }	
}
```

## Así no

```csharp
this.WhenAny(x => x.StuffFetched, y => y.OtherStuffNotBusy, (x, y) => x && y)
  .ObserveOn(RxApp.MainThreadScheduler)
  .Subscribe(x => CanDoIt = x);
```

## Porqué?
1. `ObservableAsPropertyHelper` es como una "prueba" de que tu propiedad sólo tiene un camino de cambio (aprovechando además que podemos establecerla utilizando `ToProperty`). Si utilizásemos una propiedad básica, se puede establecer el valor desde diferentes sitios, creando el spagetti code.
2. `ObservableAsPropertyHelper` se encargará de disparar los eventos de `INotifyPropertyChanged` - si creas propiedades read-only, puedes ahorrar mucho código repetitivo.
3. `WhenAny` te permite combinar múltples propiedades, tratar los cambios como un stream observable y crear retornos espcíficos para tu ViewModel.
4. Scheduling. Simplemente pasa como parámetro un valor `scheduler`, el cual te evitará la necesidad de llamar a `ObserveOn` por ti mismo.
5. Laziness. Simplemente establece `deferSubscription` a `true` y ahora tu propiedad tendrá una evaluación perezosa y así la subscripción a tu observable se hará en diferido en la primera llamada.


