## Command errores

Si la lógica provista para tu `ReactiveCommand` puede fallar en algún punto, necesitar tratar con los posible errores.

Para la ejecución del command, el flujo que recibes desde `Execute` emitirá algunos errores que ocurran en tu lógica de ejecución. Sin embargo, la subscripción a este observable es a menudo inducida por la infraestructura de binding. Por lo tanto, es posible que no puedas recibir errores al observar la ejecución.

Para este dilema, `ReactiveCommand` incluye el observable `ThrownExceptions` (de tipo `IObservable<Exception>`). Cualquie error que ocurra durante la ejecución la lógica *también* emitirá este observable. Si no tienes una subscripción a éste, ReactiveUI te tirará tu aplicación. Esto te fuerza a crear un manejo de errores consistente.

Puede ser tentador añadir *siempre* una subscripción a `ThrownExceptions`, incluso si el único recurso es hacer log del problema. Sin embargo, puede ser interante tratar esto como otra estrategia para manejar excepciones cuando tengas una compensación. Si, por ejemplo, tu command sólo actualiza una propiedad de tu ViewMode y nunca debería fallar, una subscripción a `ThrownExceptions` sólo servirá para ocultar problemas de implementación. Dicho esto, ten cuidad con problemas intermitentes, como los errores de acceso a red o archivos. Como siempre, una buena cobertura de pruebas te ayudará a identificar cuando una subscripción a `ThrownExceptions` tiene sentido.

> **Nota** Tu flojo para controlar `canExecute` también tiene potencial para producir un error. Este tipo de casos suelen ser errores de programador, ya que no quieres un flujo para controlar el flujo de `canExecute` acabe en error. A pesar de esto, estos errores también se emitirán a través de `ThownExceptions`.
