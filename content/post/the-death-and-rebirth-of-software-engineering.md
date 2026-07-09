---
title: "The Death and Rebirth of Software Engineering"
date: 2026-07-10T06:51:17+10:00
draft: false
summary: "AI pushed software engineering into a different form, where product thinking and respect for process matter more than ever."
tags: ["ai", "engineering"]
---

In many ancient cultures, death is the start of some kind of transformation. The thing that dies is supposed to move into a higher and usually unobservable state, which is a very convenient arrangement because nobody can verify it. The ancient Egyptians had the whole afterlife bureaucracy worked out, complete with Osiris, Anubis, and a heart being weighed against the feather of Ma'at. The Greeks gave us the phoenix, which politely catches fire and returns as itself with better branding. In some Indian traditions, death is part of a continuing cycle of rebirth. So the old world had a fairly consistent message: death may simply be a dramatic change of operating model.

What does that have to do with software engineering? I think AI code generation has pushed the profession into something similar. Software engineering is still here, at least for now. Perhaps one day I will have to come back and admit that this paragraph aged badly. For now, the profession is going through a transformation.

We are generating more code than ever precisely because code generation has become so cheap. A developer can now produce in an afternoon what used to take a team several days of typing, searching, refactoring, and mild emotional decline. The volume of code keeps going up. That is exactly why the profession is changing. When code becomes cheap, judgment becomes expensive.

This shift feels apocalyptic to a lot of developers, and with good reason. If your main advantage was speed of implementation, the market has just become very rude. I think two skills are becoming central for anyone who wants to remain useful in this new world: product thinking, and respect for process.

## Product thinking

In my experience, the best teams were defined by how quickly they learned. They had a short loop between deciding, building, observing, and correcting course. AI helps a lot with the building part. Deciding what is worth building in the first place is still the harder part.

That matters because a fast team can now build the wrong thing much more efficiently than before. You can generate three versions of a feature before anyone has properly answered the annoying but important questions. Should this field be mandatory? Does this workflow make sense for the business? Is this edge case urgent, or is it just interesting? Should we simplify the requirement instead of automating every exception that wandered in from a meeting?

These are product decisions, even when they arrive disguised as technical details. And increasingly, developers are the people closest to them. If every small decision has to be escalated through three layers of ceremony before a field can become optional, the team is only producing the visual effects of movement.

Claude can write good code once you tell it what to build. It does not know what your product is trying to optimize for. It does not know which compromise is acceptable, which business rule is sacred, or which user annoyance is small enough to live with for another month. Someone still has to own that judgment, and more and more that someone is the developer.

## Respect for process

The second skill is probably more important.

AI makes it easy to change a lot of code and leave behind very little explanation. You can refactor a module, rename a concept across half the repository, update the tests, and submit the pull request before lunch. Wonderful. Then six months later somebody else opens the diff history and tries to work out whether that big change was a careful design decision or an especially confident hallucination with unit tests. With this much generated code flying around, traceability becomes a serious problem. The hardest part is often not understanding what changed, but why it was done in the first place.

This is where process starts to matter. I mean the basic discipline of leaving behind a usable trail of intent. Why was this changed? What trade-off was accepted? What was deliberately left out? What risk was considered acceptable at the time? That is also why a human who understands the older engineering disciplines around tickets, reviews, documentation, and change history is still crucial to the process.

In a <a target="_blank" href="/2026/07/04/a-two-agent-pr-workflow-claude-writes-codex-reviews/">recent post</a> I described a workflow where Claude implements a change and Codex reviews it. One quiet benefit of that setup is that documentation becomes easier than it used to be. AI can write the Jira comment. It can summarize the trade-off. It can draft a pull request description that actually says something useful. Through GitHub or an MCP server, it can update the ticket with what changed and why. The old excuse that documentation takes too much time is getting weaker by the day.

A developer who respects process treats tickets, pull request descriptions, and commit messages as part of the product itself. They hold the memory of why the system looks the way it does. When that memory disappears, the code base starts to feel haunted.

Put the two skills together and the shape of the new profession becomes clearer. Product thinking on its own turns into fast chaos. Process on its own turns into slow theatre. The useful developer is the one who can decide well and explain clearly.

That is why I do not think software engineering has died. The typing part of the job has been heavily devalued. The surrounding judgment has become more exposed. AI removed one of the easier ways engineers used to prove that they were valuable.

The hard part has always been deciding what deserved to exist, shaping it into something coherent, and leaving enough reasoning behind that the next person can continue the work without needing a seance.

If there is a rebirth happening, I suspect that is what is being reborn.
