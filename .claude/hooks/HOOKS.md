# Hooks — deterministic guardrails

Hooks are shell commands Claude Code runs automatically on lifecycle events.
No LLM judgment involved — they fire every time, regardless of what the agent
"remembers" to do. This is what makes them the real enforcement layer, not
CLAUDE.md (which is read every session but is still advisory) or skills
(which load only when the model judges them relevant).

The actual hook configuration lives in `.claude/settings.json` next to this
file — that's the file Claude Code reads. This document explains what each
one does and why it exists, so the JSON isn't a black box.

## PostToolUse — after any edit/write to a `.java` file

1. **Format + lint** — runs your formatter (e.g. `mvn spotless:apply`) and
   static analysis (Checkstyle/PMD). Catches style drift immediately instead
   of at review time.
2. **Run affected tests** — runs the test module touched by the change, so a
   broken test surfaces within the same turn, not at the end of a long
   session.
3. **Secret scan** — runs gitleaks on the diff. Blocks the operation if a
   credential-shaped string is detected, regardless of whether the agent
   intended to commit it.

## PostToolUse — after any edit to `pom.xml`

4. **Dependency CVE check** — runs OWASP Dependency-Check. Surfaces new
   vulnerable dependencies the moment a version bump or new dependency is
   added, rather than waiting for a dedicated `security-scanner` run.

## PreToolUse — before any Bash command

5. **Block dangerous git operations** — vetoes `git push --force` and direct
   commits to `main`/`master`. Requires an explicit override conversation
   rather than silently allowing it.

## SessionStart

6. **Sanity check** — confirms `CLAUDE.md` was loaded and the working branch
   isn't `main`/`master`, so you notice immediately if something's off
   rather than discovering it after work has already started.

## Fill in for your environment

The commands in `settings.json` use placeholders (`<your-format-command>`,
etc.) — replace them with your actual Maven/Gradle commands, linter config
paths, and branch-naming rules before this becomes fully functional.
