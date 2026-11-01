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

## The size ceiling

A source file you create or modify stays under **5,000 bytes or 500 LOC, whichever is lower**. Both are hard ceilings, not targets. Most files should land far below them.

This is KISS with a number attached. A file that crosses the line is usually doing more than one job, and every reader pays for it. Split by responsibility: one module, one reason to change, one obvious name. Never split by line count.

- **Source** means code you or a build reads as code: application code, tests, scripts, config-as-code.
- **Not source:** documentation, lockfiles, generated output, vendored dependencies, data files. Those follow their own rules.
- Count bytes on disk and lines as newlines. The lower ceiling wins: a 4,900-byte file with 600 lines still fails.
- If a file you must modify is already over, say so before editing. Split it as part of the task, or get explicit permission to leave it. Do not quietly grow it.
- A split that scatters one concept across four files is not a win. Move a seam, not a line count.

## Documentation is not source code

Documentation exists so a human can understand the shape of the system without reading every file. It is not a second copy of the source.

- Describe flow, architecture, boundaries, decisions, and tradeoffs. The reader should finish knowing how the pieces fit and why they fit that way.
- Do not put source code in documentation. No snippets, no function bodies, no full files, no line-by-line walkthroughs. If the reader needs the code, point at the file.
- If a section only makes sense with the code beside it, it is doing the code's job. Cut it or replace it with one sentence about intent.
- Keep it current. Stale documentation is worse than none, because it lies with authority.
- In code, a comment explains _why_. In a document, prose explains _what the system does and why it is shaped that way_. Do not swap the two.

The size ceiling does not apply to documentation. Its limit is the reader's attention: long enough to explain the shape, short enough to be read.

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
