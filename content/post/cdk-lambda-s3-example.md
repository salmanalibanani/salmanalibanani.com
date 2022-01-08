---
title: "CDK init - where do we go from here"
date: 2022-01-08T13:26:00+11:00
draft: true
tags: ["AWS", "CDK", "S3", "Lambda"]
summary: "An example layout for large CDK projects."
---

Having worked with various Infrastructure-as-Code solutions in recent months, the first thing that you notice with a framework like CDK is this: lesser friction.

Let me explain.

When you are working with something like CloudFormation templates (or ARM on Azure), you find yourself switching between your application code (in C#, TypeScript or whatever) and your markup-based infrastructure templates.  This is probably not a big deal and most developers will consider switching between declarative and imperative code as a fact of life, specially if you have experience with front-end development.  But there comes a point where you start asking yourself some serious existential questions:

* Why do I have to keep switching between declarative infrastcuture markups and my imperative business logic?  Is there a better way to build back-end infrastructure?
  
* Why do I have to deal with thousands of lines of template code?  Sure it's generally easy to understand them once you are familiar with them.  But that doesn't mean my eyes don't bleed every time I look at that ugliness.  There has to be something better that fits well with my shortening attention span!

CDK solves these problems for us.  And it is a pleasure to work with, but only if you are smart about how you layout your CDK based projects.  Infrastructure monoliths are not fun to deal with, no matter how they are written.








