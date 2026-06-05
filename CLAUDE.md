# Project: executelabs.ca — legal/support pages

## Cloudflare Pages
- **Project name:** `executelabs`
- **Project type:** Direct Upload (drag-and-drop / Wrangler). NOT Git-connected.
- **Custom domain:** executelabs.ca

### Deploy command (run from THIS folder)
```bash
npx wrangler pages deploy . --project-name=executelabs
```

> ⚠️ **Every Pages deploy REPLACES the entire project's files.** Whatever folder
> you deploy becomes the *complete* site. Only deploy the folder that contains
> the full intended set of files for the `executelabs` project.

## URL mapping
Cloudflare Pages serves `.html` files at clean URLs automatically:
- `index.html`   → executelabs.ca/        (Legal landing page)
- `privacy.html` → executelabs.ca/privacy
- `support.html` → executelabs.ca/support
- `terms.html`   → executelabs.ca/terms

(`/support.html` 308-redirects to `/support`, etc.)

## GitHub
- **Repo:** https://github.com/lycosevan05/Executewebsite
- **Branch:** main
- Push with: `git add -A && git commit -m "..." && git push`
- Note: GitHub repo is for version control / backup only. It is NOT wired to
  Cloudflare auto-deploy. Deploys happen via the `wrangler` command above.

## Current folder contents — THIS IS THE COMPLETE SITE
This `legal` folder is the single source of truth for the entire `executelabs`
Pages project. It contains all four live pages:
- index.html   (homepage / Legal landing)
- privacy.html
- support.html
- terms.html

Because a Pages deploy replaces everything, ALWAYS deploy this folder (which
holds all four files) — never a partial folder.

### History note
`index.html` was previously kept in a separate `../legal-site/` folder while
`support.html` lived here, so neither folder alone was a complete/safe deploy.
On 2026-06-05 they were consolidated: index.html was copied into this folder so
`legal/` now holds the full site. The old `../legal-site/` folder is redundant
and should not be deployed. privacy.html and terms.html are identical in both.
