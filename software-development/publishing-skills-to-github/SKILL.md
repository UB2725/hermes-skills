---
name: publishing-skills-to-github
description: Publish local skills or dirs to GitHub without the gh CLI.
---

# Publishing Skills / Directories to GitHub (no gh CLI)

Use when the user asks to push a local skill (or any directory) into a new or existing GitHub
repo and `gh` is not installed. Everything here is `git` + the GitHub REST API over `curl`.

For general GitHub work when `gh` IS installed, use the `github` skill instead. This skill is the
fallback path and the packaging/shape conventions for skill repos.

## Procedure

1. **Find the token — read the file, do not rely on shell expansion.** The token lives in
   `~/.hermes/.env` as `GITHUB_TOKEN=`. Read the file and parse it in-process:

   ```python
   import re
   raw = open("/Users/<user>/.hermes/.env").read()   # or $HERMES_HOME/.env
   T = re.search(r"^GITHUB_TOKEN=(.+)$", raw, re.M).group(1).strip().strip('"').strip("'")
   ```

   Then persist it once for reuse and lock it down: `chmod 600` on the temp file holding it.

2. **Validate the token and learn the account before creating anything.**

   ```bash
   curl -s -o /dev/null -w '%{http_code}\n' \
     -H "Authorization: Bearer $TOKEN" https://api.github.com/user          # 200 = good
   curl -s -H "Authorization: Bearer $TOKEN" https://api.github.com/user \
     | python3 -c "import sys,json; print(json.load(sys.stdin)['login'])"
   ```

   Also read `x-oauth-scopes` from a plain `curl -sI` — a classic PAT needs `repo` to create
   repos and push. Report the login to the user so they can confirm it is the account they meant.

3. **Check whether the repo already exists** before creating, so you never clobber one:

   ```bash
   curl -s -o /dev/null -w '%{http_code}\n' -H "Authorization: Bearer $TOKEN" \
     https://api.github.com/repos/<owner>/<name>      # 404 = free to create, 200 = exists
   ```

4. **Create the repo via REST** (no `gh repo create` needed).

   ```bash
   curl -s -H "Authorization: Bearer $TOKEN" -H "Accept: application/vnd.github+json" \
     https://api.github.com/user/repos -d '{"name":"<name>","private":false,"auto_init":false}'
   ```

   Set `private` explicitly rather than relying on an account default. `auto_init: false` avoids
   a conflicting initial commit when you are about to push your own first commit.

5. **Stage the payload, commit, push.** Set the identity per-repo or with `-c` on the commit so
   you never mutate the user's global git config:

   ```bash
   cd <repo-dir>
   git init -q
   git add -A
   git -c user.name="<login>" -c user.email="<login>@users.noreply.github.com" commit -q -m "<msg>"
   git branch -M main
   git remote add origin https://<login>:<token>@github.com/<owner>/<name>.git
   git push -q -u origin main
   ```

6. **Verify against the REMOTE, not the local tree.** A successful `push` is not proof the
   content landed as intended. Read the tree back through the API and list paths:

   ```bash
   curl -s -H "Authorization: Bearer $TOKEN" \
     "https://api.github.com/repos/<owner>/<name>/git/trees/main?recursive=1"
   ```

   Confirm every expected file is present with the expected size, and that `private` /
   `default_branch` in the repo metadata match what you intended.

7. **Tell the user about the embedded credential.** See the pitfall below — after a token-in-URL
   push, the token is sitting in `.git/config` in plaintext. Say so explicitly and offer to strip
   it. Do not silently leave a live token in a repo the user may share, copy, or push elsewhere.

## Repo shape for a skill collection

When the payload is one or more skills, keep the layout Hermes itself uses so the folder can be
copied straight into `~/.hermes/skills/`:

```
<category>/<skill-name>/
├── SKILL.md            # required
└── references/         # optional topical depth
```

Add a `README.md` with a table of the skills (name, category, purpose) and a one-line
`cp -R <category>/<skill> ~/.hermes/skills/<category>/` install instruction. A skill folder that
copies without a path rewrite is the whole point of matching the layout.

## Pitfalls

- **A token in the remote URL is written to `.git/config` in plaintext**, and shows up in
  `git remote -v` and in `ps` output. It is fine as a one-shot to get an initial push done, but
  strip it immediately after (`git remote set-url origin https://github.com/<owner>/<repo>.git`),
  or prefer SSH / `credential.helper store` for anything durable. Always disclose it to the user.
- **Persisting the token into an environment variable inside a script does not reliably survive.**
  Re-derive it from the file at the point of use. Guessing that a variable is still populated in a
  later step produces confusing empty-string failures that look like a bad token.
- **Do not treat HTTP 404 as an error when probing for a repo.** In the create-flow it is the
  expected "not yet created" answer; only 200 (exists) or 401 (bad token) change what you do next.
- **`curl | python3` gets flagged by the security scanner** and deserves a second look before you
  run it. Write the response to a file and parse that, or keep the pipeline to a narrow, obvious
  transform.
- **Do not `git config --global` the user's identity** as a side effect of publishing. Use `-c` on
  the commit or set it locally in the new repo.
- **Verify with the remote tree, not `git log`.** A local commit that was never pushed, or a push
  to a different branch, both look successful from inside the repo directory.

## Verification

- Token check returned 200 and the login is the account the user expects.
- Repo metadata read back: `private`, `default_branch`, and `pushed_at` match intent.
- The recursive tree listing from the API contains every file you intended, at the right path.
- You have told the user whether a credential was written into `.git/config`, and offered to remove it.
