# Dispose para tus subscripciones

http://www.introtorx.com/content/v1.0.10621.0/03_LifetimeManagement.html

```
this.WhenActivated(
    disposables =>
    {
        this.WhenAnyValue(...)
            .DisposeWith(disposables);
    });
```

Ver también https://reactiveui.net/docs/handbook/when-activated/

No _todas_ las subscripciones necesitan hacer Dispose. Como los eventos. Si un componente expone un evento y se subscribe a si mismo, no necesita desusbribirse. Esto es porque las subscripción es la realiza el componente referenciándose a si mismo. Lo mismo ocurre con Rx. Si tienes un ViewModel y por ejemplo utilizas `WhenAnyValue` de una de sus propiedades, no es necesario hacer Dispose porque de un modo manifiesto el ViewModel hace referencia a si mismo.
