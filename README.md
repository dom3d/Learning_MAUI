# Learning_MAUI

MAUi is an evolution of Xamarin. Xamarin's philosophie was to use native UI as much as possible. Therefore a lot in MAUI is about cross-platform abstractions that translate or interact with native concrete implementations.

![image](https://github.com/dom3d/Learning_MAUI/assets/3112315/e0b61e04-4929-46ef-8744-f448d255cfaf)


## Program Base Architecture
``` mermaid
flowchart TD
    A[Console/Program] --> B
    B[Generic Host] --> C
    C[Application] --> D[Window]
    
```


### DotNet Generic Hosting
The ".NET Generic Host model" from "ASP.NET Core" is also used for MAUI. It provides out-of-the-box functionlity for

-
-
-


``` C#
using Microsoft.Maui;
using Microsoft.Maui.Hosting;
 
public static class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();	
        builder
            .UseMauiApp<App>() // App is the instance to run, inherted from 'Application'
            .ConfigureFonts
            (
              fonts =>
              {
                fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
                fonts.AddFont("OpenSans-Semibold.ttf", "OpenSansSemibold");
              }
            )
            .Services.AddSingleton<SomeService>() // Inversion of Control & Dependency injection for components such as pages, singletons etc.
            .Services.AddSingleton<IDataService, DataService>() // variation where for a given interface a specific implementation is used
            .Services.AddTransient<MainPage>() // UI aspects are added as well, clearly showing that "sevices" is a bad name
            .Services.AddTransient<MainPageViewModel>()
            ;
            
        #if DEBUG
          builder.Logging.AddDebug();
        #endif
        return builder.Build();          
    }
}
```

The application instance creates the first aspects of the UI. UI elements are either "Pages" or "Views".
Also able to hook into

``` C#
public partial class App : Application
{
	public App()
	{
		InitializeComponent();

		MainPage = new AppShell(); // using a shell page a root node for the UI
	}

  // optional in order to be able to hook into app lifecycle events
	protected override Window CreateWindow(IActivationState activationState)
	{
		// standard behaviour
		Window window = base.CreateWindow(activationState);

		// custom app lifecyle handlers
		window.Created += (sender, eventArgs) =>
		{
			// take actions here...
			Console.WriteLine("========= Window created =======");
		};

		window.Activated += (sender, eventArgs) =>
		{
			// take actions here...
		};
    // Stopped 
    // Resumed 
    // Destroying
    // Deactivated
    

		return window;
	}
}
```


```C#
private static MauiAppBuilder RegisterFirebase(this MauiAppBuilder builder)
    {
        builder.ConfigureLifecycleEvents(events =>
        {
#if IOS
            events.AddiOS(iOS => iOS.FinishedLaunching((app, launchOptions) => {
                Firebase.Core.App.Configure();
                return false;
            }));
#else
            events.AddAndroid(android => android.OnCreate((activity, bundle) => {
                Firebase.FirebaseApp.InitializeApp(activity);
            }));
#endif
        });

        return builder;
    }
```

Access to a service
``` C#
App.Current.Services.GetService<SecondPageViewModel>();
```

## other stuff
``` C#
private async void OnCounterClicked(object sender, EventArgs e)
{
  await AppShell.Current.GoToAsync("///SecondPage");
}
```

Handlers and Mappers
https://learn.microsoft.com/en-us/dotnet/maui/user-interface/handlers/

