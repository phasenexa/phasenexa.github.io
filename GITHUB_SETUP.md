# GitHub Repository Setup

Manual steps required to get the `phasenexa.github.io` website repo working.

## 1. Create the repository

Create a **public** repository named `phasenexa.github.io` under the `phasenexa` GitHub organisation.

- Repository name: `phasenexa.github.io`
- Visibility: **Public** (required for GitHub Pages on free tier)
- Description: `Developer tools for European energy trading. Open source libraries for market data, bid generation, and exchange connectivity.`
- Do NOT initialise with README (you will push these files)

### Topics / Keywords

Add these topics to the repo for discoverability:

```
energy-trading, electricity-market, nordpool, entso-e, euphemia,
epex-spot, power-markets, developer-tools, european-energy,
day-ahead, intraday, bidding-zones, 15-minute-mtu, python, golang
```

## 2. Push initial content

```bash
cd phasenexa.github.io
git init
git add .
git commit -m "feat: initial website scaffold"
git branch -M main
git remote add origin git@github.com:phasenexa/phasenexa.github.io.git
git push -u origin main
```

## 3. Add the Anthropic API key secret

This is needed for both the `build.yml` (Claude Code content updates) and `claude.yml` (@claude mentions) workflows.

1. Go to **Settings > Secrets and variables > Actions**
2. Click **New repository secret**
3. Name: `ANTHROPIC_API_KEY`
4. Value: your Anthropic API key
5. Click **Add secret**

## 4. Run the build workflow

1. Go to **Actions** tab
2. Select **Build & Deploy Website**
3. Click **Run workflow**
4. Choose whether to update content from repos (first run can skip this since repos may not exist yet)
5. Click **Run workflow**

This will build the Retype site and push the output to a `retype` branch.

## 5. Configure GitHub Pages

1. Go to **Settings > Pages**
2. Source: **Deploy from a branch**
3. Branch: `retype` / `/ (root)`
4. Click **Save**
5. Check **Enforce HTTPS**

Your site will be available at `https://phasenexa.github.io` within a few minutes.

## 6. Optional: Retype Community Key

Retype offers a free Community Key for GitHub Pages hosted projects that unlocks Pro features (dark mode default, breadcrumbs, etc.).

1. Visit [retype.com/community](https://retype.com/community/) and follow the instructions
2. Add the key as a repo secret named `RETYPE_KEY`
3. The `build.yml` workflow already supports this - uncomment the env block in the Retype build step

To enable this, edit `.github/workflows/build.yml` and add to the Retype build step:

```yaml
- name: Build Retype website
  uses: retypeapp/action-build@latest
  env:
    RETYPE_KEY: ${{ secrets.RETYPE_KEY }}
```

## 7. Optional: Install Claude GitHub App

For the `@claude` mention workflow to work fully:

1. Install the Claude GitHub App: [github.com/apps/claude](https://github.com/apps/claude)
2. Grant it access to the `phasenexa.github.io` repository
3. Alternatively, the `claude.yml` workflow uses `anthropics/claude-code-action@v1` with your API key, which works without the app

## Branch protection (recommended)

Set up branch protection on `main`:

1. Go to **Settings > Branches > Add rule**
2. Branch name pattern: `main`
3. Enable:
   - Require pull request reviews before merging
   - Require status checks to pass (once CI is running)
   - Require branches to be up to date
4. Click **Create**

Do NOT protect the `retype` branch - the GitHub Action needs to push to it.

## Repository settings

Recommended repo settings:

- **Features**: Enable Issues, disable Wiki (docs are on the site), disable Projects
- **Pull Requests**: Allow squash merging only, enable auto-delete head branches
- **Social preview**: Upload the Phase Nexa logo as the social preview image (Settings > General > Social preview)
