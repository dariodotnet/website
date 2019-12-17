# Windows Forms

Asegúrate de instalar `reactiveui.winforms` en tu aplicación.

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

Utiliza tu concepto normal de WinForms tal y como usualmente desarrollas con WinForms. Hay algunos métodos de extensión que harán tu vida más fácil.

- https://reactiveui.net/api/reactiveui/reactiveusercontrol_1/
- https://reactiveui.net/api/reactiveui/iviewfor_1/
- https://reactiveui.net/api/reactiveui/routedviewhost/
- https://reactiveui.net/api/reactiveui/autodatatemplatebindinghook/
