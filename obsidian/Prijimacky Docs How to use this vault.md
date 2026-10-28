---
title: Prijimacky Docs How to use this vault
type: note
tags: [meta]
created: 2026-10-28
updated: 2026-10-28
summary: Where the Prijimacky Docs vault lives, how to open it in Obsidian, and the rules for keeping it current.
---
# Prijimacky Docs — How to use this vault

Back to [[Prijimacky Docs Home]].

## Where it lives
The vault is the `obsidian/` folder of the [`Dokumentace-Prijimacek-v-Pohode`](https://github.com/Elemenous-Studio/Dokumentace-Prijimacek-v-Pohode) repository. **Git is the sync**: sessions commit changes, you pull them, and you push your own edits.

## Opening it in Obsidian (Windows)
It is linked into the main vault with a directory junction, so it appears at `Elemenous\Prijimacky Docs`. To recreate the link (in `cmd`, not PowerShell; the target folder must not exist yet):

```cmd
mklink /J "C:\Users\vojta\Documents\OBSIDIAN_MAIN\MAIN\Elemenous\Prijimacky Docs" "<path to your clone>\obsidian"
```

The junction points at the working copy, so Obsidian shows whatever branch that clone has checked out — keep the clone on `main` and `git pull` to see what sessions wrote.

## Rules (people and Claude)
- Read [[Prijimacky Docs Home]] first, then [[Prijimacky Docs Tasks]] and [[Prijimacky Docs Open questions]].
- After a work session: update [[Prijimacky Docs Tasks]], record decisions in [[Prijimacky Docs Decisions]] (dated, with the reason), move answered items out of [[Prijimacky Docs Open questions]], add `Log/YYYY-MM-DD Prijimacky Docs`.
- Every note has `title`, `type`, `tags`, `created`, `updated`, `summary` properties. Bump `updated` on change.
- Note names are unique across the whole main vault — prefix project notes with `Prijimacky Docs`.
- Every folder has a `CLAUDE.md` with instructions for agents (what belongs there, note shape).
- No secrets and no personal data — it's Git. Secrets stay in Doppler.
