Order: 15
Title: Model, View, ViewModel
---


# Eventos de cambio en propiedades de viewmodels anidados 

this.WhenAny hace que sea estúpidamente sencillo manejar los eventos de cambio para instancias de viewmodel anidadas..

i.e. 

```
// var canExecute = this.WhenAny(vm => !vm.IsBusy, vm => !vm.Child.IsSearching)

public ParentViewModel()
{
    public bool IsBusy {get;set;}
    public ChildViewModel Child {get;set;}
}

public class ChildViewModel()
{
    public bool IsSearching {get;set;}
}
```
