# Skills

The runnable half of the vault. `8-System/brain.md` says what each workflow is; the skill says how to do it.

`7-Skills/` is the source of truth. Every skill is a folder with a `SKILL.md`, plus `references/` where the skill needs detail only some runs reach.

## Three places, one original

Agents do not look here. They look in their own folder, so the skills are published into two of them:

| Path | For | How |
|---|---|---|
| `7-Skills/` | The vault's own architecture, and humans reading it | The original |
| `.claude/skills/` | Claude Code, Claude Cowork | Symlinks, so they cannot drift |
| `.agents/skills/` | Codex, ChatGPT and anything else reading the `.agents` convention | Copies, because a symlink does not survive a zip download or a checkout without symlink support |

Edit the original in `7-Skills/`. The Claude side follows on its own. The copies do not, so republish them:

```bash
rm -rf .agents/skills && mkdir -p .agents/skills
for d in 7-Skills/*/; do [ -f "$d/SKILL.md" ] && cp -R "$d" ".agents/skills/$(basename "$d")"; done
```

Check they still match before you commit:

```bash
for d in 7-Skills/*/; do n=$(basename "$d"); diff -rq "$d" ".agents/skills/$n" >/dev/null || echo "drifted: $n"; done
```

An edit that lands in one copy and not the other is the failure this table exists to prevent. It is silent: both files look fine on their own, and the agent that reads the stale one does the old thing.

## Adding a skill

1. Create `7-Skills/<name>/SKILL.md` with `name` and `description` in the frontmatter. The folder name and the `name` field have to match or the skill will not register.
2. Symlink it: `ln -sfn ../../7-Skills/<name> .claude/skills/<name>`
3. Republish the copies with the command above.
4. Add a row to the skills table in `README.md`.

The description is what decides whether a skill fires at the right moment. Say when to reach for it, not what it does step by step - an agent that can act on the description alone will skip the body, and the body is where the rules live.
