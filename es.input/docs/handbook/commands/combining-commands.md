## Combinando Commands

Aveces puede ser muy útil tener varios commands funcionando como uno solo. Por poner un ejemplo, considera un navegador que permite al usuario limpiar la caché individual \(historial, descargas, cookies\), o limpiar todas las cachés. Habría un comando para limpiar cada caché de forma individual, cada uno de los cuales tiene la lógica para permitier la ejecuión del command. Esto sería propenso a errores si tenemos que repetir o combinar todas las lógicas para el command que limpia todas las cachés. Combinando command te permite un modo elegante en este tipo de situaciones:

```cs
IObservable<bool> canClearBrowsingHistory = ...;
var clearBrowsingHistoryCommand = ReactiveCommand.CreateFromObservable(
    this.ClearBrowsingHistory,
    canClearBrowsingHistory);

IObservable<bool> canClearDownloadHistory = ...;
var clearDownloadHistoryCommand = ReactiveCommand.CreateFromObservable(
    this.ClearDownloadHistory,
    canClearDownloadHistory);

IObservable<bool> canClearCookies = ...;
var clearCookiesCommand = ReactiveCommand.CreateFromObservable(
    this.ClearCookies,
    canClearCookies);

// combine all these commands into one "parent" command
// combinar todos los commands en un único command
var clearAllCommand = ReactiveCommand
    .CreateCombined(
        new [] { clearBrowsingHistoryCommand, clearDownloadHistoryCommand, clearCookiesCommand });
```

Cuando se ejecute el command combinado, ejecutará sus commands hijos asíncronamente.

El command combinado respeta la posibilidad de ejecutar los commands hijos. Esto es, si uno de los command hijos no puede ser ejecutado, ninguno puede en el command combinado. Adicionalmente, también es posible utilizar una lógica _extra_ para comprobar si es posible ejecutar un command combinado al crearlo:

```cs
IObservable<bool> canClearAll = ...;
var clearAllCommand = ReactiveCommand
    .CreateCombined(
        new [] { clearBrowsingHistoryCommand, clearDownloadHistoryCommand, clearCookiesCommand },
        canClearAll);
```

En este caso, `clearAllCommand` sólo será ejecutable si todos los hijos del command son ejecutables _y_ el último valor de `canClearAll` es `true`.

> **Atención** Todos los command hijos dentro del método `CreateCombined` deben ser del mismo tipo. No puedes combinar, digamos, un `ReactiveCommand<Unit, Unit>` con un `ReactiveCommand<int, Unit>`. Esto es poque todos los commands hijos reciben como parámetro el provisto por el command combinado y el resultado de la ejecución del command combinado es una lista de resultados de los hijos.



