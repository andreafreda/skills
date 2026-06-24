# skills

A small collection of [Claude Code](https://claude.com/claude-code) skills I use
to make Claude write code I actually want to maintain.

---

## ✨ `codecraft`

**Make Claude write code like a thoughtful human, not a machine.**

Out of the box, AI tends to produce code that *works* but reads like it was
generated: dense one-liners, cryptic names, clever tricks, zero docstrings, or
the opposite — a tower of abstractions for a single use case. `codecraft` steers
Claude toward code a real engineer would be happy to inherit.

When it's active, Claude:

- 🧠 **Favors the obvious solution** over the clever one — no line you have to stop and decode.
- 🏷️ **Names things fully** — intention-revealing identifiers, no cryptic abbreviations.
- 💬 **Documents every main method** with a human-oriented docstring: *what it's for and when to reach for it*, not a restatement of the signature.
- 🧩 **Applies SOLID with judgement** — real seams, not ceremony (YAGNI wins ties).
- 🚫 **Avoids classic code smells** — long parameter lists, feature envy, primitive obsession, magic numbers, boolean traps, dead code…
- 📖 **Optimizes for the next reader** — linear flow, consistent shapes, comments that explain *why*.

> Readability first. Not shorter code — *clearer* code.

### Install

```bash
# globally (available in every project)
npx skills add andreafreda/skills skill=codecraft -y -g

# or just in the current project
npx skills add andreafreda/skills skill=codecraft -y
```

Then trigger it explicitly with `/codecraft`, or let it kick in automatically
when you're writing or refactoring code, or whenever readability and simplicity
come up.

---

## Layout

```
skills/<name>/SKILL.md
```

Each skill is a directory under `skills/` with a `SKILL.md`: YAML frontmatter
(`name`, `description`) followed by the guidance body.
