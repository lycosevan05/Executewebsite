# Project: executelabs.ca — legal/support pages

## Cloudflare Pages
- **Project name:** `executelabs`
- **Project type:** Direct Upload (drag-and-drop / Wrangler). NOT Git-connected.
- **Custom domain:** executelabs.ca

### Deploy command
⚠️ Do NOT run `wrangler pages deploy .` from this folder — it uploads EVERYTHING,
including this `CLAUDE.md` and `.gitignore`, which then become public at
executelabs.ca/CLAUDE.md etc. (`.assetsignore` is NOT honored by Pages deploy —
verified 2026-06-22). Deploy from a curated copy holding only the public files:

```bash
DEST=$(mktemp -d)
cp index.html privacy.html support.html terms.html house.html logo.png "app cover image.png" "$DEST"/
npx wrangler pages deploy "$DEST" --project-name=executelabs --branch=main   # main = production
rm -rf "$DEST"
```

> ⚠️ **Every Pages deploy REPLACES the entire project's files** — the deployed
> folder becomes the *complete* site, so include ALL public files listed above.
> Use `--branch=<other>` for a throwaway preview (e.g. `--branch=staging`) before
> production. Cloudflare injects email-obfuscation at the edge, so live pages
> differ from these source files only by a `/cdn-cgi/l/email-protection` link +
> an `email-decode.min.js` script — that is expected, not drift. Unknown paths
> fall through to `index.html` with a 200 (Pages default; there is no
> `_redirects`/`404.html`).

## URL mapping
Cloudflare Pages serves `.html` files at clean URLs automatically:
- `index.html`   → executelabs.ca/        (Legal landing page)
- `privacy.html` → executelabs.ca/privacy
- `support.html` → executelabs.ca/support
- `terms.html`   → executelabs.ca/terms
- `house.html`   → executelabs.ca/house  (shared house-hunting board; Supabase-backed, noindex)

(`/support.html` 308-redirects to `/support`, etc.)

## GitHub
- **Repo:** https://github.com/lycosevan05/Executewebsite
- **Branch:** main
- Push with: `git add -A && git commit -m "..." && git push`
- Note: GitHub repo is for version control / backup only. It is NOT wired to
  Cloudflare auto-deploy. Deploys happen via the `wrangler` command above.

## Current folder contents — THIS IS THE COMPLETE SITE
This `legal` folder is the single source of truth for the entire `executelabs`
Pages project. It contains all live pages:
- index.html   (homepage / Legal landing)
- privacy.html
- support.html
- terms.html
- house.html   (private house-hunting board "The Shortlist"; writes to Supabase
                table `public.house_listings` via the anon key. Public/no-login,
                noindex. Backed by migration 20260622000000_house_board.sql in the
                main app repo. See "House board" note below.)

Because a Pages deploy replaces everything, ALWAYS deploy this folder (which
holds all of them) — never a partial folder.

### History note
`index.html` was previously kept in a separate `../legal-site/` folder while
`support.html` lived here, so neither folder alone was a complete/safe deploy.
On 2026-06-05 they were consolidated: index.html was copied into this folder so
`legal/` now holds the full site. The old `../legal-site/` folder is redundant
and should not be deployed. privacy.html and terms.html are identical in both.

## House board (`house.html` → /house) — added 2026-06-22
Standalone page (own `<!DOCTYPE>`, no shared nav). Loads `@supabase/supabase-js@2`
from jsDelivr and reads/writes one row per listing in Supabase table
`public.house_listings` (project `shoflbdwlmvttidoolvd`). Identity is just a name
in `localStorage` (`house:me:v1`) — there is NO login.
- The Supabase URL + **anon** key are embedded in the page. The anon key is
  publishable (already shipped in the iOS/web app), but the table's RLS is
  **fully open to anon** (select/insert/update/delete `using (true)`), so anyone
  who loads /house can read and write the board. It is `noindex` (unlisted), not
  secret. Schema/RLS: `supabase/migrations/20260622000000_house_board.sql` in the
  main app repo (already applied to the linked project).
- Live sync via Supabase realtime (`postgres_changes` on the table) with a 30s
  poll fallback + Refresh button. Votes are stored as jsonb `{name: kind}`;
  voting does optimistic UI then a read-merge-write of only the voter's own key.
- To lock it down later: replace the open RLS policies with an auth check (and
  add login), or gate the page behind a shared passphrase.
