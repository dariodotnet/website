Order: 10
---

Una parte importante para entender ReactiveUI es entender la Programación Reactiva. ReactiveUI es una librería construida con `Reactive Extensions for .NET`. Es un conjunto de métodos de extensión para permitir una programación reactiva. Aquí tienes unos pasos para comenzar:
- Crea una app de consola net45, instala Akavache, utilizar getandfetchlatest, pasa una Task para grabar contenido (por ejemplo http://swapi.co/ para probar), subscríbete y observa como los valores asíncronos son retornados. Al contratio que las tasks, los observables representan uno o más valores a lo largo del tiempo de la suscripción
- El siguiente paso es moverte a la programación reactiva disposables/subscriptions
- Entonces mira whenactivated (reactive UI) y haz dispose de tus subscripciones (programación reactiva)
- Ahora crea un ViewModel (reactiveobject) que replica lo que has aprendido desde la aplicación de consola y conéctalo al datacontext de tu aplicación Xamarin Forms. Haz esto mismo (por ahora) como lo harías normalmente.

Ahora conoces ReactiveUI, pero todavía no de un modo eficiente explresado por ti mismo y de modo reactivo y limpio.
El siguiente paso es [OAPH](https://reactiveui.net/docs/handbook/oaph/), [WhenAny](https://reactiveui.net/docs/handbook/when-any/) y [Reactive Command](https://reactiveui.net/docs/handbook/commands/). Esto es opcional, pero verás el inmenso poder de éste.

# Ejemplo de ViewModel

Vamos a crear una aplicación simple demostrando un número de funcionalidades de ReactiveUI, sin entrar en mucho detalle.

Crearemos una aplicación WPF, la cual nos permitirá buscar sobre las imágenes públicas de Flickr.
El código completo de la aplicación se muestra al final de este copítulo y mostrará algunas pinceladas relevantes.

En Visual Studio, crea una nueva aplicación WPF (.Net 4.5 o superior)

Nuestra vista ha sido creada para nosotros, la `MainWindow`, procedamos a crear nuestro ViewModel.

**Añade referencias**
```csharp
using System.Web;
```

**Añade NuGet Packages**
```
Install-Package ReactiveUI
```

**Añade una nueva propiedad**
```csharp
public AppViewModel ViewModel { get; private set;} 
```

**Ahora asignale un nuevo valor dentro del constructor de MainWindow y haz bind al DataContext**

```csharp
public MainWindow()
{
    ViewModel = new AppViewModel();
    InitializeComponent();
    DataContext = ViewModel;
}  
```

**Crea una nueva class "AppViewModel"**
```csharp
// AppViewModel es donde describiremos la interacción de nuestra aplicación
//(podemos describir en una única class cuando nuestra aplicación es muy pequeña)
public class AppViewModel : ReactiveObject
{
    // En ReactiveUI, esta es la sintáxis para declarar una propiedade de lectura-escritura
    // que notificará a los Observers (también como WPF) que la propiedad
    // ha cambiado. Si declaramos ésta como una propiedad normal, no podremos
    // saber cuando ésta cambie!
    string _SearchTerm;
    public string SearchTerm
    {
        get { return _SearchTerm; }
        set { this.RaiseAndSetIfChanged(ref _SearchTerm, value); }
    }

    // Describiremos esto después, pero ReactiveCommand es un Command
    //(como "Open", "Copy", "Delete", etc), que maneja una task corriendo
    // en background
    public ReactiveCommand<string, List<FlickrPhoto>> ExecuteSearch { get; protected set; }

    /*ObservableAsPropertyHelper
     *
     * Aquí tenemos algo interesante: En ReactiveUI podemos tomar IObservables
     * y sus modificaciones para asignarlos a una Propiedad - cada vez que el Observable
     * tome un nuevo valor, notificaremos que a nuestro ReactiveObject que la 
     * propiedad ha cambiado
     *
     * Para hacerlo, necesitamos una clase llamada ObservableAsPropertyHelper -
     * esta clase se subscribe a un Observable y almacena una copia de los últimos
     * valores. Esto también lanza una acción cada vez que la propiedad cambia,
     * usualmente llamando a ReactiveObject's RasiePropertyChanged.
     */
    ObservableAsPropertyHelper<List<FlickrPhoto>> _SearchResults;
    public List<FlickrPhoto> SearchResults => _SearchResults.Value;

    // Aquí, queremos crear una propiedad que represente cuando la aplicación
    // está realizando una búsqueda (i.e cuando mostrar el control "spinner"
    // que permite al usuario saber que la aplicación está ocupada). También 
    // declaramos esta propiedad para ser el resultado de un Observable (i.e 
    // es el valor derivado desde alguna otra propiedad)
    ObservableAsPropertyHelper<Visibility> _SpinnerVisibility;
    public Visibility SpinnerVisibility => _SpinnerVisibility.Value;

    public AppViewModel()
    {
        ExecuteSearch = ReactiveCommand.CreateFromTask<string, List<FlickrPhoto>>(
            searchTerm => GetSearchResultsFromFlickr(searchTerm)
        );

        /* Creando nuestra UI de modo declarativo
         *
         * Las propiedades en este ViewModel están relacionadas unas con otras de 
         * un modo diferente a otros frameworks, es dificil describir cada relación
         * sencillamente; el código para implementar "Controlar que nuestro "spinner" 
         * está activo mientras la búsqueda se realiza" normalmente nos lleva a tener
         * que manejar un montón de eventos
         *
         * Sin embargo, con RxUI, podemos describir como las propiedades están relacionadas
         * de un modo mas organizado y limpio. Nos permite describir cómo el usuario las
         * está utilizando en nuetra aplicación, en el orden que lo hacen.
         */

        // Vamos a tomar una propiedad y convertirla en Observable aquí - este
        // Observable devolverá un valor cada vez que Search term cambie (la cual 
        // está en XAML, conectara al TextBox). 
        //
        // Vamos a utilizar el operador Throttle para ignorar cambios que 
        // ocurren demasiado rápido, ya que no queremos problemas por buscar
        // con cada tecla presionada! Entonces recuperamos el Value del cambio, 
        // filtramos los cambios que son idénticos y también cuando string está vacío.
        //
        // Finalmente, utilizamos el operador InvokeCommand de RxUI's, el cual toma
        // el String y llama al método Execute del Command ExecuteSearch, siempre tras
        // comprobar que Command puede ser ejecutado llamando a CanExecute.
        this.WhenAnyValue(x => x.SearchTerm)
            .Throttle(TimeSpan.FromMilliseconds(800), RxApp.MainThreadScheduler)
            .Select(x => x?.Trim())
            .DistinctUntilChanged()
            .Where(x => !String.IsNullOrWhiteSpace(x))
            .InvokeCommand(ExecuteSearch);

        // Cómo describiríamos cuando mostrar nuestro "spinner" en Español? 
        // Diríamos algo así como "La visibilidad del spinner es cuando la
        // la búsqueda se está ejecutando". RxUI nos permite escribir algo así
        // en nuestro código
        //
        // ExecuteSearch tiene un IObservable<bool> que se llama IsExecuting y 
        // es lanzado cada vez que el Command cambia su estado de ejecución. Ahora
        // con el operador Select() lo convertimos al tipo Visibility y entonces 
        // podemos utilizar el operador ToProperty de RxUI, el cual nos ayuda a crear
        // un objeto de tipo ObservableAsPropertyHelper.

        _SpinnerVisibility = ExecuteSearch.IsExecuting
            .Select(x => x ? Visibility.Visible : Visibility.Collapsed)                
            .ToProperty(this, x => x.SpinnerVisibility, Visibility.Hidden);
        
        // Nos subscribimos a la propiedad "ThrownExceptions" de nuestro 
        // ReacitveCommand, que es donde ReactiveUI maneja cualquier excepción que es
        // lanzada desde "GetSearchResultsFromFlickr". Revisa la sección "Gestión de errores""
        // para más información acerca de esto.
        ExecuteSearch.ThrownExceptions.Subscribe(ex => {/* Handle errors here */});

        // Aquí, vamos a describir qué pasa cuando el Command es invocado - 
        // vamos a lanzar el GetSearchResultsFromFlickr cada vez que Command 
        // es ejecutado.
        // 
        // La parte importante aquí es el valor devuelto - un Observable. Vamos a
        // terminar aquí con un Stream de FlickrPhoto como una Lista: cada vez que 
        // llamemos a Execute, finalizaremos con una nueva lista, la cual pondremos
        // inmediatamente en nuestra propiedad SearchResults, que disparará 
        // automáticamente INotifyPropertyChanged.
        _SearchResults = ExecuteSearch.ToProperty(this, x => x.SearchResults, new List<FlickrPhoto>());
    }

    public static async Task<List<FlickrPhoto>> GetSearchResultsFromFlickr(string searchTerm)
    {
        var doc = await Task.Run(() => XDocument.Load(String.Format(CultureInfo.InvariantCulture,
            "http://api.flickr.com/services/feeds/photos_public.gne?tags={0}&format=rss_200",
            HttpUtility.UrlEncode(searchTerm))));

        if (doc.Root == null)
            return null;

        var titles = doc.Root.Descendants("{http://search.yahoo.com/mrss/}title")
            .Select(x => x.Value);

        var tagRegex = new Regex("<[^>]+>", RegexOptions.IgnoreCase);
        var descriptions = doc.Root.Descendants("{http://search.yahoo.com/mrss/}description")
            .Select(x => tagRegex.Replace(HttpUtility.HtmlDecode(x.Value), ""));

        var items = titles.Zip(descriptions,
            (t, d) => new FlickrPhoto { Title = t, Description = d }).ToArray();

        var urls = doc.Root.Descendants("{http://search.yahoo.com/mrss/}thumbnail")
            .Select(x => x.Attributes("url").First().Value);

        var ret = items.Zip(urls, (item, url) => { item.Url = url; return item; }).ToList();
        return ret;
    }
}
```
El objetivo de la sintáxis para propiedades lectura-escritura de ReactiveUI es notificar a los Observers que una propiedad ha cambiado.
De otro modo, no estaríamos preparados para saber cuando han cambiado.

ExecuteSearch es básicamente una task asíncrona ejecutada en background.

En casos donde no necesitamos proveer un binding two-way entre la nuestra View y el ViewModel, podemos utilizar una mas Helpers de ReactiveUI para notificar a los Observers de los cambios en propiedades de lectura de nuestro ViewModel. Utilizamos 
ObservableAsPropertyHelper dos veces, una para generar una List<T> como una collección observable read-only, para los cambios en la
visibilidad de nuestro indicador que muestra que estamos ejecutando actualmente nuestra búsqueda.

Esto también funciona en la dirección opuesta, cuando convertimos nuestra propiedad `SearchTerm` en observable. Esto significa
que estamos notificando cada cambio que ocurre en nuestra UI. Utilizando Reactive Extensions, tras esperara los eventos y asegurarnos
de que la búsqueda no ocurre antes de 800ms desde la última pulsación. Y si en este punto el usuario no hizo cambios en el último valor, o si la búsqueda está en blanco, ingnoramos completamente los eventos.

Utilizando el observable `IsExecuting`de `ReactiveCommand`, derivamos un nuevo observable que cambia la visibilidad para nuestro 
"indicador de proceso".

El método `GetSearchResultsFromFlickr` se invoca cada vez que hay un cambio filtrado en nuetra UI, 
definiendo qué debería ocurrir cuando un usuario ejecuta una nueva búsqueda.

Crea una class sencilla para usar como modelo de los resultados de Flickr - ya que nunca actualizaremos las propiedades una vez
creado el objeto, no tenemos que utilizar ReactiveObject.

```csharp
namespace FlickrBrowser
{
    public class FlickrPhoto
    {
        public string Title { get; set; }
        public string Description { get; set; }
        public string Url { get; set; }
    }
}
```

**Nuestro ViewModel ahora está completo**

Ahora necesitamos crear una View para nuestro ViewModel, lo siguiente es un ejemplo

```xml
<Window x:Class="FlickrBrowser.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Name="Window" Height="350" Width="525">
    <Window.Resources>
        <DataTemplate x:Key="PhotoDataTemplate">
            <Grid MaxHeight="100">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Image Source="{Binding Url, IsAsync=True}" Margin="6" MaxWidth="128"
                       HorizontalAlignment="Center" VerticalAlignment="Center" />

                <StackPanel Grid.Column="1" Margin="6">
                    <TextBlock FontSize="14" FontWeight="Bold" Text="{Binding Title}" />
                    <TextBlock FontStyle="Italic" Text="{Binding Description}" 
                               TextWrapping="WrapWithOverflow" Margin="6" />
                </StackPanel>
            </Grid>
        </DataTemplate>
    </Window.Resources>

    <Grid Margin="12">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <TextBlock FontSize="16" FontWeight="Bold" VerticalAlignment="Center">Search For:</TextBlock>
        <TextBox Grid.Column="1" Margin="6,0,0,0" Text="{Binding SearchTerm, UpdateSourceTrigger=PropertyChanged}"/>
        <TextBlock Grid.Column="2" Margin="6,0,0,0" FontSize="16" FontWeight="Bold" Text="..." Visibility="{Binding SpinnerVisibility}" />

        <ListBox Grid.ColumnSpan="3" Grid.Row="1" Margin="0,6,0,0" 
                 ScrollViewer.HorizontalScrollBarVisibility="Disabled"
                 ItemsSource="{Binding SearchResults}" ItemTemplate="{DynamicResource PhotoDataTemplate}"  />
    </Grid>
</Window>
```   


