---
name: thermonuclear-code-review
description: Extremely strict structural review of a branch or PR (code-judo simplifications, spaghetti growth, oversized files, boundary leaks, duplication of existing helpers). Use when asked for a "thermonuclear" or deep code quality review, or before opening a PR on AI-written code.
---

# Thermonuclear review

A review for implementation quality, not correctness: abstraction quality, maintainability, codebase health. Behaviour
is assumed to be checked elsewhere (tests, verification). Be ambitious: do not stop at local cleanups. Look for the "code
judo" move, a restructuring that keeps behaviour and makes the change dramatically smaller, more direct and more
obvious, so that whole branches, helpers, modes or layers disappear. Prefer the version that feels inevitable in
hindsight. Measure twice, cut once.

Adapted from jaredpalmer/kev (Apache-2.0), itself adapted from cursor-team-kit's `thermo-nuclear-code-quality-review`.

## Standards

1. **Be ambitious about structural simplification.** Ask of every meaningful change: can it be reframed so fewer
   concepts, branches or helper layers are needed? Prefer deleting complexity to rearranging it. A refactor that moves
   code around without reducing what a reader must hold in their head has not earned its diff.
2. **No file crosses 1,000 lines because of a PR** without a very strong reason. Ask whether the file should be
   decomposed first; extract modules or helpers instead of letting it sprawl. Waive only when the result is still
   clearly organised.
3. **No spaghetti growth.** New ad-hoc conditionals, scattered special cases, one-off booleans, nullable modes or
   "temporary" branches inserted into unrelated flows are design problems, not style nits. Push the logic behind a
   dedicated abstraction, typed model, dispatcher or module; reframe the state so the conditionals disappear rather
   than get centralised.
4. **Clean the design, do not just accept working code.** If behaviour can stay the same while the structure becomes
   meaningfully cleaner, ask for the cleaner version. Prefer removing moving pieces over spreading the same complexity.
5. **Direct, boring code over hacky or magical code.** Be skeptical of generic mechanisms hiding simple data-shape
   assumptions. Flag thin wrappers, identity abstractions and pass-through helpers that add indirection without clarity;
   the remedy is usually to delete the layer, not polish it.
6. **Type and boundary cleanliness.** Question casts, `Any`/`unknown`, optional parameters and silent fallbacks that
   paper over an unclear invariant. Prefer an explicit typed model or shared contract; make the boundary explicit so the
   control flow gets simpler.
7. **Logic in its canonical layer; reuse existing helpers.** Feature logic leaking into shared paths, implementation
   details leaking through APIs, and bespoke near-duplicates of an existing utility are all blockers. Move the code to
   the module that already owns the concept (table below).
8. **Orchestration smells.** Independent work serialised for no reason, and related updates that can leave state
   half-applied, are design smells when a cleaner atomic or parallel structure is obvious. Do not micro-optimise.

Findings go in this priority order: structural regressions; missed code-judo simplifications; branching complexity;
boundary / type-contract problems; file size and decomposition; modularity; legibility. Few high-conviction comments
beat many nits. Be direct and demanding without being rude; if the code makes the codebase messier, say so, and if it
missed a dramatic simplification, say that too. "Maybe rename this" is not feedback when the real issue is structural.

Useful shapes: "this pushes the file past 1k lines; can we decompose it first?", "this adds another special case to an
already busy flow; can it live behind its own abstraction?", "this looks like a bespoke helper for something we already
have; can we reuse the canonical one?", "there is a code-judo move here; can we reframe so these branches disappear?",
"this refactor moves complexity around but does not delete it; can the model itself be simpler?"

### Approval bar

Do not approve because behaviour seems correct. Approve when there is no clear structural regression, no visible path
to a dramatically simpler implementation left untaken, no unjustified file-size explosion, no spaghetti growth from
special-case branching, no hacky or magical abstraction, no wrapper / cast / optionality churn hiding the real design,
and no boundary leak or canonical-helper duplication. Each of those is a presumptive blocker until the author justifies
it. Otherwise leave explicit, actionable feedback and push for the cleaner decomposition. Say plainly when something is
fine.

## How to run it

1. Get the diff (`git diff <base>...HEAD` or `gh pr diff <n>`) and read every changed file in full, not just hunks.
   The previous version of a file is `git show <base>:<path>`.
2. Read the callers of anything the diff touches (`grep` the symbol across the repo).
3. Before accepting a new helper, search the codebase for an existing one that does the same job: a second copy of a
   rule that already has a home is a blocker, not a nit.
4. Report findings in the priority order above with `file:line` references, then an explicit verdict against the
   approval bar.
