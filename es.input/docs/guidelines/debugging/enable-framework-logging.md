# Activa Framework Logging

La información de Debug es escrita por el framework a Splat. Por defecto, [splat se envía con un looger nulo como "Debug.WriteLine" eliminado por el compilador cuando Splat es empaquetado](https://github.com/reactiveui/splat/issues/46). Escribe una implementación de `ILogger` como la de abajo para ver los mensajes:

```csharp
public class LoggingService : ILogger
{
    public LogLevel Level { get; set; }

    public void Write(string message, LogLevel logLevel)
    {
        if ((int)logLevel < (int)Level)
        {
            return;
        }

        switch (logLevel)
        {
            case LogLevel.Warn:
            case LogLevel.Error:
            case LogLevel.Fatal:
            case LogLevel.Debug:
            case LogLevel.Info:
            default:
                Debug.WriteLine(message);
                break;
        }
    }
}
```

Entonces en la raiz de tu composición, registra tu implementación

```csharp
public void ConfigureLogging()
{
#if DEBUG
    var logger = new LoggingService { Level = LogLevel.Debug };
    Locator.CurrentMutable.RegisterConstant(logger, typeof(ILogger));
#endif
}
```
