Title: Disposables
---

ReactiveUI te provee de [WhenActivated](/docs/handbook/when-activated) que ayuda a manejar el ciclo de vida y las Reactive Extensions proveen de muchas implementaciones de la interface IDisposable para ayudarte a manejar el tiempo de vida, el radio de acción y los recursos.

# Disposable

# BooleanDisposable

# CancellationDisposable

# CompositeDisposable
https://msdn.microsoft.com/en-us/library/system.reactive.disposables.compositedisposable(v=vs.103).aspx

# ContextDisposable

# MultipleAssignmentDisposable

# RefCountDisposable

# ScheduledDisposable

# SerialDisposable
* Es unos de los disposable más utilizados
* Lo más útil es cuando se define el disposable, lo anterior es Disposed
* Algunas veces sólo necesitas manejar algo que sólo estará vigente un tiempo
* O si lo defines como Disposable. Vacío, cero o uno de algo
* Es atómico y thread safe e inmune al doble-disposing

https://msdn.microsoft.com/en-us/library/system.reactive.disposables.serialdisposable(v=vs.103).aspx

# SingleAssignmentDisposable

Para una explicación completa de cada implementación, revisa Disposables

# Ver también
* http://www.introtorx.com/content/v1.0.10621.0/20_Disposables.html
