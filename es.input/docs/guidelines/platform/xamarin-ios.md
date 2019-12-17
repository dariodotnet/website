Title: Xamarin iOS
----

Tus ViewModels deberían heredar de `ReactiveObject`

- https://reactiveui.net/es/api/reactiveui/reactiveobject/

Utiliza `ISupportsActivation` y `WhenActivated` para el ciclo de vida

- https://reactiveui.net/api/reactiveui/isupportsactivation/
- https://reactiveui.net/es/docs/handbook/when-activated/

Mantén las referencias a tus subscripciones

- https://reactiveui.net/docs/concepts/reactive-programming/subscriptions#lifecycle

Utiliza disposables para manejar el ciclo de vida, alcance y recursos:

- https://reactiveui.net/docs/concepts/reactive-programming/disposables

No utilices eventhandlers, utiliza los métodos de extensión disponibles en `reactiveui.events`

- https://reactiveui.net/docs/handbook/events/

Utiliza tu concepto normal de iOS tal y como usualmente desarrollas con iOS, tenermos aglunas clases base que deberías utilias y que exponene observables como `Changed`, `Changing` y `Deactivated` que puedes utilizar para composición.

- https://reactiveui.net/api/reactiveui/reactivetableviewcontroller/
- https://reactiveui.net/api/reactiveui/reactivetableview/
- https://reactiveui.net/api/reactiveui/reactivecollectionview/
- https://reactiveui.net/api/reactiveui/reactivecollectionviewcontroller
- https://reactiveui.net/api/reactiveui/reactivenavigationcontroller
- https://reactiveui.net/api/reactiveui/reactivetabbarcontroller/
- https://reactiveui.net/api/reactiveui/reactivepageviewcontroller/
