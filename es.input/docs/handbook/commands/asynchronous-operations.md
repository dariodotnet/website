# Operaciones asíncronas

# Operaciones asíncronas con ReactiveCommand

Una de las características más importante de ReactiveCommand es que está contruido para facilitar y orquestar las operaciones asíncronas. En versiones anteriores de ReactiveUI, era en una clase Command separada, pero a partir de la versión 5.0 ya forma parte de éste.

### Commands y CreateAsyncXYZ

Para utilizar ReactiveCommand con operaciones asíncronas, crea tu Command utilizando la familia de métodos `CreateASync`

* **Create** - Crea un ReactiveCommand estándard
* **CreateAsyncObservable** - Crea un Command que devuelve un métdo asíncrono `IObservable<T>`
* **CreateAsyncTask** - Crea un Command que devuelve un métod asíncrono `Task` o `Task<T>`; utiliza este método si quieres escribir un método con `async/await`.

Todos estos métodos parametrizarán el resultado que ReactiveCommand devolverá al ser ejecutado (i.e si tu método asíncrono devuelce `Task<String>`, to Comman será `ReactiveCommand<String>`). Esto siginifica que nuestra subscripción al Command devolcerá un método asíncrono como un Observable.]

ReactiveCommand por si mismo no garantiza que el resultado será devuelto en el hilo de nuestra UI, por lo que puede ser necesario utilizar `ObserveOn`.

Es importante saber que ReactiveCommand es en si mismo in `IObservable` que nunca devolverá complete o OnError - los errores que ocurran en un método asíncrono serán capturados a través de la propiedad `ThrownExceptions`.

Es posible que tus métodos asíncronos puedan lanzar una excepción (la mayoría pueden!), por lo que **debes** subscribirete a `ThrownExceptions1 o la excepción será relanzada en el hilo de la UI.

Aquí un ejemplo simple:

```cs
LoadUsersAndAvatars = ReactiveCommand.CreateAsyncTask(async _ => {
    var users = await LoadUsers();

    foreach(var u in users) {
        u.Avatar = await LoadAvatar(u.Id);
    }

    return users;
});

LoadUsersAndAvatars.ToProperty(this, x => x.Users, out users);

LoadUsersAndAvatars.ThrownExceptions
    .Subscribe(ex => this.Log().WarnException("Failed to load users", ex));
```

### Cómo puedo ejectuar un Command?

El mejor camino para ejecutar ReactiveCommand es via método `ExecuteAsync`:

```cs
LoadUsersAndAvatars = ReactiveCommand.CreateAsyncTask(async _ => {
    var users = await LoadUsers();

    foreach(var u in users) {
        u.Avatar = await LoadAvatar(u.Id);
    }

    return users;
});

var results = await LoadUsersAndAvatars.ExecuteAsync();
Console.WriteLine("You've got {0} users!", results.Count());
```

Es muy importante utilizar **await ExecuteAsync** o sino ho hacer nada! `ExecuteAsync` devuelve un *Cold Observable*, lo que significa que sólo una vez cuando te subscribes a él.

Para el legacy code y para el binding de nuestros framewors de UI, el método Execute siguen funcionando.

### Porqué CreateAsyncTask?

Sabiendo que ReactiveCommand es un Observable en si mismo, es fácil invocar acciones asíncronas basadas en un ReactiveCommand. Algo así:

```cs
searchButton
    .SelectMany(async x => await executeSearch(x))
    .ObserveOn(RxApp.MainThreadScheduler)
    .ToProperty(this, x => x.SearchResults, out searchResults);
```

Sin embargo, cuando un utilizas un patrón de la mano de Rx, algo que finalmente se complicará es la desactivación del Command en si cuando la búsqueda esta en marcha (i.e. prevenir más de una búsqueda simultánea). `CreateAsyncTask` hace que esto funcione por ti.

Otra dificultad en este sentido es que no se pueden manejar excepciones - si `executeSearch` falla una vez, nunca volverá a devolver nada más por el Rx Contract. ReactiveCommand redirige las excepciones a la propiedad `ThrownExceptions`, la cual puede ser manejada.

### Common Patterns

Este ejemplo de UserError nos ilustra como es el uso canónico de CreateAsyncTask:

```cs

// Cuando LoadTweetsCommand es invocado, LoadTweets se ejecutará en
// background, el resultado será observado en el hilo de nuestra UI y 
// ToProperty la pondrá como resultado en una propiedad
LoadTweetsCommand = ReactiveCommand.CreateAsyncTask(() => LoadTweets())

LoadTweetsCommand.ToProperty(this, x => x.TheTweets, out theTweets);

var errorMessage = "The Tweets could not be loaded";
var errorResolution = "Check your Internet connection";

// Cualquier excepción lanzada por LoadTweets finalizarán 
// enviando a través de ThrowExceptions
LoadTweetsCommand.ThrownExceptions
    .Select(ex => new UserError(errorMessage, errorResolution))
    .Subscribe(x => UserError.Throw(x));
```
