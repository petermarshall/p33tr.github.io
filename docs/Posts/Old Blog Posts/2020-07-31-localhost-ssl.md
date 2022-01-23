---
layout: post
title: ssl issues running localhost
categories: [code, dotnet, core, c#, localhost]
---

## NET::ERR_CERT_AUTHORITY_INVALID
Out of the blue I started getting this error when running localhost.


Previously I was getting warnings about the ssl certificate.
The answer for edge at least is to go to enter the following in the url

       edge://flags/#allow-insecure-localhost

and set __Allow invalid certificates for resources loaded from localhost.__ enabled.

Thanks go to [Matt Smith](https://stackoverflow.com/users/2117939/matt-smith)
