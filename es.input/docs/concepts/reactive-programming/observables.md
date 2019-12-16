Order: 5
---

Los usuarios esperan datos en tiempo real. Quieren sus tweets ahora. Quiere confirmar el pedido ya. Necesitan precios específicos actualizados. Sus juegos online necesitan ser responsivos. Como desarrolladores, demandas la opción de dispara-y-olvida. No quieres quedarte bloqueado esperando un resultado. Quieres tener el resultado cuando esté listo. Todavía mejor, cuando trabajamos con conjuntos de resultados, quieres recibirlos individualmente cuando estén listos. No quieres esperar a que todo el conjunto sea procesado antes de que puedas ver el primero. El mundo se ha movido al ya; los usuarios esperan que lo hagamos. Los desarrolladores tienen herramientas para hacerlo, es fácil. Los desarrolladores necesitan herramientas para reaccionar.

Rx y específicamente IObservable<T> no es un reemplazo para IEnumerable<T>. No recomendaría intentar tener algo que es naturalmente para esperar y forzarlo a que sea para activar.

Traduciendo un IEnumerable<T> existente a valores IObservable<T> es hacer que nuestro código pueda ser "más Rx" con mensajes encolados. Las colas, como en MSMQ o una implementación general de JMS tienen transaccionalidad y son por definición secuenciales. Siento que IEnumerable<T> se ajusta de un modo más natural.
Por difinición la mejor herramienta para trabajar con tu código debería ser sencillo de mantener, proveer mejor rendimiento y probablemente tener mejor soporte.