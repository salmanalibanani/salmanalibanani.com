---
title: "Living in Silos"
date: 2021-10-09T16:05:37+11:00
draft: false
---
I still remember it as if it all happened yesterday.

It was a Monday morning.  I was under the pump.  The extension to the API end point that my customer had asked for was critical for signing off the contract.  But the backend for that API was owned by another team in the company.

I emailed the scrum master working with that team.

"Hello!  Can you guys please prioritize the changes to the API we discussed last week.  I am hoping you can pull this off by the end of the week, so that we can finish our part and show our customer that we have the capability to deliver this kind of stuff.  You already have the specifics in the JIRA ticket I assigned to you."

Unfortunately, I didn't get a reply.

Next Monday, I called my boss to talk to their boss about the backend changes.  Half an hour later, I get a call from the person I emailed last Monday.  After exchange of pleasantries, they told me that their team's backlog had been finalized for the next three months, and I could expect the requested changes to hit the production by early next year.



This was in August, by the way.

## The life of a startup

In the beginning there is an idea.  The idea is good, and then somebody says "let there be an investor who can pay for this to be build".  And therefore an investor emerges, and with their money the founders hire five developers and put them in a space in Stone & Chalk in the Sydney CBD.  Three months later, the idea comes to life and hits production.  

And the founders and investors like it, and they say "let there be an IPO".  Well, three months is probably too soon, but they want that eventually.

In early days of the startup, the team iterates and innovates fast.  If it all works out as expected, then the company gets a riddiculous valuation and goes public.  The founders get featured on AFR or StartupDaily, and then leave the business with their billions.  It's a familiar story.

What happens next to the company can either be a comedy or a tragedy, depending upon your perspective.

The new business (minus the founders it effectively is a new business) now has more cash than they know what to do with, so the development team expands from 5 developers to 50.  The company employs scrum masters and business development managers, senior test analysts and junior people & culture leads, executive vice presidents of innovation and junior assistant growth managers for the SME clients.  Before you know it, you now have a middle management.

To address the scaling problems, the newly hired engineering managers (a role that didn't exist before the company went public) decide that they will have to break the code base down in smaller chunks and get a two-pizza team to work on each part.  Evey self respecting developer loves pizza, right?  Some prefer breaking the assets horizontally (frontend, backend, middle tier etc), others have heard of this sexy new concept called "microservices".  They choose one, depending on whatever the read about microservices the week before.

And two years later, you find me scratching my head trying to figure out what should I do when a simple change in a code base owned by another team needs six months of wait time.  "It used to take a couple of hours in good old days", I told myself back in August.  And if you are still reading this, you probabaly did too.

## The rise of the middle management

I have nothing against middle management.  I probably haven't explained yet their role in the issues I have raised above.  I don't even know what the solution to this scaling problem is.  I do know, however, what a bad solution to this problem in the life of a startup looks like.

Typically within each development team you find roles such as a BA, a product manager or owner (and if you know the difference between a product manager and a product owner, please do reach out to me), and a scrum master.  I would call them the support roles, and I have no intention to minimize the importance of such roles.  They are support roles because the main function of the team is to deliver working software and these team members don't write code.  I do believe that their support is critical in meeting team's objectives.  But I want to classify these roles in a single bucket of "supporting roles" to make a point later.

Another important thing to recognize here is that although these standard job titles seem to be used a lot in the industry today (Scrum Master, BA, Delivery Lead etc.), in reality each company has different nuances to these roles.  So the first thing that goes wrong with the introduction of these roles is that nobody knows what their job really is!  As a consequence, you see rising tensions between various members of your team.  You hear your delivery manager saying "hey she is just a tech lead, project timelines and delivery is my responsibility, not hers.  She only delivers code!"

If you can't see the sheer stupidity of that statement, I humbly request you not to waste your time reading this and go back to your conversation next to that water cooler.  You will not find anything useful in the rest of this blog post.

## The rise of territorial boundaries

People have different approaches to their work.  Some have a process oriented mindset: you give them a team and they will define who does what, maintain a backlog of work to be done, keep everyone busy etc; their focus is on the management of "state of affairs" and they believe that the outcomes will follow if process is perfected.  

Others are problem solvers: you give them a problem statement, and they deliver a working, end-to-end solution.  For them process is only an enabler and nothing more.  It's only the outcome that counts.  If a process doesn't work for them, they want to throw it away and design a different process.  They are pragmatists. 

There is probably no intrinsic problem with either of these two approaches, but they do not work well together.

The process peeps, by the very nature of their approach, tend to be more territorial.  These middle managers are running a team, so it matters to them that the area of responsibility of their team is clearly defined.  They are concerned with story points, team velocity and all those bar charts and pie charts to show their higher management how team is functioning.  They aspire to get the job of their boss some day.  Therefore a big number is good when it comes to story points delivered per sprint.  To them it seems to measure something, even if they don't know what it actually is!  For some of them it's even better when the other team's code doesn't hit production on time, because it shows to the higher management that "my team is better than their team".

"I delivered 15000 story points per sprint over the last 17 years.  I deserve a promotion", they tell their boss during their annual KPI meeting.

The outcome peeps don't think that way.  They don't like to be placed in silos.  When they see a problem, they want to fix it and it doesn't matter to them who owns the code.  They want working software to hit production as often as possible, and they get frustrated with the territory based excuses like "sorry I am too busy with what my boss has put into my backlog".  They get even more frustrated when they see their "process peers" totally missing the point that all teams actually work for a single business, and they believe that it's everybody's problem if any team is struggling.

## Middle manager makes technical decisions

This eventually happens.  And I will tell you why this is bad.

Typically the BAs and Scrum Masters I have met have no experience of writing working software.  They don't understand I don't have a better solution to the scaling problem. Eventually you want more hands on deck. And emergence of the middle management is a natural consequence of that need for growth. What does matter though is that how that management layer approaches its role. My argument is that a process/territory oriented middle management team hurts your business more than it helps. Often the people in roles like scrum master or deliver leads are people who haven't been a software developers themselves. I am not an elitist and do hate it when a smart developer thinks that they can be as good at everything in life. Anyone can be arrogant. But I do believe that not everyone can appreciate the mental process of a developer who is solving a problem in code without knowing how the eventual solution is going to look like. If as a delivery person you believe that coding is as structured as the work of an accountant or a plumber, you are more likely to be part of the problem then the solution. I have utmost respect for all other professions, but I believe that the mind of a software developer works differently.









