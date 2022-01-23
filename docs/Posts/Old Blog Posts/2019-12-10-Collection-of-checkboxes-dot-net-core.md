---
layout: post
title: Nested checkboxes problem for asp.net mvc
categories: [c#, code]
---
**Nested checkboxes problem for asp.net mvc**

I have a viewmodel that has a collection of items, some of those items are bool values that I want to set with checkboxes in the UI. On submit I want to process the checkbox values server side.

First create a .Net Core web application.

For this explanation I have created two classes Parent and child. Parent has many children.

{% highlight csharp %}

    public class Parent
    {
        public Parent()
        {
            Children = new List<Child>();
        }
        public string Name { get; set; }
        public List<Child> Children { get; set; }
    }

    public class Child
    {
        public Child() { }
        public string Thing { get; set; }
        public bool ThisIsSet { get; set; }
    }
{% endhighlight %}

You can see that the Child class has a property called ThisIsSet that is a boolean.

The HomeController Index Method instantiates a Parent class, populates it with test data and passes that ViewModel down to the UI.

{% highlight csharp %}

        public IActionResult Index()
        {
            Parent parent = new Parent();
            Child A = new Child() { Thing = "A", ThisIsSet = false };
            Child B = new Child() { Thing = "B", ThisIsSet = false };
            parent.Children.Add(A);
            parent.Children.Add(B);
            return View(parent);
        }
{% endhighlight %}

The View, Index.cshtml under the Home directory received this ViewModel.

```
@model WebApplication1.Controllers.Parent

@{
    ViewData["Title"] = "Index";
}

<h1>Index</h1>

<h4>Parent</h4>
<hr />
<div class="row">
    <div class="col-md-4">
        <form asp-action="Create">
            <div asp-validation-summary="ModelOnly" class="text-danger"></div>
            <div class="form-group">
                <label asp-for="Name" class="control-label"></label>
                <input asp-for="Name" class="form-control" />
                <span asp-validation-for="Name" class="text-danger"></span>
            </div>
            @for (int i = 0; i < Model.Children.Count; i++)
            {
                <label asp-for="@Model.Children[i].Thing" class="control-label"></label>
                <input asp-for="@Model.Children[i].Thing" class="form-control" />
                <label asp-for="@Model.Children[i].ThisIsSet" class="control-label"></label>
                <input asp-for="@Model.Children[i].ThisIsSet" class="form-control" />
            }
            <div class="form-group">
                <input type="submit" value="Create" class="btn btn-primary" />
            </div>
        </form>
    </div>
</div>
```
There are some special things happening in this view that we do not normally see. The Children collection is processed using a for loop and a counter, rather than using a foreach loop. This is so we can use the counter to reference the items in the Chilren collection.

We also have to use the asp-for notation so that the values are populated in the form values that are sent back to the server.

For values that dont need to be displayed you can add the attribute type=hidden.

This form will POST back to a Create method on the HomeController.

{% highlight csharp %}

        public IActionResult Create(IFormCollection formValues)
        {
            Parent p = ToParent(formValues);
            StringBuilder sb = new StringBuilder();
            sb.Append("start ");
            foreach(var c in p.Children)
            {
                sb.Append($"Thing is {c.Thing} and {c.ThisIsSet}  ");
            }
            return RedirectToAction("Index2", new { message = sb.ToString() });
        }
{% endhighlight %}

This methods has a function ToParent that takes an IFormCollection and generates a Parent from it. The Parent class doesn't HAVE to be the output type but in this instance it helps us to understand what is going on. Not that we have to use an IFormCollection not a FormCollection.

{% highlight csharp %}

        private Parent ToParent(IFormCollection formCollection)
        {
            var model = new Parent();
            model.Name = formCollection["name"];
            List<Child> itemList = new List<Child>();
            int i = 0;
            while (!string.IsNullOrEmpty(formCollection["Children[" + i + "].Thing"]))
            {
                Child c = new Child();
              
                c.Thing = formCollection["Children[" + i + "].Thing"];
                var x = formCollection["Children[" + i + "].ThisIsSet"];
                c.ThisIsSet = Convert.ToBoolean(x[0]);
                itemList.Add(c);
                i++;
            }
            model.Children = itemList;
            return model;
        }
{% endhighlight %}

Processing this IFormCollection is just horrible.
If you inspect it in the debugger its a list of key value pairs which would work well for a flat simple object. But we have a collection. And there is this awful way of getting to the values in the collection.

And the big suprise is that the checkbox does not come back as a {true}, {false} value. But a selected checkbox comes back as {true, false} and an unchecked box comes back as {false}, so the first value in that object represents the status, that's why we have to pick the first element from the array x.

The rest of the Create method is just for debugging, it creates a message that is then displayed.

___
