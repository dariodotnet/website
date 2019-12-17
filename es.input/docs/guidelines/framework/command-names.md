# Command Nombres

No utilices el sufijo `Command` para tus `ReactiveCommand`; en su lugar, utiliza un vervo que decriba la acción del Command. Por ejemplo:
Don't suffix `ReactiveCommand` properties' names with `Command`; instead, name the property using a verb that describes the command's action. For example:

```csharp
public ReactiveCommand Synchronize { get; private set; }

// and then in the ctor:
Synchronize = ReactiveCommand.CreateAsyncObservable(
  _ => SynchronizeImpl(mergeInsteadOfRebase: !IsAhead));

```

Cuando la implementación de un `ReactiveCommand` es demasiado larga o compleja para un delegate anónimo, nombra tu implementación con el mismo nombre del Command pero utilizando el sufijo `Impl` (por ejemplo, `SyncronizeImpl` como se muestra arriba).
