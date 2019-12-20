# Patrones comunes

Este ejemplo desde `UserError` también islustra el uso habitual de `CreateAsyncTask`:

```cs

// Cuando LoadTweetsCommand es invocado, LoadTweets se ejecutará en 
// background, el resultado ser Observado en el Main thread, y 
// ToProperty lo almacenará en la propiedad de salida
LoadTweetsCommand = ReactiveCommand.CreateAsyncTask(() => LoadTweets())

LoadTweetsCommand.ToProperty(this, x => x.TheTweets, out theTweets);

var errorMessage = "The Tweets could not be loaded";
var errorResolution = "Check your Internet connection";

// Cualquier excepción lanzada por LoadTweets será
// enviada a través de ThrownExceptions
LoadTweetsCommand.ThrownExceptions
    .Select(ex => new UserError(errorMessage, errorResolution))
    .Subscribe(x => UserError.Throw(x));
```
