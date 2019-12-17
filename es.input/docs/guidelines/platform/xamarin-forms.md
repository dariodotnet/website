Title: Xamarin Forms
---

Asegúrate de instalar `reactiveui.xamforms` en tu aplicación.

Your viewmodels should inherit from `ReactiveObject`

- https://reactiveui.net/api/reactiveui/reactiveobject/

Utiliza wireupcontrols

- https://reactiveui.net/docs/handbook/data-binding/xamarin-android/wire-up-controls

Utiliza `ISupportsActivation` y `WhenActivated` para el ciclo de vida

- https://reactiveui.net/api/reactiveui/isupportsactivation/
- https://reactiveui.net/es/docs/handbook/when-activated/

Mantén las referencias a tus subscripciones

- https://reactiveui.net/es/docs/concepts/reactive-programming/subscriptions#lifecycle

Use disposables to manage lifetime, scope and resources:

- https://reactiveui.net/docs/concepts/reactive-programming/disposables

No utilices eventhandlers, utiliza los métodos de extensión disponibles en `reactiveui.events.xamforms`

- https://reactiveui.net/docs/handbook/events/

Utiliza tu concepto normal de Xamarin Forms tal y como usualmente desarrollas con Xamarin Forms, tenermos aglunas clases base que deberías utilias y que exponene observables como `Changed`, `Changing` y `Deactivated` que puedes utilizar para composición.

- https://reactiveui.net/api/reactiveui.xamforms/reactivecarouselpage_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivecontentpage_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivecontentview_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactiveentrycell_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactiveimagecell_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivemasterdetailpage_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivenavigationpage_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactiveswitchcell_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivetabbedpage_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactivetextcell_1/
- https://reactiveui.net/api/reactiveui.xamforms/reactiveviewcell_1/
- https://reactiveui.net/api/reactiveui.xamforms/routedviewhost/
- https://reactiveui.net/api/reactiveui.xamforms/viewmodelviewhost/


