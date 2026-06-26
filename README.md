# agent-skills

A personal [Claude Code](https://docs.claude.com/en/docs/claude-code) plugin marketplace (`denewout-skills`). It ships one plugin, **core**, containing a few skills.

## Install

```bash
# Add the marketplace, then install the core plugin
/plugin marketplace add https://github.com/DenEwout/agent-skills
/plugin            # install "core"
/reload-plugins    # apply
```

For local development, point the marketplace at the checkout instead:

```bash
/plugin marketplace add /path/to/agent-skills
```

## Skills

| Skill | Invocation | Purpose |
|-------|------------|---------|
| `htmldoc` | user + model | Render documentation as a standalone, fully offline HTML file. |
| `spar` | user only | Entrypoint (`/core:spar`) that launches a sparring session. |
| `sparring` | model-invocable | Interrogate an idea or plan with tough, adversarial questions. |

### htmldoc

Produces a single self-contained HTML file with Pico CSS, highlight.js syntax
highlighting, a dark-mode toggle, and Mermaid diagrams — all inlined, so the
output works offline (~3.6MB per file).

The template is split into a fixed `header.html` and `footer.html`. Generating a
doc is pure concatenation: the model writes only the body fragment, then assembles
`header + content + footer` into `docs/<name>.html` in the working directory. The
large template files never enter the model's context.

### spar / sparring

`spar` is a thin, user-only launcher (`disable-model-invocation: true`) for
`sparring`, which challenges a decision or plan with adversarial questions and
offers alternatives. The split keeps `spar` explicitly user-triggered while
`sparring` stays available for Claude to invoke automatically.

## Layout

```
.claude-plugin/marketplace.json   # marketplace manifest
plugins/core/
  .claude-plugin/plugin.json      # plugin manifest
  skills/
    htmldoc/    SKILL.md + header.html + footer.html
    spar/       SKILL.md
    sparring/   SKILL.md
```

## License

MIT — see [LICENSE](LICENSE).
