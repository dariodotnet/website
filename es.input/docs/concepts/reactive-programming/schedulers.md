Order: 10
Title: Schedulers
---

.https://staltz.com/primer-on-rxjs-schedulers.html

Un par de cosas que deberías ser de ayuda, RxUI tiene algunas métodos de ayuda para complementar los schedulers y se los puedes pasar así ImmediateScheduler.Instance - esto es porque limita la acción y deja de compartir schedulers entre pruebas.

Necesitarás el paquete de Testing y entonces podrás hacer, e.g.
```
using(TestUtils.WithScheduler(ImmediateScheduler.Instance)){
 // your test here
}
```
documentación aquí https://reactiveui.net/api/reactiveui.testing/testutils/949c3f58

(internamente, esto hace lo que tiene que hacer en todo momento, pero después se ordena a si mismo)

Otra cosa que utilizamos en otras pruebas (e.g. si estás esperando el cambio de valor de un observable durante algunos escenarios) es `CreateCollection`, e.g https://github.com/reactiveui/ReactiveUI/blob/develop/src/ReactiveUI.Tests/ReactiveCommandTest.cs#L35

La ventaja de este modo es que puedes jugar con el scheduler, lo cual es fantástico si estás utilizando un `TestSheduler`porque no te tienes que preocupar de irlos ordenando por tu cuenta (e.g. (new TestScheduler().With(sched => {}));)

Cada _thread- tiene su propio scheduler asignado y es la extensión `With` la que lo maneja por ti. Esto signifinca que si tus pruebas son asíncronas, en teoría los schedulers no se pisarán entre ellos.
