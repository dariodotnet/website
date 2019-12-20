# An Example

```cs
public class LoginViewModel : ReactiveObject
{
    private readonly ReactiveCommand<Unit, Unit> loginCommand;
    private readonly ReactiveCommand<Unit, Unit>  resetCommand;
    private string userName;
    private string password;

    public LoginViewModel()
    {
        var canLogin = this.WhenAnyValue(
            x => x.UserName,
            x => x.Password,
            (userName, password) => !string.IsNullOrEmpty(userName) && !string.IsNullOrEmpty(password));
        this.loginCommand = ReactiveCommand.CreateFromObservable(
            this.LoginAsync,
            canLogin);

        this.resetCommand = ReactiveCommand.Create(
            () =>
            {
                this.UserName = null;
                this.Password = null;
            });
    }

    public ReactiveCommand<Unit, Unit> LoginCommand => this.loginCommand;

    // Nota que el cliente no requiere de la API completa o el genérico ReactiveCommand<TParam, TResult>,
    // podemos declarar el tipo como ReactiveCommand
    public ReactiveCommand ResetCommand => this.resetCommand;

    public string UserName
    {
        get { return this.userName; }
        set { this.RaiseAndSetIfChanged(ref this.userName, value); }
    }

    public string Password
    {
        get { return this.password; }
        set { this.RaiseAndSetIfChanged(ref this.password, value); }
    }

    // aquí simulamos un login aleatorio correcto/fallando
    private IObservable<Unit> LoginAsync() =>
        Observable
            .Return(new Random().Next(0, 2) == 1)
            .Delay(TimeSpan.FromSeconds(1))
            .Do(
                success =>
                {
                    if (!success)
                    {
                        throw new InvalidOperationException("Failed to login.");
                    }
                }
            )
            .Select(_ => Unit.Default);
}
```



