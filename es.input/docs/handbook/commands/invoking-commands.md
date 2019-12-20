# Invocando Commands

Aveces puede ser conveniente ejecutar un command en respuesta a algún observable que no está atada una interacción por parte del usuario. Por ejemplo, una característica que automáticamente guarda el documento actual ejecutando `SaveCommand` cada 5 minutos. La extensión `InvokeCommand` hace que esto sea sencillo de conseguir:

```cs
var interval = TimeSpan.FromMinutes(5);
Observable
    .Timer(interval, interval)
    .InvokeCommand(this.ViewModel, x => x.SaveCommand);
```

> **Sugerencia** `InvokeCommamnd` respeta el `CanExecute` del command. Esto es, si el método `CanExecute` devuelve `false`, `InvokeCommand` no ejecutará el command durante esa emisión del observable.