---
name: build-in
description: Implement code from a type-first perspective. Use for /build-in or when 'implement this' or 'design this' introduce or reshape an abstraction.
---

# Build In

Implement the code as types and interfaces with `not implemented` bodies, then let sub-agent critique and tests drive the implementation.

## Start

Use `TodoWrite`, one item per phase.

1. Understand the problem
2. Type the problem
3. Agree
4. Implement

## Phase 1: Understand the problem

Explore the codebase so you understand the problem and why the change needs to be implemented. Fully explore any area of code related to the problem so you can build a full mental model of: how the code works, how affected areas of the codebase interact, and the limitations of the current implementation.

## Phase 2: Type the problem

Write only types and interfaces, or functions and methods with `not implemented` bodies. When modifying existing code, leave the existing logic in place and put the `not implemented` stub after it.

- Typescript `throw new Error('not implemented');`
- Rust `todo!()`
- Go `panic("not implemented")`

Find the project's linting and typechecking commands and execute them to validate the type-first approach. If you cannot find the commands, ask the human for them.

You are looking to polish the interaction between the code focusing on optimising for readability and testability. Keep polishing, code is cheap and malleable. Once you have a great 'typed' out solution, spin up 3 `general-purpose` sub-agents and seek direct, terse critical feedback. You should vary the lenses of the sub-agents: one API surface & naming, one on testability and one hostile ("what is going to break in 6 months time"). Request a review with a focus on what is weak about your typed implementation, also include:

- summary of the problem to be solved
- an overview of the approach taken
- the code changes

Screen the feedback and look to:

- reduce complexity
- prevent domain leakage
- prevent long function or method chains
- hide complexity behind smaller public interfaces

Then perform the [Throw away](#conditional-check-throw-away) check.

## Phase 3: Agree

At this point it is a hard stop! Write the design rationale including ideas you rejected to the human. Then use `AskUserQuestion` to check the human agrees with the approach and the file changes. No `not implemented` body may be filled until the human consents!
If the human does not agree with the approach, follow [Recover](#recover).

Otherwise, branch if on the default branch. Create a commit of the typed skeleton only with a commit message that provides an overview of the rationale.

## Phase 4: Implement

For each `not implemented` body of code:

- write tests that outline its behaviour
- run tests to ensure they run but all fail on the `not implemented` stub. You need to confirm compilation is not the cause of failure.
- implement business logic to pass a single test written in the previous "red step". Any changes to the tests must be surfaced to the human.
- repeat until all tests pass

Finally run all tests to conclude to ensure no regressions have been introduced.

Then perform the [Throw away](#conditional-check-throw-away) check.

If the conditional check did not trigger a revert, provide a brief change summary to the human. You are finished!

## Conditional Check: Throw away

If the typed out problem or the implementation:

- introduces developer friction
- requires workarounds
- requires callers to have an internal knowledge of abstraction implementations
- experiences an explosion of branching logic

then you should suggest reverting the typing or implementation with consent from the human. Not all is lost, you will have improved understanding of the problem that you should distill into writing. Then follow [Recover](#recover).

## Recover

Work out with the human which constraint was missed or misunderstood, and which assumption was wrong, before restarting at phase 2.
