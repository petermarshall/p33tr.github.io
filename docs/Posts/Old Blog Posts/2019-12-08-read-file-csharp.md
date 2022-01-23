---
layout: post
title: Reading a file in C# is so easy.
categories: [c#, code]
---
# 
I have an rss feed that I want to read and process. Opening an reading this file in C# is a one line command.

Well 3 lines if you want it to be any use.

{% highlight csharp %}
string fileLocation = @"D:\export.rss";
string textFromFile = System.IO.File.ReadAllText(fileLocation);
Console.WriteLine("Contents of {0} = {1}", fileLocation, textFromFile);
{% endhighlight %}

