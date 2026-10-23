# Releasing paper-lite

paper-lite is a GitHub fork of [sulistef/obsidian-suliworld](https://github.com/sulistef/obsidian-suliworld). Obsidian installs a theme from the GitHub release whose tag equals the `version` in `manifest.json`, so the two must always agree.

## Rules

- The tag is the bare version: `0.1.1`, never `v0.1.1`.
- `manifest.json` `version` must equal the tag exactly.
- Each release must attach `manifest.json` and `theme.css`. The release workflow does this.
- `theme.css` is generated. Edit `src/theme.css`, then run `./build.sh` before committing.

## Release steps

1. Edit `src/theme.css`, then run `./build.sh`.
2. Bump `version` in `manifest.json` (for example `0.1.0` to `0.1.1`).
3. Commit and push to `main`:
   ```bash
   git add -A && git commit -m "Bump version to 0.1.1" && git push origin main
   ```
4. Tag with the same version and push the tag:
   ```bash
   git tag 0.1.1 && git push origin 0.1.1
   ```
5. The **Release** workflow (`.github/workflows/release.yml`) checks the tag against `manifest.json`, then creates the release with `manifest.json` and `theme.css` attached.
6. Verify:
   ```bash
   gh release view 0.1.1 --json tagName,assets --jq '{tag:.tagName,assets:[.assets[].name]}'
   curl -sL -o /dev/null -w "%{http_code}\n" https://github.com/jrayas/paper-lite/releases/download/0.1.1/theme.css
   ```
   Both downloads must return 200. A raw-file fetch can be stale for a few minutes.

If the workflow is unavailable, release by hand with `gh release create 0.1.1 theme.css manifest.json --title 0.1.1`.

## GitHub settings

- **Repository:** `jrayas/paper-lite`, forked from `sulistef/obsidian-suliworld`.
- **Topics:** `obsidian`, `obsidian-theme`, `obsidian-md`, `theme`, `css`.
- **Remotes:** `origin` is the fork, `upstream` is the original. Pull upstream changes with `git fetch upstream && git merge upstream/main`, then rebuild.
- **Actions:** workflows are disabled on a new fork until enabled in the repository's Actions tab (or with the API call below).
  ```bash
  gh api -X PUT repos/jrayas/paper-lite/actions/permissions -F enabled=true
  ```

## Licence and credit

The upstream theme is MIT-licensed. Keep Stéphane Sulikowski's copyright line in `LICENSE` and the "Forked from" note in `README.md`. The bundled fonts are under the SIL Open Font Licence (see `fonts/OFL.txt`).

## Submitting to the community themes list

Before submitting, check that the latest release tag equals `manifest.json` `version`, that `screenshot.png` exists in the repository root, and that `name` in the manifest matches the name you submit.
