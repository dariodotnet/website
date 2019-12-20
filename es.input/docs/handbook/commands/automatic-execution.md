# Ejecución automática
No lo hagas desde el ViewModel, invócalo desde la View en su lugar!

Revisa -> https://codereview.stackexchange.com/questions/74642/a-viewmodel-using-reactiveui-6-that-loads-and-sends-data

Lo único que cambiaría es no llamar inmediatamente a LoadItems.ExecuteAsyncTask en el constructor del ViesModel. Invocando el command desde el constructor de tu VM significa que tu VM se hace más dificil de probar, porque siempre tienes que hacer un mock de los efectos de llamara a LoadItems, incluso si las pruebas no están relacionadas.

En su lubar, siempre llamo al command desde el contructor de la View, algo así:

    this.WhenAnyValue(x => x.ViewModel.LoadItems)
        .SelectMany(x => x.ExecuteAsync())
        .Subscribe();
        
Esto significa que una vez tengamos un nuevo ViewModel, ejecutamos LoadItems, lo cual es lo que queremos cuando la app está en marcha, pero no es lo que queremos para cada unit test.
