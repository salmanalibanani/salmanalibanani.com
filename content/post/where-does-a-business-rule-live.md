---
title: "Where Does a Business Rule Live?"
date: 2026-05-16
draft: false
summary: A business rule does not really live where the folder says it lives; it lives where its meaning is defined, protected, and kept from quietly becoming four different truths.
---

Software architects, like philosophers, have a habit of dealing with questions that seem simple and making everyone uncomfortable by thinking about them properly.

One such question is this: where does a business rule live?

At first, this sounds like the kind of thing a decent architecture diagram should answer. Then you open your editor to look at the code of a real enterprise system, and all sorts of expletives start to come out of your mouth.

The answer you would hope for is usually that it lives in the business layer, or the domain layer, or whatever part of the system has been given that responsibility. That sounds reasonable. In terms of physical artifacts, if we have a folder called Business or Domain etc, surely the business rules must live there.

But enterprise software is not always that predictable. That’s not what we pay consultants for.

In many systems, a business rule does not live in one place. It lives in the front end because the user needs quick validation. It lives in the back end because the server cannot trust the browser. It lives in the database because, a decade ago, someone manually updated the database using SQL, bypassed the application, and managed to bring the system down for a week. After that, the database was promoted from storage mechanism to part-time security guard. And then the same rule was replicated in the reporting module because the report had to show the same “correct” number.

At that point, that single business rule has turned into something else. It has spread like a rumour and settled into the system like an urban legend. It perhaps once descended from the heavens (i.e. the mind of the Architect), and then began replicating itself like Agent Smith in _The Matrix_.

## The Small Change

Imagine a rule that decides whether something is approved.

That word sounds simple. People use it in meetings with great confidence. “Only approved items should appear here.” Everyone nods. The ticket is written. The work begins.

Then, a few years later, the business changes what approved means. Maybe one status must now be excluded. Maybe another condition must be included. Maybe there is a special case that was always there in the real world but only recently discovered by the development team, as special cases normally do.

A developer picks up the ticket. They find the rule in the front end and change it. The screen now behaves correctly. Then they discover that the back end has its own version of the rule, which also makes sense, because the back end must enforce the real rule. So they change that as well.

The screen works. The API works. The tests that exist are passing. The release goes out.

Happy days, until someone runs a report. The number on the report is wrong. Not completely wrong. It is wrong in the typical enterprise way. You know, only wrong on the third Wednesday of the month when the report is grouped by region, and the temperature outside is between 37 and 39 degrees and your dog was crying between 4 and 4:15 pm.

Now there is an escalated ticket. Someone says this was clearly not tested. Someone else says it was tested, and technically they are both right. The screen was tested. The service was tested. The save operation was tested. But nobody tested the report because nobody remembered that the report had its own private version of the same business rule.

This is how a small change becomes a production issue. This is how a developer ends up fixing a report at 2 am on Saturday night, trying to work out why a query written years ago suddenly has a different understanding of “approved”.

## The Problem Is Definition, Not Duplication

It is easy to call this a duplication problem. In one sense, it is. The same rule exists in more than one place. But the more interesting problem is that the copied rule may not mean the same thing everywhere.

Some duplication is hard to avoid. The front end may need quick validation. The database may need to protect itself from creative human behavior. The report may need its own logic for performance or presentation. The DRY principle tells us not to repeat ourselves, and as a general instinct, that is fair enough. But real systems are rarely that clean. The same business rule often appears in the front end, the back end, the database, the reporting module, the export job, and possibly a spreadsheet called Final_Final_v7.xlsx.

So the real question is not whether repetition exists. It almost always does. The question is whether the repetition is deliberate and still connected to one clear definition. Real systems are not pure in that sense, and pretending they are pure is one of the fastest ways to create architecture that looks impressive and annoys everyone.

That brings us to the real problem: nobody knows which part of the system owns the definition. We say something is approved, but what does approved actually mean? At a human level, the word feels obvious. If an insurance claim is approved, we expect the money to arrive. If a leave request is approved, we expect someone to disappear for two weeks and return with photos and/or strong opinions about airport lounges.

But software cannot survive on that general sense of the word. A system needs to know what makes something approved. Is it approved when the manager clicks the button? When the status changes to `APPROVED`? When the doctor signs it, Finance reviews it, Compliance stops frowning, or someone’s mother prays very sincerely for the workflow to move forward? These sound like silly questions, but they are exactly the kind of silly questions that become serious after release.

In one part of the system, approved might mean the user clicked the approve button. In another, it might mean the workflow moved to a certain state. In the database, it might mean a flag was set by a stored procedure that everyone treats with respect because nobody wants to open it. In the report, it might mean the item has an approved date, has not been reversed, and falls inside a reporting period defined by someone who left the company several years ago but somehow still controls everyone’s weekend.

That is why the report bug matters. The report was not merely duplicating a condition. It was carrying its own definition of approval. The production issue happened because nobody remembered that definition existed.

## Wittgenstein and the Approval Workflow

Ludwig Wittgenstein is considered by many to be one of the most important philosophers of the twentieth century, though also one of the harder ones to casually bring into a software architecture blog post without alarming the reader.

In his later philosophy, Wittgenstein moved away from the idea that meaning is some fixed thing sitting behind a word, either in the world or inside someone’s mind. His famous line is that “the meaning of a word is its use in the language”. In simpler terms, do not only ask what a word points to. Look at what people do with it.

That idea is painfully practical in software. The meaning of “approved” in a system is not only found in the requirements document. It is not only found in the enum name. It is not only found in the workflow diagram that everyone likes, even though nobody has updated it since the last restructure. If we want to understand what “approved” really means in the application, we have to do what Wittgenstein suggests: look and see how the word is used.

So the useful question is not only, “Where is the approved rule implemented?” The better question is, “How is approval being used across the system?”

Wittgenstein also compares words to tools, and that analogy works nicely here. A hammer, a screwdriver, and a measuring tape may all sit in the same toolbox, but they do very different jobs. Words can be like that too. They may look the same across the system, but their function may be different in each place. “Approved” in the UI may be used to enable a button. “Approved” in the API may be used to reject an invalid operation. “Approved” in the report may be used to calculate a monthly number that someone will put into a board pack and then defend with unusual confidence.

The real meaning of “approval” in the system is not found in any one of these uses alone. It emerges from their amalgamation. That is exactly why this becomes a problem. If the front end, back end, database, and report all use approval slightly differently, then the system is not merely repeating the same rule. It is building several definitions under the same name, which is a very efficient way to create confusion while still sounding professional.

## What Do You Mean?

Justin Bieber has a song called _What Do You Mean?_. I must admit I am not a fan of his music, but I really like this particular song. I suspect he did not write it as a contribution to software architecture. Maybe he did. But there is no doubt that the question itself is surprisingly useful for architects.

What do you mean by approved?

What do you mean by active?

What do you mean by complete, locked, paid, discharged, cancelled, or eligible?

These are important architectural questions. They force us to slow down before we allow the same word to travel through the system with five slightly different passports. The moment we ask “what do you mean?”, we stop treating a business word as obvious and start treating it as something that must be defined, protected, and tested.

That is why the philosophical question matters. Philosophy is not being added to the discussion so the blog post can wear a slightly more expensive jacket. The question of meaning has a practical job. It helps us notice that a production bug may not come from bad syntax, bad SQL, or bad React code. It may come from the fact that different parts of the system answered the same meaning-question differently.

## So What Problem Are We Actually Solving?

At this point, it is tempting to say the solution is obvious. Put the rule in one place. Make the business layer own it. Make the domain model express it. Make everyone else call that. Then go home early and explain to your family that enterprise software has finally been brought under control.

That would be nice. It would also be slightly fictional.

A React component may still need to know enough about approval to guide the user. A database may still need checks to protect itself. A report may still need SQL logic because the reporting engine cannot call your domain model every time someone wants a monthly total. An export job may need to translate approval into a different status for another system, which probably also believes it is the centre of the universe.

So the problem we are solving is not the existence of repetition. The problem we are solving is confusion. When the same business rule appears in several places, the team needs to know which definition is correct, who owns it, and which other places are merely expressing that definition for local reasons.

That is what ownership means in this context. It does not mean every other part of the system becomes ignorant. It means the system knows which definition wins when two parts disagree.

If the React component treats something as approved, but the backend rejects it, the team should know which side carries authority. If the report counts something as approved, but the core workflow excludes it, the team should know which interpretation is wrong. Without that clarity, every copy of the rule becomes a possible truth, and the application starts behaving like a small country with five governments.

And philosophical thinking forces us to chase that clarity.

## Define the Meaning Before You Chase the Code

A practical starting point is to write the definition in business language. Not in a long document that will be uploaded to Confluence and then left there to become a historical artifact. Just a clear statement of what the business word means.

The complete, unambiguous definition of the business concept should be close to the code that owns the rule. It could live near the backend rule. It could live in an architecture decision record. It could live in a test fixture that names the business scenarios clearly. It could live in lightweight documentation that is linked from the relevant code or report. The exact format matters less than the fact that a developer can find it when the rule changes.

Because the uncomfortable truth is that documentation alone is weak. People do not always read documentation. Sometimes they do not even know it exists. Sometimes the documentation was written during a previous civilisation and now describes a system that exists only in memory and invoice history.

So documentation should not be the only protection. It should be supported by code, naming, tests, and visible links between the authoritative definition and its copies. Documentation can explain the meaning. Tests can make that meaning harder to accidentally break.

## Define It Once, Repeat It Consciously

Where possible, the executable version of the rule should live in the most authoritative part of the system. In many applications, that will be the domain layer, business layer, or backend service that controls the workflow. The folder name alone does not make it authoritative. The authority comes from the team agreeing that this is where the system’s official understanding of approval is expressed and changed.

Other parts of the system may still carry their own expressions of the rule. Front-end validation can help the user. A database constraint can protect integrity. A report condition can calculate a number efficiently. An export mapping can translate approval for another system. These expressions may be necessary, but they should remain connected to the same definition.

That connection should be visible. If a report carries approval logic, the team should know that it carries approval logic. The report query, report definition, documentation, or tests should make the relationship clear. This does not require a new governance committee, a twelve-page template, or a meeting called “Rule Ownership Alignment”, although someone will eventually suggest one. It only requires enough honesty so that the next developer can see that this SQL is not random filtering. It is a copy of a business definition.

This is the difference between repetition and drift. Repetition is unavoidable, but drifting meaning is what keeps good architects up at night.

## Let the Tests Carry the Meaning

This is where tests become more useful than documentation. Documentation can explain the rule, but nobody reads it. Tests keep asking whether the system still believes it. And people do read tests, especially when they fail and block the build.

When a business rule appears in different parts of the product, the tests should carry a shared sense of identity. A React component test, a backend unit test, and a report validation test may use different tools, but if they are testing approval, their names and scenarios should make it clear that they are testing the same business idea.

Suppose the definition says that an approved item must have an approved status, a valid approved date, and must not be reversed. The React test may verify that a reversed item is not shown as approved. The backend test may verify that the same reversed item is rejected by the approval rule. The report test may verify that the same item is excluded from the approved total.

Those tests should not be identical, but they should look related. A developer reading them in different parts of the system should feel that these tests belong to the same family, not that they were written by three departments who only communicate through incident tickets.

This is where the philosophical question becomes practical. Asking “what does approved mean?” forces us to create examples. Those examples become tests. The philosophy does not sit above the code wearing a robe and looking wise. It becomes ordinary test cases that say: this is approved, this is not approved, and this used to look approved but is no longer approved because it was reversed.

## Help the Next Developer

A useful way to judge this design is to imagine a developer joining the team six months from now. They are given a ticket that changes what approved means. They did not attend the original meetings. They do not know why the report has that strange condition. They do not know about the SQL incident from a decade ago.

That future developer matters. When we write code, we are not only telling the computer what to do today. We are also leaving something behind for someone else to read later. In that sense, code is a kind of time capsule. One day it will be opened by a different civilization: a developer with limited context and a ticket that says “small approval change”.

What helps that developer avoid mistakes? A clear business definition helps. An authoritative implementation helps. A visible list of known copies helps. Test scenarios that repeat the same business examples across the front end, backend, and report help even more.

The developer should be able to discover that the approval rule is used in the React screen, the backend workflow, the monthly report, and the export. They should be able to see which implementation owns the definition and which ones are copies. They should be able to run tests that show whether the copies still agree with the definition.

That is a practical architectural outcome. The system is no longer relying on someone remembering that the report has its own version of the rule. The knowledge is present in the shape of the code, the names of the tests, the report comments, and the examples used across layers. It is written with the awareness that somebody, someday, will have to read it without having lived through the history that produced it.

## Follow One Word Through the System

One practical way to test your architecture is to choose one important business word and follow it through the system. Pick something like approved, active, customer, discharged, overdue, paid or cancelled. These words look harmless, but they often carry more business meaning than the architecture documents sitting quietly in Confluence.

Ask what that word means on the screen, in the backend, in the database, in the report, and in any export or integration. Then ask whether those uses connect back to one definition, or whether each part of the system has developed its own private interpretation.

Also look at the tests. If different layers test approval, do their test names and scenarios show that they are protecting the same concept? Does the React test about approval look connected to the backend test about approval? Does the report validation use the same business examples? Or is each test only checking local behaviour while the larger business meaning drifts around unsupervised?

This exercise is not glamorous. It mostly involves reading old code, old reports, and old assumptions while developing complicated feelings about people you have never met. But it will tell you something useful. It will tell you whether your architecture protects meaning, or merely organises files.

## Where the Rule Actually Lives

So where does a business rule live? The simple answer says it belongs in the business layer, or the domain layer, or whichever part of the system owns business meaning. That answer points in the right direction, but it does not go far enough, because real systems often need the same rule to appear in more than one place.

A better answer is that a business rule lives where its meaning is defined, owned, and kept consistent. Copies may exist elsewhere, but they should remain copies, not competing truths. The goal is not architectural purity. The goal is traceability of meaning.

Define the rule clearly. Make the unavoidable copies visible. Design the tests so that different layers still speak the same business language. When the business asks what approved means, the system should not respond with four different answers and a report query written in 2017.

That is where the rule actually lives: in the combination of definition, ownership, visible copies, and tests that stop meaning from drifting quietly across the system. Otherwise, what we have is not architecture in any useful sense. It is archaeology with a production incident attached.
