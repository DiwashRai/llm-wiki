# LLM wiki

A knowledge base kept by agents and browsed in Obsidian. Start at `index.md`: it lists every page and sets the page
conventions.

Any agent may add to the wiki, but tell the user what was added or changed. **Never commit or push without the user's
permission**, however small the change.

## Adding to the wiki

Agents load these pages as context, so every line costs tokens and dilutes attention.

- Keep pages short. Leave out anything an agent would do anyway.
- State things directly: "keep it short", not "the owner wants it short".
- Prefer bullet points to tables.
- List every page in `index.md` with a short, accurate description. Agents use it to find a page without opening others,
  so the description must say what the page holds.
