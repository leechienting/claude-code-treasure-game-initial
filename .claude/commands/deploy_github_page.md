# Deploy to GitHub Pages

Deploy the current Vite project to GitHub Pages and return the live URL.

## Steps

### 1. Check prerequisites

Run these checks in parallel:
- `git --version` — confirm git is installed
- `gh --version` — check if GitHub CLI is installed
- `git status` — check if this is already a git repo

If `gh` is not installed, run `brew install gh` (macOS). Then stop and tell the user to run `! gh auth login` in the prompt to complete interactive GitHub authentication, since it requires browser interaction. Do not proceed until they confirm they are logged in.

If `gh` is installed, check login status with `gh auth status`. If not logged in, tell the user to run `! gh auth login` and wait for confirmation.

### 2. Initialize git if needed

If the directory is not a git repo (`git status` failed), run:
```
git init
git add .
git commit -m "Initial commit"
```

### 3. Determine repo name and GitHub username

- Get GitHub username: `gh api user --jq '.login'`
- Derive repo name from the current directory name (use the basename, lowercase, replace spaces with hyphens)

### 4. Create GitHub remote if none exists

Check for an existing remote: `git remote get-url origin`

If no remote exists, create a new public GitHub repo and link it:
```
gh repo create <repo-name> --public --source=. --remote=origin --push
```

If a remote already exists, skip this step.

### 5. Configure Vite base path for GitHub Pages

GitHub Pages serves the site at `https://<username>.github.io/<repo-name>/`, so Vite must know the sub-path.

Read `vite.config.ts` and check if a `base` field already exists inside `defineConfig({...})`.
- If it does not exist, add `base: '/<repo-name>/',` as the first field inside `defineConfig({...})`.
- If it already exists with a different value, update it to `'/<repo-name>/'`.

### 6. Install gh-pages package if missing

Check `package.json` devDependencies for `gh-pages`. If absent, run:
```
npm install gh-pages --save-dev
```

### 7. Add deploy scripts to package.json

Open `package.json` and ensure the `scripts` section contains:
```json
"predeploy": "npm run build",
"deploy": "gh-pages -d build"
```
Add them if missing; do not overwrite existing unrelated scripts.

### 8. Commit config changes

Stage and commit any changes made in steps 5–7:
```
git add vite.config.ts package.json package-lock.json
git commit -m "Configure GitHub Pages deployment"
```
Skip this step if there are no changes to commit.

### 9. Push main branch and deploy

```
git push -u origin main
npm run deploy
```

The `predeploy` hook runs `npm run build` automatically, then `gh-pages` pushes the `build/` folder to the `gh-pages` branch.

### 10. Enable GitHub Pages via API (if not already enabled)

```
gh api repos/<username>/<repo-name>/pages \
  --method POST \
  -f "source[branch]=gh-pages" \
  -f "source[path]=/" 2>/dev/null || true
```

If the API returns an error saying Pages is already enabled, ignore it and continue.

### 11. Return the live URL

The GitHub Pages URL is:
```
https://<username>.github.io/<repo-name>/
```

Report this URL to the user. Note that GitHub Pages can take **1–2 minutes** to go live after the first deployment — tell the user to wait briefly before opening the link.
