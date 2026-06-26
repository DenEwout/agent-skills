---
name: htmldoc
description: Render documentation as a standalone, fully offline HTML file (consistent CSS, dark-mode toggle, syntax-highlighted code, Mermaid diagrams). Use when asked to produce any documentation.
allowed-tools: Write, Bash
---

Produce a standalone HTML document by writing the body content as an HTML fragment and concatenating it between a fixed `header.html` and `footer.html`. Styling, the dark-mode toggle, syntax highlighting, and Mermaid all live in those two files — you never touch them. The output is fully self-contained and works offline.

## How it works

The template is split in two so assembly is pure concatenation (no string replacement, no escaping):

- `header.html` — `<head>` with **Pico CSS and both highlight.js themes inlined**, the dark-mode toggle, and `<body><main class="container">`. ~88KB. **Do not read it** — it would just burn context, and you never need to edit it.
- `footer.html` — closes the page; has **highlight.js and Mermaid inlined** (~3.6MB) and runs them on load. **Do not read it** either.

You write only the middle: the body content.

## Steps

1. **Write the body as an HTML fragment** to `content.html` in the working directory. No `<html>`, `<head>`, `<body>`, or `<style>` — just the content that goes inside `<main>`:
   - Use semantic tags: `<h1>` (first one becomes the page title), `<h2>`/`<h3>`, `<p>`, `<ul>`/`<ol>`, `<pre><code>`, `<table>`, `<blockquote>`, `<a>`.
   - Pico styles all of these automatically. **Do not add CSS classes** except the helpers below.
   - Code blocks: for syntax highlighting, add a language class to the `<code>`, e.g. `<pre><code class="language-python">…</code></pre>`. highlight.js colors it on load and recolors it with the dark-mode toggle. Escape `<`, `>`, `&` inside code as `&lt;`, `&gt;`, `&amp;`.
   - Side-by-side layout: wrap two blocks in `<div class="columns">…</div>` (e.g. two `<pre>` snippets).
   - Diagrams: emit `<pre class="mermaid">graph TD; A--&gt;B</pre>` with Mermaid syntax inside. Prefer Mermaid over hand-written SVG — its layout engine places nodes correctly.

2. **Assemble** the final file into a `docs/` folder in the current working directory (created if missing). Pick a short kebab-case filename. The output goes to `docs/<name>.html`, relative to the agent's CWD; the template stays out of context.

   ```bash
   mkdir -p docs
   cat "$CLAUDE_PLUGIN_ROOT/skills/htmldoc/header.html" content.html "$CLAUDE_PLUGIN_ROOT/skills/htmldoc/footer.html" > "docs/<name>.html"
   rm content.html
   ```

   PowerShell fallback:

   ```powershell
   New-Item -ItemType Directory -Force docs | Out-Null
   Get-Content "$env:CLAUDE_PLUGIN_ROOT/skills/htmldoc/header.html","content.html","$env:CLAUDE_PLUGIN_ROOT/skills/htmldoc/footer.html" | Set-Content "docs/<name>.html"
   Remove-Item content.html
   ```

3. **Report** the output path (`docs/<name>.html`, relative to the working directory) and try to open it with Chrome.

