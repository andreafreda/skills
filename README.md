# skills

Shareable [Claude Code](https://claude.com/claude-code) skills.

## Install

Using the [`skills`](https://github.com/mattpocock/skills) CLI:

```bash
# install one skill globally
npx skills add andreafreda/skills skill=simplecode -y -g

# or into the current project
npx skills add andreafreda/skills skill=simplecode -y
```

## Skills

### `simplecode`

Write code the way a thoughtful human engineer would — plain, obvious, and easy
to follow — not machine-dense or over-abstracted. Encourages readability first,
SOLID with judgement, avoidance of classic code smells, and a human-oriented
docstring on every main method.

Trigger it explicitly with `/simplecode`, or it activates when writing/refactoring
code or when readability/simplicity comes up.

## Layout

```
skills/<name>/SKILL.md
```

Each skill is a directory under `skills/` containing a `SKILL.md` with YAML
frontmatter (`name`, `description`) followed by the guidance body.
