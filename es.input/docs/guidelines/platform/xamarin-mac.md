Title: Xamarin Mac
----

Esta plataforma tiene dos librerías base diferentes:

* Mobile la cual es un subconjunto de NET45 personalizado, aligerado y restrictivo que excluye muchos Windowsisms.

> /Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib/mono/Xamarin.Mac/

* XM 4.5 que luce, actua y se comporta como un programa normal de .NET, pero no incluye cosas como System.Drawing y System.Windows.Forms en OSX. Esto te permite fácilmente consumir librerías estandard de escritorio con targe NET45 vía NuGuet siempre que la biblioteca no dependa de ensamblados no disponibles en XM45.

> /Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib/mono/4.5/

_ReactiveUI-Events_ y _ReactiveUI_ utilizan Xamarin.Mac Mobile pero pueden ser consumidas por aplicaciones Xamarin Mac XM 4.5. Deberíamos poder lanzar assemblies XM45 con el prohecto pero [es actualmente imposible hacerlo](https://github.com/NuGet/Home/issues/2662).

Tus ViewModels deberían heredar de `ReactiveObject`

- https://reactiveui.net/api/reactiveui/reactiveobject/

Utiliza `ISupportsActivation` y `WhenActivated` para el ciclo de vida

- https://reactiveui.net/api/reactiveui/isupportsactivation/
- https://reactiveui.net/docs/handbook/when-activated/

Mantén las referencias a tus subscripciones

- https://reactiveui.net/docs/concepts/reactive-programming/subscriptions#lifecycle

Utiliza disposables para manejar el ciclo de vida, alcance y recursos:

- https://reactiveui.net/docs/concepts/reactive-programming/disposables

No utilices eventhandlers, utiliza los métodos de extensión disponibles en `reactiveui.events`

- https://reactiveui.net/docs/handbook/events/

Utiliza tu concepto normal de iOS tal y como usualmente desarrollas con OSX, tenermos aglunas clases base que deberías utilias y que exponene observables como `Changed`, `Changing` y `Deactivated` que puedes utilizar para composición.

- https://reactiveui.net/api/reactiveui/reactivetableviewcontroller/
- https://reactiveui.net/api/reactiveui/reactivetableview/
- https://reactiveui.net/api/reactiveui/reactivecollectionview/
- https://reactiveui.net/api/reactiveui/reactivecollectionviewcontroller
- https://reactiveui.net/api/reactiveui/reactivenavigationcontroller
- https://reactiveui.net/api/reactiveui/reactivetabbarcontroller/
- https://reactiveui.net/api/reactiveui/reactivepageviewcontroller/
