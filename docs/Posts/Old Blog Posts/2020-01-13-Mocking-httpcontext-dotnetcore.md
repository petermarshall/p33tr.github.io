---
layout: post
title: Mocking httpcontext in dotnet core
categories: [dotnet-core, code, tutorial, httpcontext, unit-test, TDD]
---

Make sure the reference to httpcontext is moved into some service. That service is injected into the controller. And can
therefore be mocked.

add

``` csharp
services.AddSingleton\<IHttpContextAccessor, HttpContextAccessor>();
```

to the startup.cs ConfigureServices method.
then

``` csharp
public class UserService : IUserService
{
private readonly IHttpContextAccessor _httpContextAccessor;

public UserService(IHttpContextAccessor httpContextAccessor)
{
_httpContextAccessor = httpContextAccessor;
}

public bool IsUserLoggedIn()
{
var context = _httpContextAccessor.HttpContext;
return context.User.Identities.Any(x => x.IsAuthenticated);
}
}
```
