# Utiliza nombres decriptivos al usar `WhenAny`
En situaciones donde estas detectando cambios en múltiples expresiones, asegúrate de nombrar las variables convenientemente en el `selector`

## Así sí
```csharp
public class MyViewModel : ReactiveObject
{
  public MyViewModel()
  {
    this.WhenAny(
        x => x.SomeProperty.IsEnabled,
        x => x.SomeProperty.IsLoading,
        (isEnabled, isLoading) => isEnabled.Value && isLoading.Value)
        .Subscribe(_ => DoSomething());
  }
  
  // other code here
}
```

## Así no
```csharp
public class MyViewModel : ReactiveObject
{
  public MyViewModel()
  {
    this.WhenAny(
        x => x.SomeProperty.IsEnabled,
        x => x.SomeProperty.IsLoading,
        (x, y) => x.Value && y.Value)
        .Subscribe(_ => DoSomething());
  }
  
  // other code here
}
```

## Porqué?
Esto ayuda mucho con la legibilidad de expresiones complejas, particularmente cuando trabajamops cobn valores de tipo boolean.