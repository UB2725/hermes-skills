---
name: creating-user-local-skills
description: Create or update skills in ~/.hermes/skills.
---

# Creating User-Local Skills (skill_manage)

Use when authoring or updating a skill in `~/.hermes/skills/` from a session. For in-repo
`SKILL.md` files shipped with the package, use `hermes-agent-skill-authoring` instead — the tiers,
validators, and review standards there do not apply to user-local skills.

## Procedure

1. **Survey before writing.** `skills_list()` to check for an existing skill covering the class,
   then `skill_view(name)` on candidates. Prefer extending an existing skill over adding a narrow
   sibling. Creating a near-duplicate is the most common failure of this pass.
2. **Draft the full SKILL.md**, then create it. Use `skill_manage(action='create')`; it writes to
   `~/.hermes/skills/<category>/<name>/SKILL.md` (category optional but recommended).
3. **Add support files in the same batch** when they belong to the skill from the start —
   `references/<topic>.md`, `templates/<file>`, `scripts/<file>`. Batch them as extra operations in
   the same `operations` array; the whole array applies atomically.
4. **Distinguish the two file kinds.** `references/` is for depth read on demand (decision tables,
   recipes, domain notes). `scripts/` is for re-runnable actions the agent should execute rather
   than retype. `templates/` is for files meant to be copied and modified. Do not put a runnable
   probe in `references/`.
5. **Point to every support file from SKILL.md** with a one-line description, or a future session
   will never know it exists.
6. **Verify on disk after writing.** The tool reports success per operation; confirm the paths and
   sizes actually exist before telling the user it is saved.

## Frontmatter rules (user-local)

```yaml
---
name: my-skill-name
description: Class-level capability statement, 60 chars or less.
---
```

- **Keep `description` at 60 characters or fewer — count it, do not eyeball it.** Skills are
  created with the description shown in the system prompt; anything longer is truncated at 57
  chars plus `...` and the routing signal is lost. The limit is enforced on create, so an
  off-by-one rejects the whole call with a character-count error.
- **No colon in the `description`.** A bare `key: value` inside an unquoted YAML scalar makes the
  parser read the whole line as a mapping and the create call fails with a frontmatter parse error.
  Rewrite to avoid the colon, or wrap the value in double quotes. This is a common cause of a
  mystifying "mapping values are not allowed here" failure.
- The body is free-form markdown; the classic section order (`When to Use` / `Procedure` /
  `Pitfalls` / `Verification`) keeps entries legible and greppable.

## What belongs in a skill vs memory

- **Skill**: the procedure for a class of task — the steps in order, the commands that work, the
  user's preferences for the result, and the pitfalls that cost time. Loads only when relevant.
- **Memory**: facts true in every session regardless of task — who the user is, environment facts,
  standing conventions with no task home. Memory is a small, hard-capped budget.
- A durable preference about HOW a kind of work should be done belongs in the skill that governs
  that work, not in memory, even when the user stated it as a general complaint.

## Editing existing skills

- **Read before writing.** Call `skill_view(name)` (and `skill_view(name, file_path=...)` for the
  exact file) in the same session as the edit. The tool refuses a patch otherwise, and content
  quoted earlier in a transcript does not satisfy the check.
- **Prefer `patch` over rewriting.** A targeted `old_string`/`new_string` edit keeps the rest of the
  file intact; `content` alone replaces the entire SKILL.md.
- **Fix the misleading sentence in place.** Do not append an `UPDATE:` paragraph below a rule that
  is now wrong — edit the rule, and delete the wording it replaced.

## Pitfalls

- **Bundled and hub-installed skills cannot be patched by a background/curator pass.** Writes are
  refused with an explicit "Refusing background curator patch for bundled skill" error. When the
  learning belongs in a protected skill, create a curator-managed sibling or fall back to the
  closest unprotected skill instead of retrying the refused write.
- **A batch `operations` array is atomic.** If one operation fails validation, every operation in
  the array is rolled back. Fix the offending operation and re-send the whole array; do not assume
  earlier operations in the array were applied.
- **Do not name a skill after a single session artifact.** Names must describe the class of task
  (`publishing-skills-to-github`), never a ticket number, an error string, or a one-off incident.
- **Do not encode environment state as a rule.** A missing binary or an unconfigured credential is
  fixable by the user and is not a durable constraint; capture the install/config step instead.

## Verification

- `skills_list()` shows the new skill with the intended description (note: the current session's
  loader is cached, so it may not appear as a loadable skill until a new session).
- Every file intended on disk exists, at the right path, with plausible size.
- Each support file is referenced by name from SKILL.md.
