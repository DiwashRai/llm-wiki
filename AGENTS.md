# LLM wiki

A knowledge base kept by agents and browsed in Obsidian. Start at `index.md`, then
`active/index.md`, which lists the pages in use and sets the page conventions.

Any agent may add to the wiki, but tell the user what was added or changed. **Never commit or push
without the user's permission**, however small the change.

## Folders

Knowledge moves left to right: `unprocessed/` (raw) → `distilled/` (processed) → `active/` (in use).
Work from `active/` by default.

- File new captures and agent research in `unprocessed/`.
- In `distilled/`, record the user's ideas in their words. Mark any agent suggestion as one.
- Move a page right only once the user has processed it, or when it goes into use.

## Adding to the wiki

Agents load these pages as context, so every line costs tokens and dilutes attention.

- Keep pages short. Leave out anything an agent would do anyway.
- State things directly: "keep it short", not "the owner wants it short".
- Prefer bullet points to tables.
- List every page in its folder's `index.md` (`unprocessed/` excepted), except files that belong to
  a workflow, with a short, accurate description. Agents use it to find a page without opening
  others, so the description must say what the page holds.
- Keep each list of pages in alphabetical order.
