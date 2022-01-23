---
layout: post
title: How to Create, deploy and trigger a dotnet core webjob
categories: [dotnet, core, webjob, azure]
---


Webjobs run in the context of a running app service, the easiest way to get goin is to add a 
console application to an existing solution that deploys to an app service.

In that solution add new console application project.

Change the defualt program as follows

'''csharp
        static async Task Main()
        {
            var builder = new HostBuilder();

            builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
                b.AddTimers();

            });
            builder.ConfigureLogging((context, b) =>
            {
                b.AddConsole();
            });
            builder.ConfigureHostConfiguration(Configure);
            builder.ConfigureAppConfiguration(Configure);


            builder.UseEnvironment(EnvironmentName.Development);


            var host = builder.Build();
            using (host)
            {
                
                await host.RunAsync();
            }
        }

        private static void Configure(IConfigurationBuilder builder)
        {
            builder.SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json",false,true);

        }

'''
The AddTimers() is needed if you want the functions to run as a cron job.


The AzureWebJobsDashboard and AzureWebJobsStorage values storred in the appsettings file are documented elsewhere on the internet.

Create a class called Functions.cs

'''csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace xxxRefresh
{
    public  class Functions
    {
        private IConfiguration _configuration;
        public Functions(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        /// <summary>
        /// Runs whenever a message is dropped in the somequeue
        /// To perform a FULL PEP refresh use "FULL" as the message.
        /// </summary>
        /// <returns></returns>
        public  async Task ProcessQueueMessageAsync([QueueTrigger("somequeue")] string message, ILogger logger)
        {
            await PerformAction(message, logger);
        }

        /// <summary>
        /// "1 0 * * *" One minute past midnight every day
        /// USE "* * * * *" to run every minute - all the time
        /// </summary>
        /// <returns></returns>
        public async Task ProcessFullPepRefreshOnScheduleAsync([TimerTrigger("1 0 * * *")] TimerInfo timer, ILogger logger)
        {
            await PerformAction("FULL", logger);
        }

        private // write your own perform action method .....
        }
    }
}

'''
The functions will be detected when the console app in run.




