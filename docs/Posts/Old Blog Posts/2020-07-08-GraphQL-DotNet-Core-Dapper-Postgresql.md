---
layout: post
title: How to set up GraphQl for Dotnet core with Dapper and Postgresql
categories: [dotnet-core, code, tutorial, graphql, dapper, postgresql]
---

There are many examples of how to set up graphql for dotnet connecting to a SQL Server db through Entity Framework, but no where could
I find an example using dapper. Also all the examples and tutorials had a simple select from a single table, no mention of how to create
joins and complex queries. Also many of the examples did not cover fully how to perform dependency injection of the data acces layer, I will cover that.


I used the following libraries.

* I am using [dapper as the ORM](https://github.com/StackExchange/Dapper)
* The graphql library [graphql-dotnet](https://github.com/graphql-dotnet/graphql-dotnet)
* The UI interface [GraphiQl](https://github.com/JosephWoodward/graphiql-dotnet)


Create a GraphQL type for each table that you wish to include in the queries. You almost certainly have these setup as DTO's already.

A simple AccountCode dto.
``` csharp
namespace somenamespace.Common.Models
{
    public class AccountCode
    {
        public int Id { get; set; }

        public int Code { get; set; }

    }
}
```
I recomend you create a seperate folder for this graphql related code. The GraphQl Type for this is:
``` csharp
using GraphQL.Types;
using somenamespace.Common.Models;
using somenamespace.Db.Data;

namespace somenamespace.WebApi.Graphql
{
    public class AccountCodeType : ObjectGraphType<AccountCode>
    {
        private readonly IUnitOfWork _unitOfWork;

        public AccountCodeType(IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;

            Name = "AccountCode";
            Field(x => x.Id, type: typeof(IdGraphType)).Description("The ID of the Account Code");
            Field(x => x.Code).Description("The Account Code");

            Field<ListGraphType<AccountCodeMappingType>>(
                "mappings",
                resolve: context =>
                    _unitOfWork.AccountCodeMappingRepository.GetAccountCodeMappingByAccountCodeId(
                        context.Source.Id));
        }
    }
}
```
In this example the unitOfWork is equivalent the the entity framework context, that is the link to dapper.
The "mappings" field is how we perform the joint between the AccountCode and the AccountCodeMapping table. What is important here is the was we get the id into the query that is used in the repository, we use 
``` csharp
context.Source.Id
```
This will take the value from the Id field and use it as a parameter in the repository query GetAccountCodeMappingByAccountCodeId.

Each type has to be registered as a service in Startup.cs

``` csharp
            services.AddSingleton<AccountCodeType>();           
```

Create a top level query class to handle simple get all contents queries from tables.
``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using GraphQL.Types;
using somenamespace.Mapping.Db.Data;

namespace somenamespace.WebApi.Graphql
{
    public class OpenQuery : ObjectGraphType
    {
        private readonly IUnitOfWork _unitOfWork;
        public OpenQuery(IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;

            Field<ListGraphType<AccountCodeType>>(
                "AccountCodes",
                arguments: new QueryArguments(
                    new QueryArgument<IntGraphType> { Name = "code" }),
                resolve: context =>
                {
                    int codeArgument = context.GetArgument<int>("code");
                    var accountcodes = _unitOfWork.AccountCodeRepository.List(codeArgument);
                    return accountcodes;
                }
            );

        }
    }
}
```
This means we can select everything from the accountCode table. Also we have a way to filter on the code.
Register that in the startup.cs as well
``` csharp
            services.AddTransient<OpenQuery>();
```
Next create a Schema file that contains this query.
``` csharp
using GraphQL;
using GraphQL.Types;

namespace somenamespace.WebApi.Graphql
{
    public class MappingSchema : Schema
    {
        public MappingSchema(IDependencyResolver resolver) : base(resolver)
        {
            Query = resolver.Resolve<OpenQuery>();

        }
    }
}
```
And add that to the startup.cs 
``` csharp
            services.AddSingleton<ISchema, MappingSchema>();
```

Next we need to create the actual graphql controller under the controller folder as usual.
``` csharp
using System;
using System.Threading.Tasks;
using GraphQL;
using Microsoft.AspNetCore.Mvc;
using somenamespace.Mapping.Db.Data;
using somenamespace.Mapping.WebApi.Graphql;

namespace Uksbs.Gfs.Mapping.WebApi.Controllers
{
    [Route("graphql")]
    [ApiController]
    public class GraphqlController : ControllerBase
    {
        private readonly IUnitOfWork _unitOfWork;
        private readonly IDependencyResolver _resolver;
        public GraphqlController(IDependencyResolver resolver, IUnitOfWork unitOfWork)
        {
            _resolver = resolver;
            _unitOfWork = unitOfWork;
        }
        [HttpPost]
        public async Task<ActionResult> Post([FromBody] GraphQLQuery query)
        {

            var inputs = query.Variables.ToInputs();
            var schema = new MappingSchema(_resolver);
            var result = await new DocumentExecuter().ExecuteAsync(_ =>
            {
                _.Schema = schema;
                _.Query = query.Query;
                _.OperationName = query.OperationName;
                _.Inputs = inputs;
            });

            if (result.Errors?.Count > 0)
            {
                return BadRequest();
            }

            return Ok(result);
        }

    }
}

```
See that ***resolver*** there, guess what, add it to the startup.cs, and the schema file as well while you are there.
``` csharp
            services.AddSingleton<IDependencyResolver>(s => new FuncDependencyResolver(s.GetRequiredService));
            services.AddSingleton<ISchema, MappingSchema>();
```
Just a couple more things to register in startup and we should be ready to go. In the ***Configure*** method of startup.cs add the following:
``` csharp
            app.UseGraphiQl("/graphql");
```
I ended up adding it as the very first line, putting it further along in the process didnt work.

I think thats it, point the browser at the root and add '/graphql' and you should get a GraphIql interface that alows you to create queries.

## References
Web pages I referenced to get this done.
* [Build a GraphQL API with ASP.NET Core](https://developer.okta.com/blog/2019/04/16/graphql-api-with-aspnetcore)
 I used this to get the syntax for the graphql types. Its using EF and they get roud the injection issue by having a simple query and taking the db context that gets injected into the controller. Its too simple, but its a start.
* [The No Bullsh*t guide to GraphQL in .Net Core](https://medium.com/swlh/the-no-bullsh-t-guide-to-graphql-in-net-core-9df290be7f27)
  Now this guy realy did get me going, specificaly arounf what needed to be added to startup.cs to register the types. He uses a different UI component. This is from medium so you may not be able to read it but the [github repo is here ](https://github.com/shuri1/NoBSGraphQLDemo/tree/master/GraphQLDemo)

* [The Sneaker Shop GitHub Repo ](https://github.com/TheysKlaas/SneakerShop)
  Ignore the frontend parts. From this I realised I could have several top level queries, and I like they way these are named. So there is an "Open" query that has open access, and a "Restricted" query with restricted access. This is also where I got the iinformation on how to get that dapper UnitOfWork injected into the query using the IDependencyResolver in the OpenSchema class.
