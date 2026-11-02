---
type: topic
updated: 2026-11-02
---

# Runbook style

Use for pages that tell an agent what to do, such as [[weekly-review]]. Not for reference notes.

Meaning comes first. If a rule would obscure it, adapt the rule.

- Start with the action, in the imperative, unless a condition or warning must be checked first.
- Use plain English and short, complete sentences.
- Put one step or rule in each bullet, with its conditions and exceptions.
- Number steps when order matters.
- Explain why only when it changes how the rule is applied.

## Example

1. Back up the database.
2. Restore the backup to a test server.
3. If the restore succeeds, delete the old database. If it fails, stop and tell the owner.
