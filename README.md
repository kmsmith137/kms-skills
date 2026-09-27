# kms-skills

Agent skills shared across projects, usable from both Claude Code and Codex.
Both tools use the same skill format: a directory containing a `SKILL.md` with
`name` and `description` frontmatter, plus any files the skill needs.

| Skill | What it does | Agents | Runs |
|---|---|---|---|
| `kms-artifact` | Render a markdown file (with math) to HTML and publish it as a claude.ai artifact, for reading on a phone | Claude only (uses Claude's Artifact tool) | when asked, or automatically |
| `kms-humanize` | Rewrite or re-explain text so a human can follow it | Claude, Codex | only when invoked |
| `kms-report` | After implementing a plan, write `plans/<topic>_report.md` | Claude, Codex | only when invoked |
| `kms-research` | Run a long research campaign in the current directory: dispatch subagents, keep a journal, write a summary per campaign | Claude, Codex (Codex untested) | only when invoked |
| `kms-review-plan` | Final review of a plan before handing it to an implementing agent | Claude, Codex | only when invoked |

Invoke a skill as `/kms-report` in Claude Code, or `$kms-report` in Codex (or
pick it from `/skills`). Text after the name is passed to the skill.

## Installing

```bash
./install              # symlink every skill into the default locations
./install --dry-run    # show what would change
./install --uninstall  # remove the symlinks
```

`install` symlinks each skill directory into:

- `~/.claude/skills/` (Claude Code),
- `~/.xsb/claude/skills/`, if `~/.xsb/claude` exists (Claude inside an xsb
  sandbox),
- `<dir>/skills/` for each `--claude-dir <dir>` (any other Claude config dir,
  e.g. a project whose `xsb.toml` sets its own `claude_dir`),
- `~/.agents/skills/` (Codex; its user-level skill directory).

Claude keeps user skills inside its config dir, so each config dir needs its
own links. Codex's user skills don't depend on its config dir (`CODEX_HOME`),
so `~/.agents/skills` serves every Codex, sandboxed or not.

Edits in this repo take effect immediately. Re-run `install` only after adding,
renaming or removing a skill; it also removes links left over from renamed
skills. It never overwrites an existing file or a symlink pointing elsewhere.

### Sandboxed agents

A sandboxed agent must be able to see both the symlinks and this repo. For
xsb, add to `~/.xsb/config.toml` (adjust the path if this repo lives elsewhere):

```toml
[[mount]]
path = "~/git/kms-skills"
mode = "ro"

[[mount]]
path = "~/.agents/skills"
mode = "ro"
```

(`~/.xsb/claude` is already mounted for Claude.) Read-only is deliberate: agents
can use the skills but not edit them. Edit them on the host.

## Writing portable skills

- **Frontmatter:** `name` and `description` are required by both tools.
  Claude Code's extra keys (`argument-hint`, `disable-model-invocation`) are
  ignored by Codex (checked with Codex 0.157.1).
- **Invoked-only skills** need a setting for each tool:
  `disable-model-invocation: true` in `SKILL.md` for Claude, and
  `agents/openai.yaml` containing `policy: allow_implicit_invocation: false`
  for Codex.
- **Bundled files:** refer to them as "next to this SKILL.md", never by a
  repo-relative path such as `.claude/skills/...`; both tools tell the agent
  where the skill file is.
- **Arguments:** Claude Code substitutes `$ARGUMENTS`; Codex doesn't (the text
  arrives as part of the user's message). If a skill uses `$ARGUMENTS`, say
  what to do when it appears literally.
- **Tool-specific steps:** name the mechanism for each tool (e.g. "dispatch a
  subagent: in Claude Code, the Agent tool; in Codex, spawn a sub-agent"). A
  skill that can't work in one tool goes in `CLAUDE_ONLY` in `install`.
- **Project-specific details** don't belong here: refer to "the project's
  instructions (CLAUDE.md or AGENTS.md)" instead.
