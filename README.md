# Description
This project shows how to host razor components in a separate class library.  

This is for a Blazor webassembly ASP.NET Core hosted app.

This solution is built in Visual Studio 2022 using .NET 6.  

# How To
- Create a new project: Blazor WebAsssembly App
  - Check ASP.NET Core hosted
- Right-click the solution, click on Add, New Project...
- Select the project type Razor Class Library
  - Do not check Support pages and views
<br>

### Razor Class Library
- Delete ExampleJsInterop.cs
- Add to _Imports.razor:<br>
**@using System.Net.Http<br>
@using System.Net.Http.Json<br>
@using System.Reflection**
- Rename Component1.razor to TestComponent.razor
  - add the routing directive:  **@page "/testpage"**
<br>

### Client
- Right-click Dependencies, Add Project Reference...
  - Select the Razor Class Library that was created
- Add to _Imports.razor:<br>
**@using System.Reflection<br>
@using [Namespace for the Razor Class Library]**<br>
eg: @using MultipleClients.Client.RazorClass
- Update App.razor:<br>
  - Add **AdditionalAssemblies** to &lt;Router...&gt;
```C#
<Router AppAssembly="@typeof(App).Assembly" AdditionalAssemblies="@LoadedAssemblies">
```
- Add the code to populate **LoadedAssemblies**
  - The typeof is a reference to a razor component in the razor class library that was created
```C#
@code {
    private List<Assembly> loadedAssemblies = new()
	  {
	      typeof(MultipleClients.Client.RazorClass.TestComponent).Assembly
    };
}
```

```
