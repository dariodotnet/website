# Casi siempre utiliza `this`a la izquierda del método `WhenAny`.

## Así sí
```csharp
public class MyViewModel
{
  public MyViewModel(IDependency dependency)
  {
    Ensure.ArgumentNotNull(dependency, "dependency");

    this.Dependency = dependency;

    this.stuff = this.WhenAny(x => x.Dependency.Stuff, x => x.Value)
      .ToProperty(this, x => x.Stuff);
  }

  public IDependency Dependency { get; private set; }

  readonly ObservableAsPropertyHelper<IStuff> stuff;
  public IStuff Stuff
  {
    get { return this.stuff.Value; }
  }
}
```

## Así no
```csharp
public class MyViewModel(IDependency dependency)
{
  stuff = dependency.WhenAny(x => x.Stuff, x => x.Value)
    .ToProperty(this, x => x.Stuff);
}
```

## Porqué?
* El ciclo de vida de `dependency` es desconocido - si es un singleton, podría introducir un memory leak en tu aplicación.