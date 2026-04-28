# TerraChat — Terraform AI Assistant
 
A minimal chat interface for Terraform questions. Covers AWS, Azure, and GCP providers.
 
## What it does
- Answers Terraform questions only (HCL syntax, providers, modules, state, CLI, etc.)
- Returns working copy-paste HCL examples
- Multi-turn conversation (in-memory, no storage)
- Rejects non-Terraform questions automatically
## Stack
- Single HTML file — zero dependencies, zero build step
- Uses Anthropic Claude API (`claude-sonnet-4`) directly from the browser
- Hosted as a static site on Vercel
---
 
## Setup
 
### 1. Get an Anthropic API key
Go to [console.anthropic.com](https://console.anthropic.com) → API Keys → Create key.
 
### 2. Set up Vercel Environment Variable
The app calls the Anthropic API from the browser. To avoid exposing your key in source code:
 
**Option A — Vercel Environment Variable (recommended)**
 
Add to your `vercel.json`:
```json
{
  "env": {
    "ANTHROPIC_API_KEY": "@anthropic_api_key"
  }
}
```
Then in Vercel dashboard → Settings → Environment Variables, add:
- Key: `anthropic_api_key`
- Value: `sk-ant-...`
Then in `index.html`, replace the fetch headers section with:
```js
headers: {
  'Content-Type': 'application/json',
  'x-api-key': '%%ANTHROPIC_API_KEY%%'  // injected at build
}
```
 
**Option B — Simple approach (for personal/private use)**
 
Hardcode your key directly in `index.html` in the fetch call:
```js
headers: {
  'Content-Type': 'application/json',
  'x-api-key': 'sk-ant-YOUR_KEY_HERE',
  'anthropic-version': '2023-06-01'
}
```
 
> ⚠️ Option B is fine for a private repo. Do NOT commit a key to a public repo.
 
### 3. Deploy to Vercel
 
```bash
# Install Vercel CLI
npm i -g vercel
 
# From the project folder
vercel deploy
```
 
Or connect your GitHub repo to Vercel — it auto-deploys on every push.
 
### 4. Deploy to GitHub Pages (alternative)
 
```bash
git init
git add .
git commit -m "init"
git remote add origin https://github.com/YOUR_USERNAME/terraform-ai.git
git push -u origin main
```
 
Then in GitHub repo → Settings → Pages → Source: `main` branch, root `/`.
 
---
 
## Project structure
 
```
terraform-ai/
├── index.html     # entire app — UI + logic
├── vercel.json    # Vercel routing config
└── README.md
```
 
## Note on API key security
 
This is a **client-side app** — the API key is in the browser. For personal/internal use this is acceptable. For a public site, build a small backend proxy (Vercel Edge Function, Cloudflare Worker, etc.) that holds the key server-side and forwards requests to Anthropic.
 
