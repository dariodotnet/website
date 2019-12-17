Title: Xamarin Android
---

Tus ViewModels deberían heredar de `ReactiveObject`

- https://reactiveui.net/es/api/reactiveui/reactiveobject/

Utiliza `ISupportsActivation` y `WhenActivated` para el ciclo de vida

- https://reactiveui.net/api/reactiveui/isupportsactivation/
- https://reactiveui.net/es/docs/handbook/when-activated/

Mantén las referencias a tus subscripciones

- https://reactiveui.net/es/docs/concepts/reactive-programming/subscriptions#lifecycle

Utiliza disposables para manejar el ciclo de vida, alcance y recursos:

- https://reactiveui.net/es/docs/concepts/reactive-programming/disposables

No utilices eventhandlers, utiliza los métodos de extensión disponibles en `reactiveui.events.winforms`

- https://reactiveui.net/es/docs/handbook/events/

Utiliza tu concepto normar de Android tal y como usualmente desarrollas con Android, tenermos aglunas clases base que deberías utilias y que exponene observables como `Changed`, `Changing` y `Deactivated` que puedes utilizar para composición.

- https://reactiveui.net/api/reactiveui.androidsupport/reactiveappcompatactivity_1/
- https://reactiveui.net/api/reactiveui/reactiveactivity/
- https://reactiveui.net/api/reactiveui/reactivefragment/
- https://reactiveui.net/api/reactiveui.android.support/reactiverecyclerviewadapter_1/
- https://reactiveui.net/api/reactiveui.androidsupport/reactiveactionbaractivity
- https://reactiveui.net/api/reactiveui.androidsupport/reactivefragment/
- https://reactiveui.net/api/reactiveui.androidsupport/reactivedialogfragment/
- https://reactiveui.net/api/reactiveui/reactivefragmentactivity/
- https://reactiveui.net/api/reactiveui/reactivelistadapter_1/
- https://reactiveui.net/api/reactiveui/reactivepageradapter_1/
- https://reactiveui.net/api/reactiveui/reactivepreferencefragment/
- https://reactiveui.net/api/reactiveui/reactivepreferenceactivity/

Hay algunos métodos de extensión que harán tu vida más fácil.

- https://reactiveui.net/api/reactiveui.androidsupport/controlfetchermixin/
- https://reactiveui.net/api/reactiveui/sharedpreferencesextensions/
- https://reactiveui.net/api/reactiveui/usbmanagerextensions/
- https://reactiveui.net/api/reactiveui/androidobservableforwidgets/
- https://reactiveui.net/api/reactiveui/flexiblecommandbinder/
