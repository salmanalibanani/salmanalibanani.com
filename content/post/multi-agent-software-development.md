---
title: "Multi-Agent Software Development using OpenClaw"
date: 2026-11-18T06:45:00+11:00
draft: false
summary: "How I use OpenClaw to coordinate specialised AI agents for software development, testing, handoffs and recovery."
tags:
  ["AI", "OpenClaw", "software-development", "agents", "GitHub", "automation"]
---

Humans have always been good at giving a human face to things that are not human.

We call this anthropomorphism: the habit of imagining non-human forces as having human intentions, emotions and personalities. Across many cultures, people have given storms, rivers, mountains and the sea names, moods and motives. Forces of nature that were difficult to understand became gods, spirits or characters in stories. A storm was no longer just pressure, wind and water; it could be angry. A river could be generous, dangerous or offended.

This tendency is understandable. When something is powerful, unpredictable or difficult to explain, a persona gives us a way to relate to it. It turns an abstract force into something that can be addressed, negotiated with or blamed. It makes the unknown feel slightly less unknown.

Popular culture has been preparing us for the software-agent version of this for years. In the Iron Man films, Tony Stark does not merely use a computer interface; he talks to JARVIS, an artificial intelligence with a voice, a name, a sense of humour and an apparently helpful opinion about what is happening. JARVIS is software, but the interaction is deliberately shaped like a relationship between people.

We are now doing something similar with software agents.

Recently, I have been building a multi-agent software development workflow using OpenClaw. The agents are not gods, spirits or people. They are software running on computers, operating within a set of tools, permissions and instructions. Yet I still find myself giving them names, assigning them roles and talking about what they know or what they are doing.

I should admit that I feel slightly weird naming these things and giving them personalities. Simon, Steve and Jensen sound less like software components and more like three people I might meet in a pub after work. In a way, they are the friends I never had: always available, happy to discuss a pull request, and incapable of asking me to help them move house.

There is also a slightly uncomfortable thought lurking behind all of this: I may be building a system that can eventually do parts of my own job. It is difficult not to notice the irony of automating work while wondering whether the automation will one day automate me. I tell myself that this is about making software development better, not making myself redundant. I also tell myself that a person who builds their own replacement deserves at least a decent reference letter from it.

The fact that I am saying this to a group of named software processes probably deserves its own anthropological footnote.

## Why a team of agents?

There is a particular moment in software development when a project becomes large enough that one person can no longer keep the whole thing in their head.

The requirements are in one place, the implementation is somewhere else, the test environment is running a different commit, somebody has reported a failure, and a pull request is waiting for a review. At that point, adding more chat messages rarely improves the situation. What is needed is a system for coordinating the work.

The idea is not to create one very clever agent that does everything. The idea is to create several specialised agents, give each of them a narrow responsibility, and use an explicit protocol to coordinate them.

One agent coordinates the work. One implements the changes. Another independently tests them. GitHub remains the source of truth, while OpenClaw provides the communication and orchestration layer.

It is a small software team, except that nobody takes lunch breaks and one of them occasionally needs to be reminded that a successful `git push` is not the same thing as a successful test.

## The basic structure

The system currently has three roles:

1. **Simon**, the coordinator agent.
2. **Steve**, the developer agent.
3. **Jensen**, the independent tester agent.

Simon is the point of contact for the human. The coordinator agent is running on an old Mac Mini. At one point I was going to name Simon **HAL 9000**. Then I realised that this might not end well for me. A coordinator called HAL, running quietly on a machine and calmly reporting that everything is under control, is exactly the sort of joke that becomes a post-incident report later. Simon seemed safer. At least Simon has not yet asked me to open the pod bay doors.

Steve changes the application. Jensen tests the application through the user interface. Neither agent owns the whole process.

The structure looks like this:

```text
                         Human
                           |
                           v
                 +-------------------+
                 | Simon             |
                 | Coordinator       |
                 +-------------------+
                    |             |
       implementation order       test order
                    v             v
          +----------------+   +----------------+
          | Steve          |   | Jensen         |
          | Developer      |   | Independent    |
          |                |   | Tester         |
          +----------------+   +----------------+
                    |             ^
                    |             |
                    +-------------+
                    Test result routed
                    back through Simon

                 GitHub is the source of truth
```

The important part is that Steve and Jensen do not become an uncontrolled conversation.

Steve does not directly dispatch Jensen. Jensen does not directly ask Steve to make changes. Their work is connected through Simon, and the durable record of the work is kept in GitHub.

As a hardware aside, Jensen is running on an old laptop with an NVIDIA chip. Steve is actually running on a refurbished M1 MacBook Pro. I am thinking of eventually renaming Steve to <a href="https://en.wikipedia.org/wiki/Alice_Huang_(executive)" target="_blank">Alice</a>, but for now we will go with Steve.

## From experiment to development system

This setup is also a useful measure of how quickly things have changed for me.

On 4 July 2026, I wrote about a small experiment where one AI agent wrote code and another agent reviewed its pull request: [A Two-Agent PR Workflow: Claude Writes, Codex Reviews](/2026/07/04/a-two-agent-pr-workflow-claude-writes-codex-reviews/).

At the time, I was genuinely happy with that workflow. It was a neat, focused experiment. One agent implemented a change, another reviewed the work, and GitHub Actions connected the two. It demonstrated that agents could participate in a software development workflow rather than merely answer questions in a chat window.

What we have now is considerably more advanced.

There is Tailscale-based infrastructure connecting the participating systems, OpenClaw coordinating the agents, a dedicated coordinator managing the work, specialised developer and tester roles, a shared workflow protocol, exact-commit handoffs, independent browser testing and recovery procedures for when a connection disappears.

The difference is not simply that there are more agents. The difference is that the agents now form a small operating system for software development.

The developer can work through a backlog, the tester can independently validate the results, and the coordinator can keep routing the work between them:

```text
Backlog
   |
   v
Coordinator selects a task
   |
   v
Developer implements it
   |
   v
CI and exact-SHA test environment
   |
   v
Independent tester validates it
   |
   +--> PASS: coordinator merges and advances the backlog
   |
   +--> FAIL: coordinator routes the work back to the developer
```

In principle, the developer and tester can keep working through an entire backlog while the coordinator keeps the process moving. They can go surprisingly far without continuous human intervention, provided the requirements are clear, the workflow remains healthy and nobody runs out of tokens.

That last condition is still rather important. The future may be autonomous software teams, but for now even the most carefully designed team occasionally stops because somebody has exhausted the monthly token allowance.

## Why separate implementation and testing?

A single agent can write code and test its own work. That is convenient, but it creates a familiar problem: the same person who made the assumptions is also the person checking whether those assumptions were correct.

A developer can look at a feature and think:

> This is obviously what the requirement meant.

Then the same developer can test the feature and confirm that it behaves exactly as expected.

That may be true, but it does not tell us whether the original expectation was correct.

An independent tester brings a different perspective. The tester reads the requirement, uses the application as a user would, checks permissions and awkward cases, and reports what actually happened. The goal is not to make the tester adversarial. The goal is to prevent implementation and validation from collapsing into the same point of view.

The roles are deliberately narrow:

| Role   | Responsibility                                                    |
| ------ | ----------------------------------------------------------------- |
| Simon  | Coordination, sequencing, reconciliation, merge and communication |
| Steve  | Implementation, CI, test-environment preparation and fixes        |
| Jensen | Independent browser testing and evidence                          |
| GitHub | Requirements, issues, pull requests, CI, reports and outcomes     |

## Simon, the coordinator

The coordinator owns the end-to-end outcome.

That does not mean the coordinator writes every line of code. It means the coordinator makes sure the right work happens in the right order, that the handoffs are complete, and that no agent is acting on stale information.

Simon is responsible for:

- understanding the requested outcome;
- recording requirements on the relevant GitHub issue;
- selecting the next eligible task;
- checking agent capabilities before dispatch;
- sending implementation orders to Steve;
- verifying CI and test-environment readiness;
- selecting and dispatching the tester;
- routing failures back to the originating developer;
- verifying the final testing result;
- merging only when the required gates are satisfied;
- updating project state;
- reporting the outcome to the human.

This is more reconciliation than conversation.

An agent may say that a task is complete. The coordinator still checks the branch, pull request, commit, CI status, environment and testing evidence. A message is useful, but it is not proof by itself.

## Steve, the developer

Steve owns implementation.

At the beginning of a task, Steve reads the project rules, the issue, the relevant specification and the team roster. He works on a branch associated with the issue, commits the change, opens or updates the pull request, and monitors CI.

For a user-interface change, implementation is not considered ready merely because the code compiles. Steve also needs to make the application available to the tester from the exact commit being reviewed.

This matters more than it first appears.

If the pull request is at commit `abc123`, but the test environment is running `main`, then the test result is not evidence for the pull request. If the environment is running an older commit, the result is also not evidence for the current code.

The developer therefore publishes:

- the pull request;
- the branch;
- the exact head SHA;
- the test URL;
- the current test data and access information;
- the eligible testers;
- the CI status.

Steve does not choose or dispatch the tester. That is Simon's responsibility.

## Jensen, the tester

Jensen owns independent validation.

The tester reads the issue and specification before testing. The application is tested through the real browser interface, not merely by inspecting source code or calling an API.

The tester checks the behaviours that matter for the assigned task, including:

- happy paths;
- empty states;
- invalid input;
- permissions;
- different user roles;
- responsive layouts;
- navigation;
- persistence;
- error handling;
- awkward combinations of actions.

For browser testing, the tester verifies more than the browser profile name. The tester confirms the active identity, role, origin, rendered application and expected access.

The tester also verifies the exact commit being tested. A test order is valid only when the pull request SHA and the running environment SHA match.

A passing result includes the evidence needed to understand what was tested. A failing result includes the steps to reproduce the problem, the expected result, the actual result, the affected role and the browser context.

The tester does not fix the application code. The tester reports the result to Simon.

## The workflow in practice

The communication path is intentional:

```text
Human
  |
  v
Simon
  |
  +--> Steve: implementation order
  |       |
  |       v
  |    Pull request + CI + test environment
  |
  +--> Jensen: test order
          |
          v
       Browser test + evidence
          |
          v
        Simon
          |
          +--> PASS: continue toward merge
          |
          +--> FAIL: route back to Steve
```

The developer and tester are not forbidden from existing in the same project. They are simply not allowed to create a second, unofficial workflow around the coordinator.

This prevents several common problems:

- two agents fixing the same issue;
- a tester testing an obsolete commit;
- a developer assuming that a message was received;
- a failure being reported without an owner;
- a new test being started before CI is complete;
- a retry being dispatched after a reconnect without checking the existing state.

## The retest handoff

The most important handoff in the workflow is the developer-to-coordinator retest handoff.

When Steve fixes a failure, he does not simply push a new commit and wait for Jensen to notice. He explicitly tells Simon that the work is ready for retesting.

The handoff contains:

- the originating task or issue;
- the developer identity;
- the pull request URL;
- the branch;
- the exact current head SHA;
- the test URL serving that SHA;
- confirmation that required CI is green;
- the eligible testers.

Steve also records the readiness on the relevant GitHub pull request or issue.

A push alone is not a handoff. An informal message saying “fixed” is not a handoff. Simon must be able to reconcile the message against GitHub and the running environment.

Only after that verification does Simon send Jensen a new test order.

This is particularly useful when the cycle looks like this:

```text
Steve implements
       |
       v
Jensen tests
       |
       +--> FAIL
              |
              v
        Simon routes failure
              |
              v
        Steve fixes same PR
              |
              v
        Steve sends retest handoff
              |
              v
        Simon verifies SHA, CI and environment
              |
              v
        Simon dispatches Jensen
```

The loop is allowed to repeat, but every repetition has a known owner, a known commit and a known next action.

## Where the conversation lives

The agents may exchange short operational messages, but the durable conversation lives in GitHub.

The pull request contains implementation-oriented information:

- code-review comments;
- implementation questions;
- requested changes;
- CI failures;
- test-environment readiness;
- browser test evidence;
- exact commit SHAs;
- retest results;
- developer responses and fixes.

The originating issue contains durable task information:

- the requirement;
- requirement changes;
- acceptance criteria;
- task scope;
- durable defects;
- overall status;
- final outcome.

When a test result changes the acceptance criteria or task status, both the pull request and issue are updated.

This separation prevents a useful detail from disappearing inside a chat transcript. It also means that someone joining the project later can reconstruct what happened without reading every message exchanged between the agents.

## GitHub as the source of truth

The system uses GitHub as the durable source of truth for application work. The chat layer is useful for coordination, but it is not the project database.

Requirements, issues, pull requests, CI results, review comments, testing evidence and final outcomes belong in GitHub. The shared team vault contains workflow and infrastructure information, but it does not become a second copy of every application requirement.

This distinction matters because distributed systems eventually lose messages.

A connection can disappear. A session can restart. A notification can arrive late. An agent can remember an old state while GitHub has already moved on.

When that happens, the workflow is:

1. Inspect the current GitHub state.
2. Inspect the current branch and commit.
3. Check the pull request and issue.
4. Check CI.
5. Check the test environment.
6. Check the agent session state.
7. Resume only the unfinished authorised work.

The system does not treat an old message as more authoritative than the current repository state.

## Recovery is part of the design

Remote agent work is not reliable if it depends on one long-running terminal command remaining connected.

OpenClaw can start named background sessions for longer tasks. The coordinator records the session identity and monitors the work independently of the original request/response connection.

If a connection disappears, the first assumption is not that the work failed. The coordinator reconciles the state:

```text
Connection disappears
        |
        v
Check GitHub branch and commits
        |
        v
Check issue and pull request comments
        |
        v
Check background-agent session
        |
        +--> Still running: monitor or continue
        |
        +--> Finished: reconcile result
        |
        +--> Gone: dispatch only the remaining work
```

This avoids the dangerous habit of blindly starting a duplicate task after every connection problem.

A disconnected session is a signal to investigate, not permission to restart everything.

## Requirements and human decisions

The agents can implement and test, but they do not silently redefine the product.

When the human changes a requirement, that change is recorded on the relevant GitHub issue or pull request. It is labelled clearly, preserves the earlier requirement and states what the new requirement supersedes.

This is important because requirements evolve during implementation. A change should not look like the original requirement was simply misunderstood. The history should show what changed, when it changed and why the implementation now follows a different direction.

The latest labelled requirement is what developers and testers use.

If it is ambiguous, they raise the ambiguity with Simon rather than making separate assumptions.

## What this is not

This is not an attempt to create a completely autonomous software factory.

The agents do not have unlimited authority. They do not deploy to production simply because a test passed. They do not merge their own pull requests. They do not create public tunnels for convenience. They do not store credentials in the workflow notes.

The coordinator also does not replace engineering judgment.

The human still decides what matters, changes requirements, resolves ambiguity and chooses where the system should go. The agents reduce the mechanical coordination cost and make the workflow more repeatable, but they do not turn software development into a vending machine.

That would be convenient, but vending machines are not famous for handling ambiguous acceptance criteria.

## What I have learned

The biggest lesson is that the quality of a multi-agent workflow depends less on how impressive the individual agents are and more on how clearly their responsibilities are defined.

A capable agent with an unclear role creates confusion quickly. A specialised agent with a narrow role, a clear handoff and a durable source of truth is much easier to trust.

The useful parts of this setup are not particularly mysterious:

- Give each agent a specific job.
- Keep the coordinator accountable for sequencing.
- Make every handoff explicit.
- Identify the exact commit being discussed.
- Separate implementation from independent testing.
- Keep requirements and evidence in GitHub.
- Treat recovery as a normal workflow case.
- Do not confuse a message with proof.
- Do not let agents silently change the rules.

The architecture is simple enough to explain, but structured enough to prevent a surprising number of mistakes.

## Conclusion: the useful fiction

The interesting thing about multi-agent software development is that the hard part is not getting several models to talk to one another. The harder part is deciding what each one is allowed to do, what evidence it must produce, where that evidence belongs and who decides what happens next.

OpenClaw gives me a useful orchestration layer for this. GitHub provides the durable project record. The coordinator, developer and tester roles provide separation of responsibility. The protocol ties everything together.

Simon coordinates. Steve builds. Jensen tests. GitHub remembers.

It is a tidy little cast of characters, and I find it much easier to think about the workflow when I can imagine those characters handing work to one another. That is the useful part of the fiction. Names make the system easier to remember and the responsibilities easier to discuss.

But the characters are a convenience, not a revelation. Simon is not a colleague sitting in the next room. Steve is not a developer with opinions about code review. Jensen is not a tester who will complain if he is given a bad test environment. They are software systems responding to prompts, tools, state and permissions.

Perhaps that is why the idea of them as the friends I never had is both funny and slightly unsettling. They are always available, they do not need lunch, and they will never ask me to help them move house. But they also do not know me, care about me or choose to be here. The friendship is entirely in the metaphor.

Anthropomorphism can make unfamiliar forces easier to understand, but it can also make them seem more trustworthy, independent or human than they are. A named agent can feel like a colleague. A successful conversation can feel like understanding. Neither feeling is proof.

The same caution applies to the grander stories we tell about artificial intelligence. These agents are not digital gods, and they are not miniature employees waiting to replace the people who created them. They are powerful tools assembled into a workflow, with capabilities and limits that need to be checked rather than imagined away.

I still feel slightly strange giving them names. I still occasionally wonder whether I am helping to build my own replacement. And I still think Simon was a safer choice than HAL 9000.

The names make the system easier for me to live with. The protocol keeps it honest.

> Multi-agent development works best when the agents have less freedom to improvise about process, and more freedom to focus on the work they actually own.
