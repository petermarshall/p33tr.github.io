---
layout: post
title: How to Refresh a ViewComponent from javascript
categories: [code, javascript, visualstudio, viewcomponent]
---

ViewComponents are nice tidy encapsulated bits of code and HTML in dotnet core mvc framework.

To insert the result of a view component into the page you do.

''' html
<div class="position-relative" id="notifications">
    @await Component.InvokeAsync("Notification")
</div>
'''

and you have a view component called NotificationViewComponent that supplys the html to go in there, as the content of the
div with the id 'notifications'.

That will run on page load.

However, what if you want the view component contents to refresh at some point, when you get a new notification to display for example.

You cannot call the viewcomponent from a post or get directly. You have to add an intermediary method in a normal controller.

''' csharp
public IActionResult ReloadNotifications()
{
    return ViewComponent("Notification");
}
'''

Write a javascript function as follows, that calls the normal controller, that then returns the contents of the view component.

''' javascript
function reloadNotifications() {
    $.post('/App/ReloadNotifications',
        function (data) {
            $("#notifications").html(data);
        });
}
'''

and then call that function when you want to reload the contents of that div.

