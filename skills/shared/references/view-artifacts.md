# View Artifacts (Shared)

## What to Achieve

Make the markdown artifacts produced by agents readable to the user during validation, without modifying the original content.

## When to Use

Trigger this from the validation gate whenever the user asks to view artifacts before approval, or when artifacts include diagrams or tables that benefit from rendering.

## What to Do

1. Identify the markdown files for the current stage in the agent output directory (`outputs/agent-discovery/` or `outputs/agent-architecture-dotnet/`).
2. Generate a single file at `outputs/validation_view.html` strictly following the template `skills/shared/assets/html-template-generator.html`.
   - Keep the base structure (header, nav-tabs, tab-pane, artifact-card, footer)
   - Preserve template CSS classes (do not create visual variations)
   - Insert artifact content into tabs/cards without changing the base style
   - Render Mermaid.js diagrams inline (use `mermaid.min.js` CDN)
   - Include timestamp and source file list in the footer
3. Inform the user of the generated file path so it can be opened in the browser.

## What This Agent Does NOT Do

- It does not modify original markdown files.
- It does not filter or summarize content; it renders the full content.
- It does not keep a history of previous visualizations (`validation_view.html` is overwritten on each invocation).

## What Success Looks Like

The user can read all stage artifacts in a single navigable HTML page, with visible diagrams, without opening files manually.

## Guidance

- Use `skills/shared/assets/html-template-generator.html` as the only base template.
- Do not create new visual patterns for each execution; vary only content.
- Preserve the heading structure from the original markdown.
- If a Mermaid diagram is invalid, show the source code block with a visible warning, without breaking the page.
