# Description
This project shows how to host razor components in a separate class library.  This allows for the Blazor Client to be split up into multiple modules.

I find this to be useful for vertical slice architecture as it allows isolating client features in their own module instead of just their own directory.

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
  - Add the code to populate **LoadedAssemblies**.  The reference is to a razor component in the razor class library that was created
```C#
<Router AppAssembly="@typeof(App).Assembly" AdditionalAssemblies="@LoadedAssemblies">
```

```C#
@code {
    private List<Assembly> loadedAssemblies = new()
	  {
	      typeof(MultipleClients.Client.RazorClass.TestComponent).Assembly
    };
}
```

- Updated Shared/NavMenu.razor
  - Add a link to the new page
```C#
<div class="nav-item px-3">
    <NavLink class="nav-link" href="testpage">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Test Page
    </NavLink>
</div>

```

# Releasing
Releasing a separate client is not as easy as just releasing the dll for that project.  If this is done, the web app will not update with the changes for that dll.

Through testing, I found that I also needed to release the main client.  I guess this is because Blazor WebAssembly won't reload child UI's unless it reloads the parent UI.  And it won't reload the parent UI unless a new version is released.

So from a command line I would run:
```DOS
C:\Client.RazorPage\dotnet publish -c release -o "C:\Path\To\Release\Dir"
C:\Cleint\dotnet publish -c release -o "C:\Path\To\Release\Dir"
```

I would then copy the dll's published to our web server.

I did not find an easy way to publish via Visual Studio.  It's possible, but would require flagging each project as "Publish" vs "Do Not Publish", which would be a lot of work each time.  Doing it via command line (or automating it) is much easier
