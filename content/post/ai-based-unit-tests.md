---
title: "Don't be ashmed of creating unit tests with ChatGPT"
date: 2024-05-21T22:24:20+10:00
draft: false
---

"Your tests should be based on the original requirements specified for the system", they say.

"The main goal of unit testing is to ensure that each unit of the software performs as intended and meets requirements", they say.

And there is some truth to all this. But often we forget about a crucial aspect of this whole process - the human element!

## The myth of meeting all requirements

The reality is that both the code written by developers and the tests (unit or otherwise) are inherently based on the developers' subjective understanding of system requirements. Each developer brings their own perspective, experiences, and interpretations to the table, which can result in varying implementations and tests that may not fully align with the original intent of the requirements. This subjectivity can lead to gaps between what the requirements actually stipulate and what the tests validate.

Then there is the question of how complete the specifications are. At any point in time - specially during development - the system may not be fully specified, and some specifications may not have been implemented. Many teams are under pressure to get the system out of the door and they may leave out features with the intention that those will be implemented in due time. Sometimes, for scenarios that are not specified but have to be dealt with in the code due to whatever reason, developers will make reasonable assumptions. This is more common than you would think.

And then, of course there is almost always a real evolution in requirements. Repeat after me: requirements change!

Give all that, **the best you can hope for is that the tests ensure that the code does what the developer thought it must do.**

## Your code is (in)correct

The concept of software correctness is often idealized as the state where software precisely meets its specifications and requirements. However, this notion is problematic for several reasons. Specifications - as mentioned above - can be ambiguous or incomplete, and the context in which software operates can change. Moreover, correctness often overlooks the trade-offs between performance, security, and usability. Instead of striving for absolute correctness, aiming for robustness and resilience is a noble cause IMHO.

## AI is amazing - and cheap

The real value of AI generated tests is this: AI-generated unit tests can lock in the current implementation based on a developer's understanding, serving as a safeguard against future modifications that can potentially break stuff by other developers. These tests ensure that changes do not break existing functionality, providing a safety net that preserves the integrity of the code as it evolves.

"Don't test your implementation! Test against your original requirements", they say.

I say, there is value in both.

And given how cheap it is now with AI to generate tests that lock in your implementation, it's just a no brainer.
