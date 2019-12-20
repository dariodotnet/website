# Controlando CanExecute

`ReactiveCommand` automáticamente pasa a no disponible cuando se está ejecutando. Esto es, el observable `CanExecute` emitirá `false` cuando comience su ejecución y `true` cuando la ejecución se complete. Sin embargo, hay momentos que querrás tomar el control de la ejecutabilidad de tus commands. Por ejemplo, quieres que tu command de login se deshabilite hasta que el usuario haya introducido los datos de nombre y contraseña.

Para hacer esto, puedes pasar un `IObservable<bool>` como parámetro `canExecute` al crear el command:

```cs
var canExecute = this
    .WhenAnyValue(
        x => x.UserName,
        x => x.Password,
        (u, p) => !string.IsNullOrEmpty(u) && !string.IsNullOrEmpty(p));
var command = ReactiveCommand.CreateFromObservable(this.LogOnAsync, canExecute);
```

Aquí, `command` tiene una restricción de ejecución adicional. A saber, ambos `UserName` y `Password` deben ser provistos. Por supuesto, `command` no permanece disponible durante la ejecución de `LogOnAsync`, incluso si `UserName` y `Password` son válidos actualmente. En otras palabras, los *suplementos* para el comportamiento de `canExecute`; no reemplazan el comportamiento por defecto.

> **Atención** Por razones de rendimiento, no observes el parámetro encargado de `canExecute` de tu `ReactiveCommand` en el main thread. Tal vez quieras observar las emisiones del observable encargado de `canExecute` en el main thread, así que asegúrate de añadir una llamada a `ObserveOn` si es necesario.
