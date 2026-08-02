---
title: "AI Can Write the Code. Judgment Still Decides the Outcome"
date: 2026-08-02T11:15:31+10:00
draft: false
summary: "AI can produce good code while following a confident but wrong line of reasoning. A better engineering workflow supplies the judgment it lacks."
tags: ["ai", "engineering", "architecture", "process"]
---

There is a very specific kind of AI-assisted engineering failure that I think most developers will recognise soon enough.

The model writes good code. It reads a surprising amount of the repository. It suggests sensible-looking options. It creates tests. It may even give you a very polished explanation, complete with headings and bullet points, of why the thing you want cannot be done.

Then you discover that it can be done. The relevant capability is in the platform documentation, or in a library guide, or in a part of the project that the model never thought to inspect. Nothing was technically difficult. The reasoning simply started from the wrong place and became more confident from there.

The issue sits upstream of code. The code can be fine; the path used to arrive at it is the problem.

## AI-assisted software engineering goes both ways

We tend to talk about AI-assisted software engineering as though the assistance only travels in one direction. The developer asks for help. The AI investigates, writes code, creates tests, explains an unfamiliar part of the system, and hopefully does not delete the production database while trying to improve a variable name. So far, a very normal day in enterprise software.

That is useful assistance. But it is only half of the relationship.

The AI also needs assistance from the engineer. It needs to know which documentation carries authority, what the business is trying to achieve, which constraints are deliberate, which decisions are still open, and where it should stop and ask rather than confidently filling in the blank. It needs someone to recognise when a sensible-looking answer is based on the wrong assumption.

In other words, the human helps the model reason. They provide the context, boundaries, priorities, and healthy scepticism that stop a fast implementation from becoming a fast mistake.

That is why I think "AI-assisted software engineering" is slightly misleading. The useful model pairs an engineer and an AI, each assisting the other with different strengths and different ways of getting things wrong. Like a buddy-cop film, except both detectives can write TypeScript and neither one is allowed near production without supervision.

<figure>
  <img src="/img/ai-can-write-the-code-judgment-still-decides-the-outcome/WhereIsMyAssistance.png" alt="An engineer and an AI wondering where their assistance is" />
  <figcaption>AI-assisted software engineering, from both perspectives</figcaption>
</figure>

## The confident wrong answer

We have all seen a human version of this. Someone opens an old module, finds a strange workaround, and assumes it represents the intended architecture. They copy it into the next feature. Then six months later the codebase contains three versions of the same workaround, each with a slightly different name, and a meeting is arranged to discuss "alignment". Nobody is entirely sure what alignment means, but there will be a PowerPoint.

AI can do this much faster, which is progress in the same sense that a faster photocopier is progress when somebody is duplicating the wrong form.

It can look at the code before it looks at the platform. It can treat a local document as the complete source of truth. It can find the first implementation that appears relevant and build the rest of its answer on top of it. If that initial assumption is wrong, the work that follows may still be beautifully implemented, tested, documented, and completely aimed at the wrong problem.

A good outcome requires more than a technically working answer.

The uncomfortable part is that models are very good at making the wrong direction feel reasonable. They do not get tired. They do not become visibly uncertain. They do not stare out of the window for five minutes and suddenly remember that the platform documentation might contain an entire section on the thing they are about to reimplement.

That last instinct comes from experience. Usually unpleasant experience, involving an incident channel, a hotfix, and somebody saying "we have always done it this way" as though that improves the situation.

## The battle scars gap

A senior engineer accumulates a collection of reflexes over time:

1. Check what the platform supports before declaring a limitation.
2. Find the real source of truth before trusting the closest convenient one.
3. Treat a "small" architectural change with suspicion until its dependencies are visible.
4. Notice when a technical question is really a product decision in disguise.
5. Get a second opinion when one more fix has been almost working for several hours.

These are humble skills. "Opened the documentation before creating a workaround" rarely appears at the top of a LinkedIn profile, although perhaps it should. These habits stop a good implementation from becoming an expensive detour with a very confident pull request description.

This is primarily a judgment gap. A model can be given the documentation, the codebase, the issue tracker, and enough context to make a small consulting firm nervous. It still needs an explicit process that tells it what to trust, what to question, and when a decision still needs to be made.

Context is useful. Context without an order is just a larger pile of things to misunderstand, like a Confluence space after three reorganisations.

## The planning problem appears before the code

I have been building an AI-assisted delivery workflow around this idea. The aim is practical: turn useful engineering habits into visible steps that can be followed and reviewed. Giving one agent decades of engineering judgment would be ambitious even by current AI marketing standards.

One example came from reviewing a set of planned work items for a larger feature. At first glance, the work looked like a normal sequence of tickets. Build the foundation, add the integration, add the user flow, test it at the end. Very neat. Almost suspiciously neat.

Once the plan was examined properly, several different problems appeared, as they tend to do when a plan is asked any questions at all:

- A data-model decision was actually blocked by an unanswered product question.
- A proposed integration relied on assumptions about an external platform that needed to be checked against its documented limits.
- A background process needed explicit retry, reconciliation, duplicate-handling, and observability behaviour instead of inheriting a few convenient constants from an unrelated part of the codebase.
- End-to-end testing depended on infrastructure and a test environment that did not yet exist.

The feature remained viable. These findings completely changed the order in which the work had to happen.

The resulting plan had explicit blockers, a dependency graph, separate foundation tickets, clear non-goals, and a distinction between product decisions and engineering decisions.

That is exactly the kind of reasoning we normally expect from a good technical lead. Its value came from making the unknowns visible before somebody started writing code around them, regardless of who produced the list.

## Put the sources of truth in order

The first guardrail is simple enough to put in a repository instruction file:

1. Platform documentation.
2. Project documentation.
3. The code.

This order is deliberate.

Platform documentation tells us what the underlying technology is meant to support. Project documentation records the decisions, constraints, and conventions we have chosen. The code tells us what exists today, including the useful parts, the historical accidents, and the part that was written late on a Friday because the release was somehow tomorrow.

All three are valuable, and their authority varies with the question being asked.

If an agent starts with code alone, it can mistake a workaround for a design decision. If it starts with project documentation alone, it can faithfully preserve a gap in that documentation. Starting with the platform creates a better foundation for the rest of the investigation.

The code remains essential. Reality eventually becomes unavoidable there, usually at the exact point a test fails in a way nobody can explain. The workflow asks the right questions before treating the current implementation as the answer.

## Turn assumptions into tickets

The next part is about issue quality.

An AI can turn a sentence like "add the new workflow" into a pull request surprisingly quickly. That speed exposes a problem: a short instruction can hide a business rule, a security boundary, a deployment concern, a licensing decision, a migration path, or a definition that different parts of the system have been interpreting differently for years.

So the workflow I am building treats an issue as more than a todo item. A useful issue records the context, relevant specifications and business rules, likely code areas, expected tests, local validation, dependencies, non-goals, and a definition of done. It also states the decisions that are still open rather than quietly turning them into implementation choices.

This has a couple of benefits.

First, it gives the builder a better brief by distinguishing intentional exclusions from missing requirements. Second, it gives the reviewer something concrete to compare the implementation against. A pull request review can assess bugs, scope, and whether the change solved the ticket that was actually written.

Most importantly, it makes product decisions visible. Choices affecting the commercial model, the user experience, a security boundary, or the long-term architecture deserve a decision with options and consequences before implementation begins.

That sounds obvious until you see how often an architectural diagram is really just a product decision wearing a technical hat and insisting that it is here for a quick technical discussion.

## Ask the questions before the implementation hides them

I have also started asking the planning agent to present decision impacts in an interactive Q&A format. Give the options. Explain what each one affects. State the recommended option and why. Be honest about what is not yet known.

The aim is to catch the wrong turn early, rather than turn every change into a committee meeting with myself. One person and an AI can still somehow generate too much bureaucracy, including action items that require clarification before they can become action items.

The point is to catch the wrong turn early.

Once a branch contains several hundred lines of plausible code, the architecture begins to feel more real than the decision that produced it. People become reluctant to revisit the premise because the implementation already exists. AI is especially good at encouraging this feeling: it can make the next version cheaply, so we keep trying to rescue the current direction instead of asking whether it is the right one.

An explicit decision stage creates a useful pause. The agent can investigate and recommend. The human can decide whether the trade-off is acceptable. Commercial, architectural, and operational choices then receive the attention they deserve.

## Separate the roles

The wider workflow uses separate stages for requirements, issue planning, building, and review.

A requirements stage turns a business need into something precise enough to discuss. A planning stage examines documentation, code, dependencies, and risks. A builder implements the agreed work, writes and runs tests, and prepares a pull request. A reviewer checks the change independently for regressions, requirement mismatches, missing validation, and assumptions that have quietly escaped into production candidates.

These stages create independent challenge. A separate reviewer can assess the work rather than leaving one agent to grade its own first idea. We have enough of that energy in architecture reviews already.

I wrote previously about a <a target="_blank" href="/2026/07/04/a-two-agent-pr-workflow-claude-writes-codex-reviews/">two-agent pull request workflow</a> that explored an automated review handoff. The principle is the same even when the tools change: a handoff creates an opportunity for challenge. In the workflow I am using here, however, the human remains the merge gate. A reviewer can ask whether the requirement was understood, whether the source of truth was correct, whether an existing capability has been rebuilt, and whether the tests prove the useful behaviour rather than merely the new implementation.

Some decisions remain human gates. Explicit approval is the condition for merging; a green build and the absence of comments provide evidence, not a decision. Consequential deployments stay as deliberate human actions, and agents wait for approval before merging a pull request.

These controls make using AI at speed more reliable.

## Documentation is part of the control system

Documentation functions as part of the control system.

Repository instructions capture policies that should apply consistently. Architecture notes explain choices that cannot be recovered from a class name. Issues record scope, exclusions, dependencies, and decisions. Pull requests describe what changed and how it was checked. Review comments preserve the questions that mattered at the time.

This becomes more important as generating code gets cheaper. A team can now create a large diff before lunch. Six months later, somebody else still needs to understand whether that change was a careful decision or an especially confident hallucination with unit tests and 98% coverage.

The old excuse was that documentation takes too long. That excuse is getting weaker. AI can help draft the issue, organise the decision record, write the pull request summary, and capture validation results. Human work lies in deciding which facts must be true before those words should be trusted.

## The second opinion problem

One thing I still want to improve is adversarial review of plans. The standard engineering answer when you are deep in a problem is to get another person to challenge the approach. The same principle applies here.

The first model may have an excellent answer. It may also have a very elegant explanation of the first wrong assumption it made. A separate review step, ideally from a different model or a human, can ask the annoying questions that the original reasoning quietly avoided.

A bounded review pass is often enough; otherwise agents may debate each other until the heat death of the universe, with one of them opening a ticket to investigate the heat death. The aim is practical confidence and a lower chance that one confident line of reasoning becomes the entire architecture.

## Working code and the right outcome

AI is making coding easier. That part is real, useful, and occasionally ridiculous in the best way. We can investigate repositories faster, produce tests faster, document changes better, and turn a well-defined issue into a reviewed pull request with far less manual effort than before.

But a working implementation is only one part of a good outcome.

The harder part is still deciding what should be built, what the platform actually supports, which document carries authority, which unknown must be resolved first, which trade-off is acceptable, and when an apparently small change carries large consequences.

The engineer's role remains essential. The typing part of the job is becoming much less central, while the responsibility for judgment is becoming impossible to ignore.

The useful engineer is increasingly the person who can fill the gaps between requirements, architecture, business intent, and operational reality. AI can help with all of those things. It can even help create a workflow that catches more of its own mistakes.

But it still needs somebody to ask the question that battle scars teach you to ask:

"Before we build around this, have we checked the obvious thing?"
