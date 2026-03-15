# BunkerWeb Community Board

A zero-dependency, single-page GitHub dashboard for the [BunkerWeb](https://github.com/bunkerity/bunkerweb) ecosystem. Browse issues and pull requests from the community, view repository stats, and manage your own GitHub activity — all from one static HTML file, no backend required.

---

## Features

- **Read without login** — Issues and pull requests from any configured repository are publicly visible
- **Write with login** — Creating issues, editing, commenting, merging, and reviewing PRs require GitHub authentication
- **BunkerWeb spotlight** — Live stats (stars, forks, open issues, open PRs) pulled from the main repo
- **Community board** — Configurable list of repositories displayed as project cards
- **Device flow OAuth** — Authenticate with GitHub directly from the browser, no backend or client secret needed
- **Zero dependencies** — Pure HTML/CSS/JS, single file, deployable anywhere static files are served

---

## Project Configuration

The list of community repositories shown on the board is driven by a JSON array. Edit the `COMMUNITY_REPOS` constant near the top of `index.html`:

```json
[
  {
    "owner": "bunkerity",
    "repo":  "bunkerweb",
    "url":   "https://github.com/bunkerity/bunkerweb"
  },
  {
    "owner": "corazawaf",
    "repo":  "coraza",
    "url":   "https://github.com/corazawaf/coraza"
  },
  {
    "owner": "SpiderLabs",
    "repo":  "ModSecurity",
    "url":   "https://github.com/SpiderLabs/ModSecurity"
  }
]
```

Each entry maps directly to a GitHub repository URL of the form:

```
https://github.com/{owner}/{repo}
```

The dashboard fetches live data (stars, forks, open issue count, open PR count) from the GitHub API for each entry. The primary source for the BunkerWeb spotlight counters shown on the dashboard is:

```
https://github.com/bunkerity/bunkerweb
```

---

## Read vs. Write Access

| Action | Login required |
|--------|---------------|
| View issues & pull requests | No |
| View repository stats & commits | No |
| View release notes | No |
| Create an issue | **Yes** |
| Edit / close an issue | **Yes** |
| Comment on an issue | **Yes** |
| Create / request a PR review | **Yes** |
| Merge a pull request | **Yes** |

---

## Getting Write Access — Setup Guide

Authentication uses the **GitHub Device Flow** — no backend server or client secret is required, only a GitHub OAuth App `client_id`.

### Step 1 — Create a GitHub OAuth App

1. Go to **GitHub → Settings → Developer settings → OAuth Apps → New OAuth App**
   (direct link: `https://github.com/settings/applications/new`)
2. Fill in the form:
   | Field | Value |
   |-------|-------|
   | Application name | `BunkerWeb Community Board` (or any name) |
   | Homepage URL | URL where you host `index.html` (e.g. `https://yourname.github.io/bunkerweb-community-board`) |
   | Authorization callback URL | Same URL as homepage |
3. Click **Register application**.
4. On the next screen, copy the **Client ID** (20-character string starting with `Iv1.` or similar).

> You do **not** need to generate a client secret — device flow only uses the client ID.

### Step 2 — (Optional) Hardcode the Client ID

Open `index.html` and find the `CONFIG` section near the top:

```js
const CLIENT_ID_FALLBACK = '';   // ← paste your client ID here
```

If left empty, the login modal will prompt the user to enter their own client ID each time.

### Step 3 — Log In via the Board

1. Open the board in your browser.
2. Click **Login** in the top-right corner.
3. Paste your Client ID if not pre-filled.
4. A device code is displayed. Click **Open GitHub** (or visit `https://github.com/login/device`).
5. Enter the device code on GitHub and click **Authorize**.
6. The board automatically detects the approval and stores the access token in `localStorage`.

Your session persists across page reloads until you explicitly log out.

---

## Deployment

### GitHub Pages (recommended)

The repository includes a ready-made workflow at [.github/workflows/deploy.yml](.github/workflows/deploy.yml).

1. Push `index.html` to the `main` branch of your repository.
2. Go to **Settings → Pages** and select the `gh-pages` branch as the source (the workflow creates it automatically).
3. The board will be live at `https://{your-username}.github.io/{repo-name}/`.

### Any Static Host

Because the entire app is a single `index.html` file, it can be hosted on any static file server:

```bash
# Python quick server
python3 -m http.server 8080

# Node / npx
npx serve .
```

---

## GitHub API Rate Limits

Unauthenticated requests are limited to **60 requests/hour** per IP. Logging in raises this to **5,000 requests/hour**. The current rate limit status is displayed in the top navigation bar after login.

---

## Required OAuth Scopes

When the OAuth App is created and a user logs in, the following scopes are requested:

| Scope | Purpose |
|-------|---------|
| `repo` | Read private repos, issues, PRs |
| `issues:write` | Create and edit issues |
| `pull_requests:write` | Comment on and merge pull requests |
| `read:user` | Display username and avatar |

---

## License

See the main [BunkerWeb repository](https://github.com/bunkerity/bunkerweb) for license information.
