# grove.city — farewell static site (Cloudflare)

Standalone, self-contained static farewell page for `grove.city`, hosted on Cloudflare as a
Worker with static assets. No build step, no API, no Next.js.

- **Source of the page:** copied from `grove/app/public/index.html` (+ only the assets it references).
- **Deliberately excluded:** the fundraising deck, `llms*.txt`, and everything else in `app/public/`.

## Live now
- Worker: **grove-farewell** (account: Sabintsev Projects, `668edec31449b9257af49e7e42057877`)
- URL: **https://grove-farewell.sabintsev-projects.workers.dev**

## Re-deploy after editing `public/`
```bash
cd ~/Developer/grove-farewell
wrangler deploy
```

## Attach grove.city (do this once the domain is on Cloudflare)
Wrangler CANNOT add the domain or change nameservers — those are dashboard/registrar steps.

1. **Add the zone** — Cloudflare dashboard → Add a site → `grove.city` → Free plan. Cloudflare
   gives you two nameservers.
2. **Change nameservers at the registrar** (whoever holds grove.city) to the two Cloudflare NS.
   Wait for the zone to go "Active" (minutes–48h).
3. **Bind the custom domain to this Worker**, either:
   - **Dashboard:** Workers & Pages → `grove-farewell` → Settings → Domains & Routes →
     Add Custom Domain → `grove.city` (repeat for `www.grove.city`). Cloudflare auto-creates the
     DNS record + TLS cert.
   - **Wrangler** (only works after the zone exists in this account): add to `wrangler.jsonc`
     ```jsonc
     "routes": [{ "pattern": "grove.city", "custom_domain": true }]
     ```
     then `wrangler deploy`.
4. Remove the old `grove.city` / `app.grove.city` custom domains from Render so they stop answering.

## Notes
- `not_found_handling: single-page-application` → any unknown path serves the farewell letter
  (harmless for a one-page site). Change to `"none"` in `wrangler.jsonc` if you'd rather 404.
- To take it down: `wrangler delete` (removes the Worker), or unbind the custom domain.
