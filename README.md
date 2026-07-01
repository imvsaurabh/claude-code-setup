# Claude Code setup — Java backend workflow

Drop this folder's contents into the root of your project (or copy the
`.claude/` folder and `CLAUDE.md` into an existing project root).

```
your-project/
├── CLAUDE.md                          # always-loaded project memory
└── .claude/
    ├── settings.json                  # hooks configuration
    ├── hooks/
    │   └── HOOKS.md                   # explains what each hook does
    ├── skills/
    │   ├── company-coding-standards/SKILL.md
    │   ├── spring-boot-rest-conventions/SKILL.md
    │   ├── apache-camel-conventions/SKILL.md
    │   ├── structured-logging/SKILL.md
    │   ├── design-patterns-checklist/SKILL.md
    │   ├── bdd-test-standards/SKILL.md
    │   ├── bug-ticket-intake/SKILL.md
    │   ├── code-review-checklist/SKILL.md
    │   └── security-review/SKILL.md
    └── agents/
        ├── api-builder.md
        ├── message-processor-builder.md
        ├── bug-fixer.md
        ├── test-writer.md
        ├── code-reviewer.md
        ├── security-scanner.md
        ├── security-fixer.md
        └── plan-reviewer.md
```

## Before this is fully functional

Everything here is a real starting structure, but several files contain
placeholders you need to fill in with your team's actual conventions:

- `CLAUDE.md` — Java version, exact branch/commit conventions
- `company-coding-standards/SKILL.md` — your real package layout if it
  differs from the example, naming conventions, forbidden patterns
- `structured-logging/SKILL.md` — your exact mandatory log fields
- `design-patterns-checklist/SKILL.md` — the patterns your team actually
  expects, beyond the illustrative examples given
- `.claude/settings.json` — real Maven/Gradle commands, linter paths, and
  the branch-protection script referenced in `HOOKS.md`

## First run

1. Open Claude Code in this project.
2. Run a small test task, e.g. "add a health-check endpoint," and confirm:
   - it reads `CLAUDE.md` and mentions loading the relevant skill(s)
   - the hooks fire after you approve a file edit (formatter/linter output
     should appear)
3. Adjust skill descriptions if a skill doesn't trigger when you expect —
   the description field is what the model matches against, so make it
   specific to your real task phrasing.

## Notes

- `code-reviewer`, `security-scanner`, and `plan-reviewer` are defined with
  read-only tool access (no `Edit`/`Write`) by design — this is intentional,
  not an oversight. Detection and fixing are kept as separate steps so you
  always get a human checkpoint in between.
- `security-fixer` only acts on findings you've explicitly approved — it
  will not silently apply the full `security-scanner` report.
- Hook event names and JSON schema in `settings.json` may drift as Claude
  Code evolves — check the current hooks reference in the Claude Code docs
  if something doesn't fire as expected.
