---
title: "A Two-Agent PR Workflow: Claude Writes, Codex Reviews"
date: 2026-07-04T13:45:06+10:00
draft: false
summary: "A practical workflow where Claude implements a change and Codex reviews the pull request automatically in GitHub Actions."
tags: ["ai", "github-actions", "code-review", "automation", "engineering"]
---

Imagine two developers working together, one reviewing the other person's pull request. Sometimes this goes very well. They agree on most things, resolve a few comments, and the code gets merged. But sometimes it goes in a different direction. It starts with "I think this test is missing", and twenty minutes later both people are questioning each other's priorities in life, and perhaps even the purpose of existence, or lack thereof.

When agents work together, things are a bit different, even if the agents are made by two different companies. What I find useful in this setup is the narrow scope given to each agent and the explicit handoff between them. Claude writes the code. Codex reviews the pull request. GitHub Actions runs the review automatically. That is the workflow I have been using, and this post is about how it works.

## The workflow

The operating model is very simple:

1. Claude picks up a GitHub issue.
2. Claude implements the change on a branch.
3. Claude pushes the branch and opens a pull request.
4. GitHub Actions triggers a Codex review automatically.
5. Codex posts a review summary and inline comments on the pull request.
6. Claude makes one fix pass based on that feedback.
7. Claude pushes the fixes to the same branch.
8. Claude merges the pull request, closes the issue, and deletes the branch.

One thing here is deliberate. I do not want an endless review loop where one agent reviews, the other fixes, then the first reviews again, and this just keeps going. I want one review pass from Codex, one fix pass from Claude, and then merge. That keeps the whole thing simple and predictable.

## Why I like this setup

I did not want one of those vague "AI code review" setups where some model says generic things about a pull request and nobody really knows what to do with that output. I wanted a review step that is automatic when the PR is opened or updated, visible in GitHub itself, precise enough to refer to exact changed lines, focused on bugs, regressions, requirement mismatches, risks, and missing tests, and also separate from merge or release behaviour. That last point matters because opening a pull request should trigger review only. I don't want the review workflow merging code, approving the PR, or doing anything else of that nature. It should review the pull request and leave the merge decisions to something else.

## What the GitHub Action actually does

The whole thing lives inside GitHub. GitHub Actions listens to `pull_request` events like `opened`, `reopened`, and `synchronize`, it uses `GITHUB_TOKEN` to read PR details and post the review back, and it uses `OPENAI_API_KEY`, stored as a GitHub secret, to call Codex. There is no separate bot server, and no extra orchestration layer.

When the workflow runs, it collects the pull request title, the pull request description, the changed files, and the diff for each changed file. That diff is then annotated with line numbers from the new file, so findings can be mapped back to exact lines in the PR. Without that step, the feedback is much less useful.

## What Codex is asked to look for

The review prompt is intentionally narrow. I want Codex spending its time on engineering problems like bugs, regressions, requirement mismatches, logic issues, edge case risks, and missing or weak tests where they matter, instead of style preferences, formatting, or random cleanup ideas. This matters a lot because if you ask a model to comment on everything, it absolutely will.

## What gets posted back to the PR

Codex returns a structured result, and the workflow turns that into a GitHub review with a summary comment and inline comments for findings that can be attached to exact lines. If a finding is valid but cannot be safely attached to a changed line, it goes into the summary comment instead. I think this is important because not all useful review comments are line-specific, and I also don't want the automation pretending that a comment belongs to some line when it really doesn't.

Also, the review is posted as a comment review. It does not auto-approve the PR. The goal here is to provide engineering feedback. Fake branch protection is not something I am interested in building.

## The `reviewed_by_codex` label

After a successful review, the workflow applies a `reviewed_by_codex` label to the pull request. I want that label to have a strict meaning: Codex completed a real review successfully. It should not be applied just because the workflow happened to run. That distinction is important because failures happen in different ways. The API key may be missing or invalid, the account may be out of quota, the request may be rate limited, the configured model may be wrong, or the OpenAI call may fail for some other reason.

If that happens, the workflow should still comment on the PR and explain what went wrong, but it should not apply the label. Otherwise the label becomes meaningless very fast.

## Error handling matters more than people think

Saying "review failed" is not good enough. Different failures need different action. An invalid API key means repository secrets need to be fixed, insufficient quota means billing or credits need attention, rate limiting is often temporary and may just need a re-run, and a missing model usually means the configuration is wrong. If the automation is part of your engineering workflow, then even the failure path should be useful. So the workflow should parse the OpenAI error and post something concrete on the pull request, instead of just saying "something went wrong".

## How Claude uses the feedback

The review is not the end of the process. It is an input into Claude's second pass. After Codex comments on the pull request, Claude reads the feedback and makes one fix pass. Again, I want to emphasize the "one pass" part here because I do not want the default behaviour to become an endless back and forth loop.

If Claude decides not to implement one of the findings, then that should be documented in a PR comment with a reason. A skipped finding should not disappear silently. It may be out of scope, it may be based on a wrong reading of the diff, or it may conflict with a deliberate product decision. Whatever the case is, it should be visible.

## Why this works better than generic "use AI for coding"

What makes this useful for me is the separation of responsibilities. Claude is used for implementing the issue, changing the code, opening the pull request, applying the fixes, and merging and cleaning up. Codex is used for reviewing the PR diff, identifying engineering risks, posting summary and inline comments, and marking the PR as reviewed when the review actually completed. This keeps Claude away from grading its own work while writing it, and it keeps Codex away from merge control. Each one has a small, clear job.

## Scope limits

This workflow is intentionally not trying to do everything. It does not run the full repository test suite as part of the review trigger, auto-merge the PR, approve the PR automatically, create repeated review and fix cycles, or depend on tooling outside GitHub and the model API. I made those choices deliberately. The smaller and clearer the boundary is, the easier this workflow is to trust.

## Conclusion

So yes, if you imagine two human developers reviewing each other's code, there is always a chance that the discussion goes from a missing test to an argument about engineering values, career choices, or the meaning of life. This workflow avoids most of that simply because the roles are constrained. Claude writes. Codex reviews. GitHub Actions enforces the handoff. Even when the agents come from two different companies, what makes this useful is the narrow scope of responsibility. Each one has a specific job, and neither of them gets to turn the pull request into an existential crisis.

The funny thing is that I was actually too early to this whole AI party, as I did my masters in AI back in 2003. Yeah, over two decades ago. And now somehow I feel late to the party, because some people are doing amazing stuff with AI while others are losing jobs very fast. This workflow is by no means perfect. In fact, most of it was built by asking AI how and what I should do to improve my development workflow. Strange times indeed. These days you ask AI to help you use AI better.

So the purpose of this post is simply to share an idea and open it up for suggestions and recommendations. I am quite sure there are better ways to do this, and hopefully the Internet will respond and teach me something useful.
