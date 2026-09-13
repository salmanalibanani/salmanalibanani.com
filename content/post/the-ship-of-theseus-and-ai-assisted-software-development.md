---
title: "The Ship of Theseus and AI-Assisted Software Development"
date: 2026-09-02T09:00:00+10:00
draft: false
summary: "The tools used for AI-assisted development may be new, but the principles that make them effective are not."
tags: ["ai", "engineering", "process", "philosophy"]
---

There is an ancient philosophical thought experiment called the Ship of Theseus. The story is about a ship that was preserved over a long period of time by replacing its wooden planks as they decayed. One plank was replaced, then another, and the process continued until eventually none of the original planks remained. This created a problem for philosophers, who apparently had enough spare time to worry about such things: if every part of the ship has been replaced, is it still the same ship?

There are various versions of the thought experiment and considerably more philosophical discussion around it than I am going to attempt here. But as a software developer, I find the whole thing interesting for a different reason. Forget the question of whether it is still the same ship. The more interesting thing is that the ship remained a ship while they were changing it.

This is actually a pretty good way to develop software.

## Replace one plank at a time

Most developers who have worked on reasonably large systems have experienced the temptation of a rewrite. You inherit a system that has accumulated ten years of design decisions, obsolete libraries, strange abstractions and business rules whose origins nobody remembers. At some point somebody looks at the whole thing and says that perhaps it would be easier to rewrite it.

Sometimes that is the right decision. More often, I think there is another option which is less exciting but considerably safer. Replace one part of the system, make sure everything still works, and then replace another.

This idea appears in software engineering in many different forms. Incremental development, evolutionary architecture, continuous delivery and the strangler pattern all have elements of the same philosophy. Instead of disappearing for six months and emerging with the glorious new version of the system, you move from one working state to another working state through relatively small changes.

There is nothing particularly new about this. In fact, that is the point of this post.

I have been using AI coding tools quite heavily, and one thing I am increasingly noticing is that many of the skills required to use AI effectively are not really new AI skills at all. They are good software engineering skills that have suddenly become more important because AI allows us to produce bad decisions at an unprecedented speed.

Small incremental development is probably the best example.

## AI can replace a lot of planks

One of the impressive things about modern coding agents is how much you can ask them to do. You can describe a fairly substantial feature, give the agent access to the repository, and watch it disappear into the codebase. A little while later it comes back having changed thirty files, added tests, modified the database, updated some configuration and possibly refactored a few unrelated things it found offensive along the way.

This is impressive, but I am not convinced that this is always the best way to use these tools.

The problem is that the cost of generating a large change has fallen dramatically, while the cost of understanding a large change has not fallen by anything like the same amount. AI can generate two thousand lines of code remarkably quickly. Somebody still needs to establish whether those two thousand lines represent the right change.

This becomes even more interesting because AI-generated code can look very convincing. The code may be clean, the tests may pass and the explanation in the pull request may sound entirely reasonable. But somewhere near the beginning of the process the agent may have misunderstood a business rule, made an incorrect assumption or chosen an architectural direction that wasn't intended. If that assumption has subsequently propagated through thirty files, you now have a substantial amount of perfectly respectable-looking code built on top of the wrong idea.

The obvious solution is not particularly sophisticated. Don't let it get that far.

Make a small change. Test it. Look at what happened. Commit it if it makes sense. Then make the next change.

In other words, replace one plank.

## Small changes are about learning, not typing

I think it is easy to misunderstand incremental development as primarily a risk-management technique. Smaller changes are certainly easier to review and easier to roll back, but there is another reason I think they are particularly important when working with AI.

They create opportunities to learn.

Software development is rarely a process of taking a perfectly understood requirement and mechanically converting it into code. Requirements are incomplete, assumptions turn out to be wrong, existing systems behave in ways nobody documented, and sometimes you simply discover a better solution after you have started working on the problem. Even our tests ultimately reflect somebody's understanding of what the system is supposed to do.

A small development loop gives us a chance to update that understanding before going further.

This is exactly the kind of environment in which AI can be extremely useful. The agent can implement something quickly, we can inspect the result, run it, test it and decide what should happen next. The important improvement is not necessarily that the implementation took five minutes instead of fifty. It is that we can complete the whole cycle of deciding, implementing, observing and reconsidering much more quickly.

If the decision was wrong, very little has been lost. If it was right, we move to the next increment.

There is something slightly ironic about having access to extraordinarily powerful coding agents and deliberately asking them to do less. But I think that restraint is part of the skill.

## Are these really AI skills?

There is now a growing list of things developers are supposed to learn to remain relevant in the age of AI. Prompt engineering, context engineering, MCP, agents, sub-agents, skills, tools, model selection and various agent frameworks are all useful things to understand. I use several of them myself.

But I am not sure that expertise in any particular one of these things will turn out to be especially durable.

The tools are changing too quickly. Something that requires elaborate prompting today may simply work without prompting next year. Context management techniques that are important today may become less important as models and tooling improve. Today's favourite agent framework may be replaced by something considerably better, or the capability may simply become part of the development environment.

Compare that with some rather boring software engineering principles: keep changes small, separate responsibilities, make behaviour testable, understand your boundaries, make assumptions explicit, maintain a useful history of decisions, review changes, and keep the system in a working state.

Those ideas have survived several generations of technology already.

Interestingly, they also describe quite well how I want an AI coding agent to behave.

## AI makes engineering discipline more important

There is a tempting argument that as AI gets better at writing software, traditional software engineering expertise becomes less important. I am starting to think the opposite may be true.

When writing code was expensive, there was a natural limit on how much damage we could create in an afternoon. A developer had to physically type the code, work through compiler errors, look things up, write tests and gradually build the implementation. There was friction in the process. Some of that friction was waste, but it also provided time to think.

AI removes a remarkable amount of that friction.

This is mostly a good thing. I certainly don't want the friction back. But removing it means that the discipline has to come from somewhere else. If an agent can modify half the application in twenty minutes, knowing that it *can* do that is considerably less important than knowing when it *shouldn't*.

That distinction has very little to do with prompting.

It comes from understanding software engineering.

A good engineer knows that a technically elegant solution may be wrong for the context. They know that requirements contain assumptions. They know that abstractions have costs. They know that tests provide evidence rather than mathematical proof that everything is fine. They know that a large change is harder to reason about than a small one, even if both were produced in exactly the same amount of time.

These principles don't become obsolete because AI writes the code. If anything, AI amplifies the consequences of ignoring them.

## Back to the ship

Perhaps this is why I like the Ship of Theseus as a metaphor for AI-assisted development.

We have acquired a crew member who can replace wooden planks at an astonishing rate. It doesn't get tired, it doesn't complain about repetitive work, and if you give it sufficiently broad instructions it may replace half the ship before lunch.

The interesting engineering question is not how quickly it can replace the planks.

It is which plank we should replace next.

We still need to decide how much of the ship to change at once, how to establish that it remains seaworthy, and whether what we learned from the last replacement changes what we should do next. The faster our new crew member becomes, the more important those decisions become.

This is why I think the distinction between good AI-assisted development and good software development may eventually turn out to be much smaller than we currently imagine. There will certainly be new tools to learn and new techniques worth understanding, just as there have been throughout the history of our profession. But the tools have always changed faster than the principles.

Perhaps the developers who become particularly good at working with AI will not be the ones who memorise the largest collection of prompting techniques or become experts in every new agent framework. They will be the ones who were good at engineering software in the first place, and learn how to apply those same principles when the entity writing most of the code happens to be a machine.

After all, if you are going to replace every plank in the ship, having a faster carpenter is useful.

Knowing something about ships is probably more important.
