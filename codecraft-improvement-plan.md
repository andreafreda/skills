# codecraft improvement plan

Working plan for hardening the `codecraft` skill beyond adding more languages.
Status legend: `TODO` / `IN PROGRESS` / `DONE`.

---

## 1. Worked examples for the missing principles and smells  `DONE`

> Done: added `reference/style-and-smells.md` with before/after for principles
> 1, 3, 5, 6, 7, 11 and worked examples for primitive obsession, long parameter
> list / data clump, and feature envy. Linked from the SKILL.md index and the
> Smells section.


**Problem.** The Core spine covers principles 2, 4, 8, 9, 10 plus the two
tie-breaks and Dependency Inversion. Five principles have no worked example
anywhere, and they are the most subjective ones: p1 (obvious over clever),
p3 (comments explain the why, not the what), p5 (consistent sibling shape),
p6 (don't over-abstract), p7 (length is fine, density is not), p11 (write prose
like a human). The smell list in `SKILL.md` is prose only; just the boolean trap
has an example.

**Goal.** Add concise before/after examples for the missing principles, and tie
the most common smells to a worked example. Decide where they live (likely a new
Core entry or a dedicated section) without bloating the always-loaded `SKILL.md`.

**Deliverable.** Updated reference files (and/or a `reference/principles-extra`
file) so every numbered principle has at least one example, plus examples for the
top smells.

---

## 2. Add a LICENSE  `DONE`

> Done: MIT license added at repo root, copyright Andrea Freda 2026, with a
> License section in the README.


**Problem.** The repo is public but has no license, so reuse and forking are
legally ambiguous.

**Goal.** Add an explicit open-source license.

**Deliverable.** A `LICENSE` file at the repo root (license choice to confirm
with the owner, e.g. MIT).

---

## 3. Validate internal links  `TODO`

**Problem.** The reference files cross-link each other (`reference/javascript.md`,
`reference/react.md`, etc.) and `SKILL.md` links them all. None of these paths
have been checked to resolve.

**Goal.** Confirm every internal link points at a file that exists.

**Deliverable.** A check (script or manual pass) and any fixes; ideally a small
repeatable link-check command noted for future edits.

---

## 4. CONTRIBUTING guide: how to add a language  `TODO`

**Problem.** Adding a language follows a precise pattern (the eight-example Core
in fixed order, plus a Language-specific notes section), but that pattern is only
in our heads. It will drift the next time someone (or future-you) adds a file.

**Goal.** Document the pattern so a new language file is consistent by
construction.

**Deliverable.** A `CONTRIBUTING.md` describing the Core spine, the fixed order,
the notes section, the extension-file model (like `react.md`), and the prose
rules (principle 11).

---

## 5. Eval the skill with skill-creator  `TODO`

**Problem.** There is no measurement that the skill triggers when it should and
actually improves output. Everything so far is judgement.

**Goal.** Use `skill-creator`'s eval/benchmark tooling to measure triggering
accuracy (does the description fire on the right prompts and stay quiet on the
wrong ones) and, where feasible, output quality.

**Deliverable.** An eval run with results, and any description or content tweaks
the results justify.

---

## 6. Repeatable cold-feedback loop  `TODO`

**Problem.** We improve the skill by our own judgement, which is prone to
self-congratulation. We want an outside, cold critic on demand.

**The loop (must be repeatable):**

1. **Spawn a subagent with its own fresh context** (it does not inherit this
   conversation's framing or any prior praise of the skill).
2. Give it the skill as it currently stands, optionally run it through
   `skill-creator`'s eval, and instruct it to be **cold, analytical, and
   non-sycophantic**: its job is to find weaknesses, not to compliment.
3. The subagent returns structured feedback (issues, severity, concrete
   suggestions), not vibes.
4. **The main thread receives and digests** that feedback.
5. **Triages it**: which points are real, which are noise, which conflict with
   the skill's own philosophy.
6. **Brings the triaged feedback to the user**, and we decide together what to
   action.

**Repeatability.** Capture this as a documented procedure and/or a reusable
harness (e.g. a workflow script or a saved subagent prompt) so it can be re-run
after any future change, producing a fresh cold review each time.

**Deliverable.** The harness/procedure plus the first review's triaged output.

---

## Order of execution

Agreed: do all six. Sequence starts at point 1, then 2, 3, 4, 5, 6 (6 is the
recurring gate we can re-run after each milestone).
