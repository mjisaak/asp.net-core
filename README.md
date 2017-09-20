# asp.net-core

## Unhandled exception filter with Application Insights

Using ASP.NET Core its easy to prevent unhandled exceptions to be exposed to the user. Together with *Application Insights* you can write an exception filter that *captures* and *tracks* every unhandled exception:

```csharp
public class ExceptionFilter : IExceptionFilter
{
    private readonly TelemetryClient _ai = new TelemetryClient();

    public void OnException(ExceptionContext context)
    {
        var exceptionTelemetry = new ExceptionTelemetry(context.Exception);

        _ai.TrackException(exceptionTelemetry);

        context.ExceptionHandled = true;
        context.Result = new ContentResult
        {
            StatusCode = 500,
            Content = $"This should not happen. Contact your administrator. Operation Id: {exceptionTelemetry.Context.Operation.Id}"
        };
    }
}
```

To enable the exception filter all you have to do is to add it withint he ```public void ConfigureServices(IServiceCollection services)``` method:
```csharp
services.AddMvc(options =>
{
    options.Filters.Add(new ExceptionFilter());
})
```