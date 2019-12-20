# Binding

Una vez has creado un command y lo tienes expuesto desde tu ViewModel, el siguiente paso lógico es consumirlo desde tu View. El modo más común de conseguirlo es utilizando el método `BindCommand`, Este método, - que tiene muchas sobrecargas - es el responsable de vincularse con algún control que exponga un `ICommand`. Su uso típico ser verás así:

```cs
// in a view
this.BindCommand(
    this.ViewModel,
    x => x.MyCommand,
    x => x.myControl);
```

Aquí hacemos un bind del command de `myControl` con `MyCommand` que tenemos expuesto en nuestro ViewModel. Lo que sucede después depende de cualquier instancia de `ICreatesCommandBinding` registrada en el [service locator](https://reactiveui.net/es/docs/handbook/dependency-inversion/). Sin embargo, normalmente `myControl` estará desactivado mientras el command no esté disponible. Adicionalmente, realizar alguna acción predeterminada de `myControl`ejecutará el command. Por ejemplo, si `myControl`es un botón, la acción requerida será un click (o un tap).

> **Nota** El siguiente ejemplo muestra una llamada a `BindCommand` pero se ejecutará dentro de un bloque de `WhenActivates`:
> 
> ```cs
> this.WhenActivated(
>     d =>
>     {
>         d(this.BindCommand(
>             this.ViewModel,
>             x => x.MyCommand,
>             x => x.myControl));
>     });
> ```
> 
> Por favor, revisa [la documentación sobre `WhenActivated`](https://reactiveui.net/es/docs/handbook/when-activated/) para más información

El modo mostrado arriba para `BindCommand` no nos ofrece una pista sobre qué event dispara la ejecución del command. Por lo tando, se utilizará un event por defecto (como puede ser `Click` o `Tapped`). Si, por otro lado, quieres atar la ejecución de tus commands a algún event por defecto, puedes utilizar una sobrecarga del método `BindCommand` pasando el nombre del event como parámentro:

```cs
this.BindCommand(
    this.ViewModel,
    x => x.MyCommand,
    x => x.myControl,
    nameof(myControl.SomeEvent));
```

Aquí, el `SomeEvent` de `myControl` será utiliado para disparar la ekecución del command en lugar del event por defecto.

> **Nota** Cuando utilizamos esta sobrecarga dentro de `WhenActivated`, es importante hacer dispose del binding cuando desactivamos la View. `BindCommand` se subscribirá al evento cada vez que la vista se active, y si el binding no hace el dispose, no se desuscribirá del event. Esto llevará a múltiples subscripcciones del event, lo que cual hará que el comando se ejecute una vez por cada subscripción activa del event.

Finalmente, `BindCommand` también nos provee de sobrecargas que nos permite especificar un parámetro que pasaremos a la ejecución de nuestro command. El parámetro es provisto como una función, un observable un una expresión que resuelva una propiedad en el ViewModel:

```cs
// pasando un contador de ejecucuón como parámetro del command
var count = 0;
this.BindCommand(
    this.ViewModel,
    x => x.MyCommand,
    x => x.myControl,
    () => count++);

// Utilizando un observable como parámetro del command
IObservable<int> param = ...;
this.BindCommand(
    this.ViewModel,
    x => x.MyCommand,
    x => x.myControl,
    param);

// Utilizando una propiedad de nuestro VM como parámetro de nuestro command
this.BindCommand(
    this.ViewModel,
    x => x.MyCommand,
    x => x.myControl,
    x => x.SomeProperty);
```
