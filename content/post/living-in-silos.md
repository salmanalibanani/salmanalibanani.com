---
title: "Living in Silos"
date: 2021-10-09T16:05:37+11:00
draft: false
---
I still remember it as if it all happened yesterday.

It was a Monday morning.  I was under the pump.  The extension to the API end point that my customer had asked for was critical for signing off the contract.  But the backend for that API was owned by another team in the company.

I emailed the scrum master looking after that team.

"Hey mate, can you guys please prioritize the changes to the API we discussed last week.  I am hoping you can pull this off by the end of the week, so that we can finish our part and show our customer that we have the capability to deliver this kind of stuff.  You already have the specifics in the JIRA ticket I assigned to you."

Unfortunately, I didn't get a reply.

Next Monday, I called my boss to talk to their boss about the backend changes.  Half an hour later, I get a call from the person I emailed to.  After exchange of pleasantries, they told me that their team's backlog had been finalized for the next three months, and I could expect the requested changes to hit the production by early next year.

This was in August, by the way.  What happened next doesn't really matter.

## The life of a startup

In the beginning there is an idea.  The idea is good, and then somebody says "let there be an investor who can pay for this".  And with that money from the early investors the founders hire five developers and put them in a space in Stone & Chalk in the Sydney CBD.  Three months later, the idea hits production, alive and kicking.  And then the founders say "let there be an IPO".  Well, that may be too soon, but they want that eventually.

In early days, the team iterates fast and innovates.  If it all works out as expected, then the company gets a riddiculous valuation and goes public.  The founders get featured on AFR or StartupDaily, and then leave the business with their billions.  It's a familiar story.

What happens next can either be a comedy or a tragedy, depending upon your perspective.

The business now has more cash than they know what to do with, so the development team expands from 5 developers to 50.  The company employs scrum masters and business development managers, senior test analysts and junior people and culture leads, executive vice president of innovation and junior assistant growth managers for the SME clients.  Before you know it, you now have a middle management.

To address the scaling problems, the newly hired engineering managers (a role that didn't exist before the company went public) decide that they will have to break the code base down in smaller chunks and get a two-pizza team to work on each part.  Some prefer breaking things horizontally (frontend, backend, middle tier etc), others have heard of this strange concept called "microservices".  For the business, it's really like flip of a coin which style is eventually chosen by the managers. 

And two years later, you find me scratching my head trying to figure out what should I do when a simple change in a code base owned by another team needs six months of wait time.  

"It used to take a couple of hours in good old days", I told myself back in August.  And if you are still reading this, you probably did too.

## The birth of the middle management

I have nothing against middle management.  I probably haven't explained yet their role in the issues I have raised above.  I don't even know what the solution to this scaling problem is.  I do know, however, what a bad solution looks like.

Typically with each team you expect a BA, a product manager or owner (and if you know the difference between a product manager and a product owner, please do reach out to me), and a scrum master.  Now, these are standard job titles that seem to be used a lot in the industry today.  But each company has a different implementation of each role.  So the first thing that goes wrong with the introduction of these roles is that nobody knows what their job really is!  As a consequence, you see the rise of the tensions between various members of your team.  You hear your delivery manager saying "hey she is just a tech lead, project timelines and delivery is my responsibility, not hers".

If you can't see the silliness of that statement, I request you to stop reading and go back to your conversation next to that water cooler.  I have nothing useful for you here.

## The rise of territorial boundaries

People have different approaches to their work.   Some have a proces oriented mindset: you give them a team and they will define who does what, maintain a backlog of work to be done, keep everyone busy etc; their focus is on the "state of affairs" and they believe that the outcomes will follow if process is perfected.  

Others are problem solvers: you give them a problem statement, and they deliver a working, end-to-end solution.  For them process is only an enabler and nothing much.  It's only the outcome that counts.  If process doesn't work for them, it's ok for them to throw it away and define a different process.  They are pragmatists. 

There is probably no intrinsic problem with either of these two approaches, but they do not work well together.

The process peeps, by the very nature of their approach, tend to be more territorial.  They are running a team, so it matters to them that the area of responsibility of their team is clearly defined.  They are concerned with story points, team velocity and all those bar charts and pie charts to see how team is functioning.  A big number is a good number when it comes to story points delivered per sprint.  The best code that doesn't hit production is the code that was the responsibility of the other team.  In fact, the more of that happens the better because it shows to the higher management that "my team is better than their team".

The outcome peeps don't like to be placed in silos.  When they see a problem, they want to fix it and it doesn't matter to them who owns the code.  They want working software to hit production as often as possible, and they get frustrated with the territory based excuses like "sorry I am too busy the backlog my boss gave me".  They tend to miss the fact that all teams actually work for a single business.  So, if another "territory" isn't doing well, it's their problem too.

## Middle managers do their thing

I don't have a better solution to the scaling problem.  Eventually you want more hands on deck.  And emergence of the middle management is a natural consequence of that need for growth.  What does matter though is that how that management layer approaches its role.  My argument is that a process/territory oriented middle management team hurts your business more than it helps.












