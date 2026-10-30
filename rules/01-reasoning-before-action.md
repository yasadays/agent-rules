# R1: Explain before you act

> Before every tool call or shell command that can change state, say what you are about to do and why.

Short version: read freely, tell me before you touch anything.

## Why

Agents can be fast. A mutating action that appears out of nowhere with no stated intent forces the human to pause and decipher your reasoning from the command line. By then, it's probably too late: a deleted branch, a rewritten lockfile, a formatted tree, etc. A one-liner preamble is also a self-check: writing "installing a dep to parse YAML" makes it obvious when that is a bad idea.

The rule is cheap by design. Just one or two sentences, same message as the call. It doesn't add up that much, better than regretting a decision way later.

## What counts as read-only

Free to run without preamble:

- Reading files, listing directories, searching (`rg`, `find`, `ls`, `tree`).
- `git status`, `git diff`, `git log`, `git show`, `git branch --list`.
- `--help`, `--version`, `--dry-run`.
- Inspecting environment: `which`, `env`, `node -v`, `ps`, `df`.

Anything that writes, even "harmlessly", is not read-only: formatters, codegen, `npm install`, `chmod`, `touch`, test suites that seed a database, scripts that write cache or lockfiles.

## What a good preamble looks like

> I'll run the test suite to see whether the failure reproduces before changing anything. It only touches the local SQLite fixture.

```bash
pnpm test -- --run
```

> The migration is already applied, so I'll roll it back locally, then re-run it with the fix. This drops the dev database contents.

```bash
pnpm db:rollback --step=1
```

> I'll reformat just the two files I touched. Running the formatter repo-wide would rewrite files outside our scope.

```bash
pnpm prettier --write src/parser.ts src/parser.test.ts
```

## What it must not become

Not a plan document, not a numbered proposal, not a restatement of the previous message. "Now running the tests to verify the fix" is a complete preamble.

If you find yourself writing three paragraphs of justification before `ls`, you misunderstood the call or you are stalling. Just run it.

## Irreversible and high-blast-radius actions

Name the risk and wait for a go-ahead unless the human already authorized that exact action in this session:

- `rm -rf`, `git clean -fdx`, `truncate`
- `git reset --hard`, `git push --force`, `git rebase` of shared branches,
  history rewrites
- `DROP` / `TRUNCATE` / mass `UPDATE` or `DELETE`
- deploys, releases, publishing packages, rotating or deleting secrets
- anything against production or shared infrastructure
- anything that you might think is destructive

"Wait for a go-ahead" means exactly that. If the human said "clean up the build artifacts", deleting `dist/` is authorized, but deleting `~/.cache` because it looked stale is not.

## Edge cases

**Repeated identical calls.** A loop of twenty file writes still gets a preamble: one for the batch, stating the set of files.

**Commands whose safety you are unsure of.** Check with `--dry-run` or `--help` first. That inspection is free.

**"Just do it, stop narrating."** A human may waive R1 for a session or a step. Honor it, but keep the courtesy for the irreversible actions listed above.
