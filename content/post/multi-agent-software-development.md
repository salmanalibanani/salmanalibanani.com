---
title: "Multi-Agent Software Development using OpenClaw"
date: 2026-11-18T06:45:00+11:00
draft: false
summary: "How I use OpenClaw to coordinate specialised AI agents for software development, testing, handoffs and recovery."
tags:
  ["AI", "OpenClaw", "software-development", "agents", "GitHub", "automation"]
---

There is a particular moment in software development when a project becomes large enough that one person can no longer keep the whole thing in their head.

The requirements are in one place, the implementation is somewhere else, the test environment is running a different commit, somebody has reported a failure, and a pull request is waiting for a review. At that point, adding more chat messages rarely improves the situation. What is needed is a system for coordinating the work.

Recently, I have been building a multi-agent software development workflow using OpenClaw.

The idea is not to create one very clever agent that does everything. The idea is to create several specialised agents, give each of them a narrow responsibility, and use an explicit protocol to coordinate them.

One agent coordinates the work. One implements the changes. Another independently tests them. GitHub remains the source of truth, while OpenClaw provides the communication and orchestration layer.

It is a small software team, except that nobody takes lunch breaks and one of them occasionally needs to be reminded that a successful `git push` is not the same thing as a successful test.

## The basic structure

The system currently has three roles:

1. **Simon**, the coordinator agent.
2. **Steve**, the developer agent.
3. **Jensen**, the independent tester agent.

Simon is the point of contact for the human. The coordinator agent is running on an old Mac Mini, which feels appropriate. It is not a particularly glamorous piece of infrastructure, but neither is a project manager's notebook, and both can be surprisingly useful if they remain available.

Steve and Jensen are specialised workers. Steve changes the application. Jensen tests the application through the user interface. Neither agent owns the whole process.

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

## From a small experiment to a real development system

This setup is also a useful measure of how quickly things have changed for me.

On 4 July 2026, I wrote about a small experiment where one AI agent wrote code and another agent reviewed its pull request: [A Two-Agent PR Workflow: Claude Writes, Codex Reviews](/2026/07/04/a-two-agent-pr-workflow-claude-writes-codex-reviews/).

At the time, I was genuinely happy with that workflow. It was a neat, focused experiment. One agent implemented a change, another reviewed the work, and GitHub Actions connected the two. It demonstrated that agents could participate in a software development workflow rather than merely answer questions in a chat window.

What we have now is considerably more advanced.

There is a Tailscale-based infrastructure connecting the participating systems, OpenClaw coordinating the agents, a dedicated coordinator agent managing the work, specialised developer and tester roles, a shared workflow protocol, exact-commit handoffs, independent browser testing and recovery procedures for when a connection disappears.

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

## Why use separate agents?

A single agent can write code and test its own work. That is convenient, but it creates a familiar problem: the same person who made the assumptions is also the person checking whether those assumptions were correct.

A developer can look at a feature and think:

> This is obviously what the requirement meant.

Then the same developer can test the feature and confirm that it behaves exactly as they expected.

That may be true, but it does not tell us whether the original expectation was correct.

An independent tester brings a different perspective. The tester reads the requirement, uses the application as a user would, checks permissions and awkward cases, and reports what actually happened. The goal is not to make the tester adversarial. The goal is to prevent the implementation and the validation from collapsing into the same point of view.

The roles are deliberately narrow:

| Role   | Responsibility                                                    |
| ------ | ----------------------------------------------------------------- |
| Simon  | Coordination, sequencing, reconciliation, merge and communication |
| Steve  | Implementation, CI, test-environment preparation and fixes        |
| Jensen | Independent browser testing and evidence                          |
| GitHub | Requirements, issues, pull requests, CI, reports and outcomes     |

The agents may be capable of doing more than their assigned role, but capability is not the same as permission.

## The coordinator

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

## The developer

Steve owns implementation.

At the beginning of a task, Steve reads the project rules, the issue, the relevant specification and the team roster. He works on a branch associated with the issue, commits the change, opens or updates the pull request, and monitors CI.

For a user-interface change, implementation is not considered ready merely because the code compiles. Steve also needs to make the application available to the tester from the exact commit that is being reviewed.

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

## The tester

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

## The communication boundaries

The agents communicate, but they do not communicate arbitrarily.

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

## The developer-to-coordinator retest handoff

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

A push alone is not a handoff. An informal message saying "fixed" is not a handoff. Simon must be able to reconcile the message against GitHub and the running environment.

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

## The protocol

The agents follow a shared protocol stored in the team's workspace.

The protocol covers:

- what agents read at startup;
- how they recover after a reconnect;
- where tasks come from;
- how ownership works;
- how handoffs are formed;
- how exact commit SHAs are used;
- how testing is dispatched;
- where results are recorded;
- how failures are routed;
- who is allowed to merge;
- what happens when a message is delayed or missing.

The agents also have role-specific instructions. The protocol contains the shared rules, while the developer and tester role notes describe their particular responsibilities.

The protocol is deliberately boring.

That is a compliment.

A good protocol should remove ambiguity rather than create interesting conversations. It should answer questions such as:

- Who owns this stage?
- What evidence is required?
- Which commit does this result apply to?
- What happens if the connection drops?
- Is this task really complete?
- Where should this feedback be recorded?
- Who is allowed to send the next instruction?

## GitHub as the source of truth

The system uses GitHub as the durable source of truth for application work.

The chat layer is useful for coordination, but it is not the project database.

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

## What I have learned so far

The biggest lesson is that the quality of a multi-agent workflow depends less on how impressive the individual agents are and more on how clearly their responsibilities are defined.

A capable agent with an unclear role creates confusion quickly.

A specialised agent with a narrow role, a clear handoff and a durable source of truth is much easier to trust.

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

## Conclusion

The interesting thing about multi-agent software development is that the hard part is not getting several models to talk to one another.

The hard part is deciding what each one is allowed to do, what evidence it must produce, where that evidence belongs and who decides what happens next.

OpenClaw gives me a useful orchestration layer for this. GitHub provides the durable project record. The coordinator, developer and tester roles provide separation of responsibility. The protocol ties everything together.

Simon coordinates. Steve builds. Jensen tests. GitHub remembers.

That division of labour is more valuable than asking one agent to pretend it is an entire software team.

It is still early, and the workflow will continue to evolve. There are more roles that could be added, better dashboards that could be built, and plenty of opportunities to discover that an apparently sensible process has a hole in it.

But the basic principle seems sound:

> Multi-agent development works best when the agents have less freedom to improvise about process, and more freedom to focus on the work they actually own.
