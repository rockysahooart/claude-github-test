# claude-github-test

A dependency-free static site deployed to GitHub Pages by GitHub Actions.

- Repo: https://github.com/rockysahooart/claude-github-test
- Live: https://rockysahooart.github.io/claude-github-test/

## Default workflow

When I describe a change, carry it through to a live deploy without asking me to
run Git commands. Stop only if authentication or another authorization that
genuinely requires me is blocking you.

1. Implement the change.
2. Validate locally before committing (see Validation below).
3. Commit with a short, descriptive message.
4. Push to `main`.
5. Watch the Actions run to completion.
6. Verify the live site responds, then report success or failure and give me the
   live URL.

Report the deploy outcome plainly. If the run fails, show the failing step's log
and fix it rather than reporting a URL that does not serve the change.

## Environment notes

`gh` is installed but not on `PATH`; call it at `/opt/homebrew/bin/gh`.

Plain `git push` fails with "could not read Username for 'https://github.com'".
Push with the CLI's credentials instead, per command:

```
PATH="/opt/homebrew/bin:$PATH" git -c credential.helper='!gh auth git-credential' push origin main
```

This is deliberate — it keeps the credential helper out of the global gitconfig.
Do not run `gh auth setup-git` without asking.

Pushes that touch `.github/workflows/` need the `workflow` token scope. If one is
rejected for a missing scope, that is a user-only authorization: ask me to run
`gh auth refresh -h github.com -s <scope>` and resume afterward.

## Validation

No build step and no test suite. Before committing, check that:

- every `id` referenced in `script.js` exists in `index.html`
- every `href`/`src` in `index.html` resolves to a file in the repo
- workflow YAML under `.github/workflows/` parses

After deploying, confirm the live page returns 200 and serves the change. Load it
in the browser and exercise the interaction when the change is behavioral.

## Deployment

`.github/workflows/deploy-pages.yml` deploys on every push to `main`, and can be
run manually via `workflow_dispatch`. It publishes the repository root as the
Pages artifact using the official actions: checkout, configure-pages,
upload-pages-artifact, deploy-pages.

Pages is configured with `build_type: workflow`. There is no `gh-pages` branch and
no build output directory — the repo root is what ships, so any new file at the
root is published.
