---
title: "Azure Service Bus client library for .NET"
date: 2020-09-04T15:25:13+10:00
draft: false
tags: ["Azure", "Service Bus"]
summary: "Get started with Azure Service Bus client library for .NET"
---
<a target="_blank" href="https://docs.microsoft.com/en-us/azure/service-bus-messaging/">Azure Service Bus</a> is an enterprise integration message broker.  We need services like these to decouple various parts of our solutions.  Over the last few months, I have been playing with a number of services that Microsoft groups together as <a href="https://azure.microsoft.com/en-us/resources/azure-integration-services/" target="_blank">Azure Integration Services</a>.  This time around I decided to look into ways to interact with these services using the SDKs provided by Microsoft.  In this post, I'll show how easy it is to integrate your .NET apps with Azure Service Bus.

Currently there are at least two Service Bus SDKs available as NuGet packages:

* <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/" _target="blank">Microsoft.Azure.ServiceBus</a> which seems to have been released in 2017 and is at version 4.1.3 at the time of this writing.

* <a href="https://www.nuget.org/packages/Azure.Messaging.ServiceBus/" _target="blank">Azure.Messaging.ServiceBus</a> which looks like a much recent project kicked off in 2020 and is more active on <a href="https://github.com/Azure/azure-sdk-for-net/tree/Azure.Messaging.ServiceBus_7.0.0-preview.6/sdk/servicebus" _target="blank">GitHub</a>.  At the time of this writing it is still in preview.

For my first foray into world of Azure SDKs I decided to use the older version of the Service Bus SDK.  I document the steps below, and you will need an Azure subscription with a Service Bus Namespace and preferably a few Queues and Topics if you want to follow along.

## Setting up the project

Start with the following on console.  (I am using .NET Core 3.1.301.)
```xml
dotnet newconsole -n AzureServiceBusSDKSample
cd AzureServiceBusSDKSample
dotnet add package Microsoft.Azure.ServiceBus --version 4.1.3
dotnet add package Microsoft.Extensions.Configuration
dotnet add package Microsoft.Extensions.Configuration.FileExtensions
dotnet add package Microsoft.Extensions.Configuration.Json
```
You will also need the following in your .csproj file:
```xml
<ItemGroup>
    <None Update="appsettings.json">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
</ItemGroup>
```
This sets up the project nicely with references to Configuration API which we will use to store the connection string for Service Bus.  You can get the connection string from Azure portal.

Note that in real projects, connection strings should be not be kept in config files.  A great place to keep connection strings is <a href="https://docs.microsoft.com/en-us/azure/key-vault/general/" target="_blank">Azure Key Vault</a>.

## Calling Management Client APIs
The SDK provides a MamagementClient object which can be used to read and update properties of various objects associated with Service Bus.  We need to supply connection string to create a Management Object.
```C#
IConfiguration config = new ConfigurationBuilder()
    .AddJsonFile("appsettings.json", true, true)
    .Build();
var connectionString = config["connectionString"];
var client = new ManagementClient(connectionString); 
```
With the ManagementClient object, we can call various methods to extract object information from Service Bus, e.g.

```C#
var queues = client.GetQueuesAsync().Result;
var topics = client.GetTopicsAsync().Result;
```
We can extract various properties from queues and topics collections.
```C#
foreach (var queue in queues)
{
    Console.WriteLine("Queue name: " + queue.Path);
    Console.WriteLine("Queue size in MB: " + queue.MaxSizeInMB);
    ...
}
```
The ManagementClient object also allows us to update parameters of queues, topics etc.
```C#
var queueDescription = client.GetQueueAsync("queuename").Result;
queueDescription.MaxDeliveryCount = 50;
client.UpdateQueueAsync(queueDescription);
```
## Sending message to a Queue or Topic
We will use QueueClient, TopicClient and other objects to pass messages to individual objects.

```C#
var queueClient = new QueueClient(connectionString, "replyqueue");  //second parameter is queue name.
queueClient.SendAsync(new Message() { Body = Encoding.ASCII.GetBytes("Hello world")});

var topicClient = new TopicClient(connectionString, "new-topic");  //second parameter is topic name
topicClient.SendAsync(new Message() {Body = Encoding.ASCII.GetBytes("Some message")});
```
In the case of topic client, the message sent to the topic will be received by all subscriptions to that topic.  Therefore, to test this in Azure portal, you need to create subscriptions against the topic you are using.

## Conclusion
The Service Bus SDK can be used to perform administrative tasks on Service Bus using the ManagementClient class.  To work with individual objects (e.g. passing messages to queues etc), we need to use the respect client objects (QueueClient, TopicClient) as shown above.

In this post we have only scratched the surface of capabilities of the SDK.  Depending upon the requrirements of your application, you can explore the APIs to use the features you need.  



