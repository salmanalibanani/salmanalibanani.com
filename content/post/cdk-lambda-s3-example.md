---
title: "An example CDK serverless project using TypeScript"
date: 2022-01-08T13:26:00+11:00
draft: false
tags: ["AWS", "CDK", "S3", "Lambda"]
summary: "An example layout for large CDK projects."
----

<a href='https://github.com/aws/aws-cdk' target='_blank'>AWS Cloud Development Kit (AWS CDK)</a> is an open source **Infrastructure-as-Code** library for AWS.  It is essentially a high-level layer that can be used through a number of modern OO languages.


I have worked with Infrastructure-as-Code solutions in a few projects, and recently had an opportunity to gain experience with AWS Cloud Development Kit (CDK).  
Having worked with various Infrastructure-as-Code solutions in recent months, the first thing that you notice with a framework like CDK is this: lesser friction.

Let me explain.

When you are working with something like CloudFormation templates (or ARM on Azure), you find yourself switching between your application code (in C#, TypeScript or whatever) and your markup-based infrastructure templates.  This is probably not a big deal and most developers will consider switching between declarative and imperative code as a fact of life, specially if you have experience with front-end development.  But there comes a point where you start asking yourself some serious existential questions:

* Why do I have to keep switching between declarative infrastcuture markups and my imperative business logic?  Is there a better way to build back-end infrastructure?
  
* Why do I have to deal with thousands of lines of template code?  Sure it's generally easy to understand them once you are familiar with them.  But that doesn't mean your eyes don't bleed every time you look at that ugliness.  There has to be something better that fits well with our shortening attention span!

**AWS Cloud Development Kit (CDK)** solves these problems for us.  And it is a pleasure to work with, but only if you are smart about how you layout your CDK based projects.  Infrastructure monoliths are not fun to deal with, no matter how they are written.

## The sample project

In order to explain the ideas in this post better, I have created a small CDK application.  You can find it here.

At the top level, you will notice that we have two separate directories.  The **app** directory contains your business logic, so in the context of serverless AWS, all your Lambda code will go there.  The **infra** directory is your CDK specific code.  The xyz file is the entry point of your CDK application, reffered to in the cdk.json file in the root.








