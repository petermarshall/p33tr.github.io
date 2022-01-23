---
layout: post
title: How to debug javascript in Visual Studio
categories: [code, debug, javascript, visualstudio]
---

Install the nuget package https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/

Add
... csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
'''
To the Startup.cs Configure method.

And it still doesnt work.

Forgett all the above.
Add 'debugger' to the script. 