Title: .NET Standard
Order: 10
---


# .NET Standard Library

A partir de la versión 8.0 de ReactiveUI, tienes soporte para [.NET Standard](https://docs.microsoft.com/en-us/dotnet/standard/net-standard). Para versiones anteriores a 8.0, puedes seguir utilizando RxUI nuget package (para otros proyectos basados en el popular paquete PCL) con una pequeña modificación.

## Proyectos utilizando Project.json:

Localiza el archivo project.json en tu proyecto netstandard y busca los siguiente:

```json
"frameworks": {
    "netstandard1.4": {}
}
```

y añade la sección `imports`:

```json
"frameworks": {
    "netstandard1.4": {
      "imports": "portable-net45+win8+wp8"
    }
  }
```

Para comprender en datalle qué hace `imports`, lee el siguiente [detallado post](https://msdn.microsoft.com/en-us/library/system.windows.input.icommand.aspx).

## Proyectos utilizando PackageReference:

Edita tu archivo de proyecto .NET Standard y localiza lo siguiente:

```xml
<PropertyGroup>
	<TargetFramework>netstandard1.4</TargetFramework>
</PropertyGroup>
```

cámbialo por lo siguiente:

```xml
<PropertyGroup>
	<TargetFramework>netstandard1.4</TargetFramework>
	<PackageTargetFallback>portable-net45+win8+wpa81+wp8</PackageTargetFallback>
</PropertyGroup>
```
