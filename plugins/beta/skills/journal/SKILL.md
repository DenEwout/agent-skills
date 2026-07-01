---
name: journal
description: Form or update today's daily log, a single per-scope file combining freeform notes and a time-tracking summary in the relevant Obsidian vault. Drafts both the notes and the time entries from what was actually worked on this session and shows them for review before writing. Use when the user wants to log what they did today, record time spent, or proactively when the user wraps up a session of substantive work on a client/project.
argument-hint: [ scope? ] [ note or time entry ]
---

Form a **daily log** inside the relevant Obsidian vault: one file per scope per day that holds both freeform notes and a
time-tracking summary for that day.

Maintain a clean, consistent structure in a dedicated folder. **Do not** try to fit into or edit the
pre-existing notes outside that structure.

## Where the daily log goes

Read the `# Context` section of the user's global `CLAUDE.md` to know in which vault/folder to work.

Infer scope from the working directory; ask once if unclear; don't guess silently for billable work.

Inside that folder, use a dedicated `Journal/` folder to put in `<YYYY-MM-DD>-daily.md` files for each day.

## Write behavior

Create the folders or files if they don't exist. **Always read the file first if it exists** never blindly overwrite it;
append/merge into the right section instead, do not make duplicate statements.

## File structure

```markdown
---
type: daily
scope: ovam
date: 2026-07-01
---

# 2026-07-01 — OVAM

## Notes

- Root-caused the intermittent BAGS `tempDocumentId` failure with [[Yentl]]: only reproduces after a DB restart, so
  likely a stale connection-pool entry. Worked around it with a manual DNG SOAP call; the real fix (a validation query
  in `DB2_DS.xml`) is still open.
- [[Jef]]'s anonymisation batch is ~20K/33K done; the nightly failures all trip on Alfresco and need prod write access
  to requeue.

## Time

- [duration:: 2h] [ticket:: MISTRAL-2881] — migration bug fix
- [duration:: 1h] — code review

**Total: 3h**
```

Conventions:

- **Notes and Time are different. Don't restate one in the other.** Notes = what happened and what was decided
  (findings, root causes, blockers, follow-ups, people). Time = the billable breakdown. "Reviewed a PR" is a Time entry,
  not a note, unless there's a finding worth keeping.
- Notes: flat/nested bullets. `[[wiki-link]]` people and proper-noun entities; leave Jira keys and URLs inline.
- `date` in the frontmatter is a plain YAML property (single colon: `date: 2026-07-01`), separate from the `::` inline
  fields in the body.
- Time entries are Dataview inline fields, one bullet per entry, newest last: `[duration:: 2h]` plus optional
  `[ticket:: KEY]`. Normalize whatever was given (a `45min` shorthand or a `14:00-16:00` clock range) into one
  `[duration:: ...]` value; when a clock range was given keep it in the text too (`14:00-16:00 — ...`), since the
  duration alone loses it. Omit `[ticket:: ...]` when there's none. The `::` is Dataview inline-field syntax, so entries
  stay queryable if you later add Dataview queries over this folder.
- `**Total: <sum>**` is the day's time summary: add up the `[duration:: ...]` values (present as e.g. `3h45`) and
  recompute it whenever the Time section changes.

## Forming or updating the log

1. **Parse the input.** A duration (`2h`, `45min`) or clock range (`14:00-16:00`) with a description → a Time entry;
   other free text → a note. With no arguments (or right after a chunk of substantive work), draft the whole log from
   what was actually done. If the work spanned more than one scope, draft a separate log per scope.
2. **Draft, don't write blind.** For anything you inferred (durations, what was done, what mattered), show the proposed
   entries for confirmation first. Never write an inferred entry the user hasn't seen. Entries the user dictated verbatim
   can go straight through, since they've already seen them.
3. **Write it.** Append notes under `## Notes` and time entries under `## Time`, per the write behavior above.
4. **Report** the file path and lines written so the user can fix them in Obsidian.
