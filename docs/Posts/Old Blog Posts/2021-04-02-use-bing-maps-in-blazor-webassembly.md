---
layout: post
title: How to add bing maps to a blazor webassembly app
categories: [code, blazor, map, webassembly]
---

Get a bing maps api key from [here](https://www.bingmapsportal.com)

Create you blazor client side app.

Locate the index.html file under wwwroot/pages
Add the following lines in the body section

``` html
<script src="script.js"></script>
<script src='https://www.bing.com/api/maps/mapcontrol?callback=GetMap&key=your-api-key-from-above' type='text/javascript'></script>
```

Add a js file at the root level in wwwroot called script.js
Add the following lines in the script file.

``` javascript
function loadBingMap() {
    var map = new Microsoft.Maps.Map(document.getElementById('map'), {});
    var pushpin = new Microsoft.Maps.Pushpin(map.getCenter(), null);
    map.entities.push(pushpin);
    return "";
}
```

Locate the page where you want the map to appear such as index.razor for example in the views folder.
Leave the @page directive as it is.

Add the following lines

``` csharp
@inject IJSRuntime JSRuntime

<h1>Display Bing Map</h1>
<div id="map" style="height:500px;width:100%;"> </div>

@code {
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync("loadBingMap", null); 
        }
    }

}
```

Build and run. 😀

