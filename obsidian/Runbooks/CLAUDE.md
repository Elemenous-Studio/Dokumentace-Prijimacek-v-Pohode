# Runbooks/ — operational procedures

Part of the Prijimacky Docs vault. Start at `../CLAUDE.md` if you haven't.

**What goes here:** step-by-step procedures someone will run under pressure — deploy, rollback, restore a backup, rotate a secret, onboard a server. `type: runbook`, named `Prijimacky Docs <action> runbook`.

**Shape:** When to use → Preconditions → Numbered steps with exact commands → How to verify → How to undo. Commands must be copy-pasteable. Mark anything that needs `sudo` or touches production as such.

If a run reveals a wrong step, fix the runbook in the same session.

General rules: frontmatter on every note, bump `updated`, link the note from `Prijimacky Docs Home` (or the relevant parent note), no secrets or personal data.
