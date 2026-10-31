---
type: topic
updated: 2026-10-31
---

# Skill writing guidelines

- Before writing, ask the user which use cases matter and what setup the skill needs, such as a script location or env
  vars. If you can't ask, stop and return the questions instead of guessing.
- Skill name and description matters a lot. Keep both obvious so that it is intuitive when to use it. The description
  starts with "Use when…" and states when to reach for the skill and nothing more.
- Keep a general skill general. Project-specific facts, like a repo's checkout paths, belong in that project's own
  context files, not in a skill every project loads.
- A skill covers how to do something, not whether to. The decision to use it is made outside the skill, so leave out
  policies like "use this by default".
- Have a very short summary at the start, even if its a single line, giving context the description doesn't. Then jump
  straight into the body.

## CLI and wrapper-script skills

Focus on the most common use cases, and give the agent exactly what it needs for them. Start with a Usage section. The
`herdr-cheatsheet` skill is a good model:

```markdown
## Usage

- herdr pane split --current --direction right --cwd <path> --no-focus
- herdr agent start reviewer --kind claude --pane w1:p2
- herdr agent prompt reviewer "Review the current diff." --wait --timeout 120000
- herdr agent read reviewer --source recent-unwrapped --lines 120
- herdr --help

## Notes

- Read IDs from the JSON responses, never guess them. `pane split` returns `.result.pane.pane_id`.
- `agent start` needs an existing pane at an idle shell prompt, so split first.
```

- Usage lists real commands, not flag descriptions. A full worked example beats an explanation.
- End Usage with the top-level `--help`, so the agent knows where the rest is.

After Usage, add a section for anything else the agent needs that `--help` won't tell it, named for what it holds. What
that is depends on the tool: a script location, required env vars, where output lands, the JSON shape, what exit codes
mean, when to pick one command over another.
