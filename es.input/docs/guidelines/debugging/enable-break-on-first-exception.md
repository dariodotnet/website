# Visual Studio for Mac

Esto es como Rx debugging pro-tip #1:

En las opciones de `Breackpoints`, click en `New Exception Catchpoint` y teclea `System.Exception` para activarlo. Las excepciones con subscripciones terminan la subscripción. Estos ejecutar "onError" en lugar de "onNext" o "onCompleted". Esto te permite capturar e imaginar antes de lanzarse "onError".
