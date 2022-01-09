---
title: "An example CDK serverless project using TypeScript"
date: 2022-01-08T13:26:00+11:00
draft: false
tags: ["AWS", "CDK", "S3", "Lambda"]
summary: "An example layout for large CDK projects."
---

<a href='https://github.com/aws/aws-cdk' target='_blank'>AWS Cloud Development Kit (AWS CDK)</a> is an open source **Infrastructure-as-Code** library for AWS.  It is essentially a high-level layer that can be used through a number of modern OO languages to create AWS resources.

As a software engineer, one of the things that I always find annoying is the need to switch between various ways of writing code as you are working on different tiers of your application.  In pre .NET days (I know, I am old), we had multi-layered web applications that would use JavaScript on the front-end, a VBScript layer of classic ASP, a backend layer written in VB6 or ATL based COM components, and an RDBMS (e.g. SQL Server) with loads of SQL stored procedures.  So you were switching between various languages and between declarative and procedural ways of thinking as you are working on your application.  Today, depending upon your technology choices for your serverless applications you could face similar situation.  For example, you could have a declarative style template for your cloud resources (whether it's CloudFormation or ARM templates on Azure), then backend logic in a language that your cloud provider supports in serverless style, and then whatever you are using on the front end (which probably will include JavaScript in some incarnation + HTML/CSS).  But then you would expect a modern serverless application to have less overhead than this!  Given that, I find it absolutely magical that today on AWS you can use TypeScript for almost everything you need to do at any tier of your application, and for me CDK is one piece of the puzzle that supports that vision.  It enables a amazing return on investment on your learning of TypeScript.

## The infrastructure monolith

As an engineer you learn to break your problem down in smaller chunks, so that you can write solutions to those chunks in a way that your code reflects "single-responsibility" tendencies: one program (a class, a module or whatever) does one thing and does it well.  As we are creating more and more cloud infrastructure as code these days, it's important to find ways to organize your infrastructure code in a way that reflects that thinking.  If you don't think about this, you end up with a template that would easily grow to thousands of lines, and your eyes will bleed every time you have to look at it.  

Note that cloud providers already support features to break things down into smaller chunks.  In Azure, for example, you can have many ARM templates that can be deployed through a main template, and I see that as an acknowledgement that super-sized infrastructure templates are not fun to maintain.  

In CDK, it is convenient to define all your resources somewhere in a file that **cdk init** generates for you.  The problem is that if you keep adding stuff there, you end up with an infrastructure monolith, which is ugly.  When you look at a thousand lines of infrastructure code stuffed into one TypeScript file, you have to ask yourself: how is this any different from a huge CloudFormation template, and can we do better?  

## TypeScript to the rescue
In CDK, because you are working with a familiar high-language programming language, you can use familiar techniques to organize your infrastructure code as well.

In order to explain how we can do that using TypeScript, I have created a small CDK application.  You can find it <a href='https://github.com/salmanalibanani/cdk-lambda-s3-example' target='_blank'>here</a>.  This is a really basic application covering a scenario that you often face in real world: an API Gateway that calls a Lambad which does it's thing and needs to write the results to an S3 bucket.

## Project structure
At the top level, you will notice that we have two separate directories.  The **app** directory contains your business logic, so in the context of serverless AWS, all your Lambda code will go there.  The **infra** directory is your CDK specific code.  The **/infra/app.ts** file is the entry point of your CDK application, refereed to in the cdk.json file in the root.  The application code is unaware of any infrastructure related pieces, and having them in separate folders helps project maintainers down the line find what they are looking for.

At the CDK entry point, we have the AWS stack.  At that level you only want to create top level bits of your infrastructure.  In this example, I have only created the S3 bucket and the API Gateway, and I have done that by defining separate classes for each resource.

The API Gateway in turn creates the Lambda, and binds it to a resource in the API.  The API Gateway delegates the creation of Lambda to another class, which helps us have a separation of concerns in our code.  The class that actually creates the Lambda is responsible for not only it's creation, but also for adding any policy statements to grant access to resources that the Lambda needs to do it's job.  In this case we add a policy to let it access the S3 bucket.  

A few general ideas are at play in this design:

* A resource creates only those resources that it needs to do it's job.  There is a hierarchy of resource in our code, which will make it easy to find where each resource is created in the long run.
* Creation of each resource is encapsulated in a separate class.
* The class corresponding to each resource knows how to create that resource, how to delegate the creation of other resources it depends on, and how to grant access to those other resources.
* Any resources that will be accessed from many other parts of infrastructure can be created directly by the top level class that creates your Stack.

## Conclusion

So far, I have only applied this idea to an application that has about a dozen resources.  But for a serverless application I find this to be a lot more maintainable than having to dump creation of every Lambda in a single file.  Also, this lets me work in a way where I can decide how am I going to solve a business problem, what AWS resources do I need for it, and then create stuff as I am writing my business logic in a more agile sort of way. 

And it's <a href='https://en.wikipedia.org/wiki/Turtles_all_the_way_down' target='_blank'>TypeScript all the way down!</a>








