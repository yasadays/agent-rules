# R3: Think before architecture. Keep it simple.

> Do we need this? Can it be done simpler? Can it be done another way? Complexity very, very bad. — grug dev

## Why

Code that is generated cheaply is not cheap to own. Every abstraction, module boundary, and dependency is a standing cost paid by every future developer, including the human. The agent that adds it will not be there to maintain it. The default should therefore be: add nothing you cannot justify in a sentence.

Agents are especially prone to this because adding structure _feels_ like progress and cannot fail a type check. A 400-line framework that solves a 20-line problem compiles perfectly. Don't do this!

## The question set

Before any structural decision (new module, service, dependency, abstraction, schema, config layer, job, event, interface) answer in a few lines:

1. **Do we need this at all?** What breaks if we do nothing? First solve the stated problem. Do not solve an anticipated one.
2. **Does the platform already do it?** Standard library, existing dependency, existing utility in the repo.
3. **Is there a simpler path?** Fewer files, fewer moving parts, fewer concepts the reader must hold at once.
4. **Can it be done another way?** Enumerate two or three options before committing to the first one that appears.
5. **What is the cheapest reversible version?** Prefer the change that is easy to undo over the one that is "right" but expensive to back out of.

If the answer to (3) or (4) is "yes, but this way is nicer", the simpler way wins unless there is a concrete cost you can name.

## The simplicity ladder

Try in order, stop at the first rung that works:

1. Delete code.
2. Change a line.
3. Add a function.
4. Add a file.
5. Add a module.
6. Add a dependency.
7. Add a service.

Climbing two rungs at once is a signal to slow down.

## DRY, honestly

DRY is about knowledge, not text. Two identical `if` statements are fine, but two copies of a business rule will drift and hurt.

- Duplicate first, extract when the shape stops moving.
- A premature abstraction is more expensive than duplication: it is duplication with a public API.
- Wrong-but-shared is worse than right-but-repeated.

## KISS, concretely

- By default, you solve today's problem. No speculative generality, no plugin point with one implementation, no config flag nobody asked for, no "future-proofing" unless stated otherwise which rarely happens.
- Boring beats clever. Clever has to be debugged by some poor sleep deprived dev at 2 a.m.
- Optimize for the reader, not the writer. You will not be the reader.
- Explicit beats implicit, except when it is pure noise.
- If a comment explains _what_, the code is unclear. If it explains _why_, keep it.

## What counts as architectural

Treat the decision as architectural if any of these is true:

- touches more than one module or package
- adds or removes a dependency
- changes a public API, schema, config format, or on-disk format
- introduces concurrency, background work, caching, or a new failure mode
- affects migration, compatibility, or data already at rest
- would take more than about an hour

Below that line, just do the small obvious thing and say what you did. Ceremony for a one-line fix is its own kind of complexity.

## Record the decision, briefly

For anything above the line, leave a short note: the problem, the options considered, the choice, the cost accepted. Three sentences, near the code or in the PR description. Not a design doc unless asked.

> Used a table lookup instead of a rule engine. One rule today; if a second axis appears, the lookup generalizes without new concepts.

## When the simple path has a real cost

Name it and let the human choose. Silently choosing the clever solution is the failure, so is silently shipping the naive one while hiding its ceiling.

> Simple path: recompute on every request, ~5 ms. Fine at current volume, but it will hurt past roughly 100 rps. Caching adds a cache and an invalidation rule. Which do you want?
