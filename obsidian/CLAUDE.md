# Obsidian vault — Prijimacky Docs

This `obsidian/` folder is the project's shared memory: plans, decisions, tasks, runbooks and a log of work sessions. It is plain Markdown in Git, so every session (local or cloud) can read and update it. On Vojta's PC it is also mounted into the main Obsidian vault at `C:\Users\vojta\Documents\OBSIDIAN_MAIN\MAIN\Elemenous\Prijimacky Docs` (directory junction), so whatever you commit here shows up in Obsidian.

## Read order
1. `Prijimacky Docs Home.md` — the map of this vault.
2. `Prijimacky Docs Tasks.md` and `Prijimacky Docs Open questions.md` — what is in flight and what is blocked.
3. The newest note in `Log/` — what the last session did and left.
4. Then the folder you need. **Every folder has its own `CLAUDE.md`** saying what belongs there; read it before adding a note.

## Layout
| Path | What |
|---|---|
| `Prijimacky Docs Home.md` | Entry point (MOC). Links to every note. |
| `Prijimacky Docs System overview.md` | How the system works today, in one page. |
| `Prijimacky Docs Tasks.md` | Work to do, by priority (P1/P2/P3), linked to GitHub issues. |
| `Prijimacky Docs Decisions.md` | Dated decision log: what, why, what it costs if wrong. |
| `Prijimacky Docs Open questions.md` | Things waiting for an answer; move them out once answered. |
| `Prijimacky Docs How to use this vault.md` | Human-facing version of these rules + Obsidian setup. |
| `Plans/` | Forward-looking plans and specs (summaries; the full spec may live in `docs/`). |
| `Reference/` | Stable facts: domain knowledge, APIs, external services, glossary. |
| `Runbooks/` | Step-by-step operational procedures (deploy, rollback, restore). |
| `Log/` | One note per work session: `YYYY-MM-DD Prijimacky Docs.md`. |

## Rules
- **After a work session**: update `Prijimacky Docs Tasks.md`, add decisions to `Prijimacky Docs Decisions.md` (dated, with the reason), move answered items out of `Prijimacky Docs Open questions.md`, and write `Log/YYYY-MM-DD Prijimacky Docs.md`. Commit the vault together with the code change it describes.
- **Note names must be unique across the whole main vault** (Obsidian resolves `[[links]]` by file name, and every Elemenous project lives in one vault). Project-level notes and log notes carry the `Prijimacky Docs` prefix; notes inside `Plans/`, `Reference/`, `Runbooks/` use specific names (`Prijimacky Docs deploy runbook`, not `Deploy`).
- Every note has frontmatter: `title`, `type` (moc | plan | note | reference | runbook | log), `tags`, `created`, `updated`, `summary`. Bump `updated` when you change a note.
- Link with `[[wikilinks]]`. Every new note is linked from `Prijimacky Docs Home` or another note — no orphans.
- The vault **summarises and links**; code and full specs stay the source of truth. Point at repo files with backticked paths (`docs/plans/x.md`).
- **No secrets and no personal data** (it is Git). Secrets live in Doppler; name the key, never the value.
- Don't edit `.obsidian/`. These `CLAUDE.md` files are for agents; humans can ignore them.
- GitHub: https://github.com/Elemenous-Studio/Dokumentace-Prijimacek-v-Pohode
