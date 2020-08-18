---
title: "Azure Service Bus bindings and triggers for Azure Functions"
date: 2020-08-18T09:31:13+10:00
draft: false
tags: ["Azure", "Service Bus", "Functions"]
summary: "How to use Azure Service Bus bindings and triggers for Azure Functions to simplify your glue code."
---
As we use more and more PAAS/serverless services on the cloud, we must find ways to integrate these services with each other to build features that our customers want.  <a target="_blank" href="https://docs.microsoft.com/en-us/azure/azure-functions/">Azure Functions</a> is one offering where we often end up fetching information from other services, or passing on stuff to those services.  In this article we will see how we can simplify integration between Azure Functions and <a target="_blank" href="https://docs.microsoft.com/en-us/azure/service-bus-messaging/">Azure Service Bus</a>.

## Output Binding and Triggers
<a target="_blank" href="https://docs.microsoft.com/en-us/azure/service-bus-messaging/">Azure Service Bus</a> is an enterprise integration message broker, and sometimes we need a service like that to decouple various parts of our solution in the cloud.  Azure Functions can interact with Service Bus in various ways:

* We can use <a href="https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-service-bus-output" target="_blank">output binding</a> to send messages from a Function App to a Service Bus.
* We can use <a href ="https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-service-bus-trigger" target="_blank">triggers</a> to respond to messages from Service Bus queue or topic.  

## Output Binding
Here is an example of how you would pass a message to a Service Bus queue without using output binding.

<pre><code>[FunctionName("MyFunctionName")]
public static async void Run([HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
{
    string queueName = Environment.GetEnvironmentVariable("QueueName");
    string serviceBusConnectionString = System.Environment.GetEnvironmentVariable("ServiceBusConnectionString");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();

    var client = new QueueClient(serviceBusConnectionString, queueName);
    var message = new Message(Encoding.UTF8.GetBytes(requestBody));

    await client.SendAsync(message);
}</code></pre>

It is a straightforward approach but has a few drawbacks.  We read the service bus connection string and queue name from environment, and create a QueueClient object to pass a message to a queue.  In doing so, we have built a dependency on a "QueueClient" object.  Our code is now more aware of the "glue" logic between our Function and Service Bus.

With output binding, we can write something like this:
<pre><code>[FunctionName("MyFunctionName")]
public static void Run([HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log,
    [ServiceBus("replyqueue", Connection = "ServiceBusConnectionString", EntityType = EntityType.Queue)] out string queueMessage)
{
    string requestBody = new StreamReader(req.Body).ReadToEnd();
    queueMessage = requestBody;
}</code></pre>

This is a more declarative way of working with Azure Functions and Service Bus.   The output variable is passed on to the queue that we have specified through "ServiceBus" attribute.  The function now has one less dependency (QueueClient) and has no awareness of how that output variable is handed over to the Service Bus.  This makes the function potentially more unit-testable.

We can also move the attributes to the function level instead of the output parameter.
<pre><code>[FunctionName("MyFunctionName")]
[return: ServiceBus("replyqueue", Connection = "ServiceBusConnectionString")]
public static string Run([HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
{
    string requestBody = new StreamReader(req.Body).ReadToEnd();
    return requestBody;
}</code></pre>
This is somewhat more readable because the function's return value is now being passed to the queue.

## Triggers

Triggers are a way to interact with a Service Bus in the opposite direction.  We can fire a Function from a Service Bus queue or topic.  We can simply supply the queue name and connection information using the "ServiceBusTrigger" attribute.

<pre><code>[FunctionName("QueueTrigger")]
public static void Run([ServiceBusTrigger("replyqueue", AccessRights.Manage, Connection = "ServiceBusConnectionSetting")]string message, TraceWriter log)
{
    // Do something with the message.
    log.Info(message");
}</code></pre>

## Conclusion
When it comes to implementing successful Azure solutions, understanding how to effectively connect Azure services with each other is a key skill.  In this post, we demonstrated how to use Azure Service Bus bindings and triggers to work with Azure Functions and Service Bus.  This is a more maintainable approach because with the use of attribues, we are handing over the responsibility of passing values between Azure services to the platform itself, and we don't have to worry about creating "QueueClient" objects and the like.


