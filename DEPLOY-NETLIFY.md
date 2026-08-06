# Deploying 40daycampaigns.com to Netlify

This site is static HTML plus two small serverless functions (a Claude proxy and a
Stripe checkout creator). Netlify hosts both from this one folder. Nothing needs to be
built first — the site ships ready.

## 1. Put the folder on Netlify
Easiest: at app.netlify.com choose **Add new site → Deploy manually** and drag this
entire folder (the one containing `index.html`, `netlify.toml`, and `netlify/`) onto the
drop zone. Netlify reads `netlify.toml`, publishes the static files, and bundles the
functions automatically.

For ongoing edits, connect the folder to a Git repo instead and Netlify will redeploy on
every push. Leave the build command empty and the publish directory as `.` — both are
already set in `netlify.toml`.

## 2. Set four environment variables
Site settings → **Environment variables** → Add:

| Key | Value | Used by |
|---|---|---|
| `ANTHROPIC_API_KEY` | `sk-ant-…` (from console.anthropic.com) | Claude "Enhance" in the Builder |
| `STRIPE_SECRET_KEY` | `sk_live_…` (or `sk_test_…` to trial) | Card checkout |
| `SITE_URL` | `https://YOURSITE.netlify.app` (or your domain) | Stripe success/cancel redirects |
| `ALLOWED_ORIGIN` | same as SITE_URL (optional; locks the functions to your domain) | both functions |
| `FN_TIMEOUT_MS` | optional, default `9000` — raise to `24000` only if your Netlify plan allows 26s functions | Claude proxy |
| `MAX_TOKENS_CEILING` | optional, default `4096` — the per-call output ceiling | Claude proxy |

Redeploy once after setting them (Deploys → Trigger deploy) so the functions pick them up.

## 3. Point your domain (optional)
Domain settings → add `40daycampaigns.com`. Netlify issues the HTTPS certificate
automatically. Update `SITE_URL` to the custom domain afterward.

## 4. Stripe receipts (optional but recommended)
Card payments already work after step 2. To record paid orders and send real receipts,
create a webhook in the Stripe dashboard pointing at
`https://YOURSITE/.netlify/functions/stripe-webhook` for the event
`checkout.session.completed`, then add `STRIPE_WEBHOOK_SECRET = whsec_…` to your Netlify
env vars. The fulfillment hook is stubbed in `netlify/functions/stripe-webhook.js`.

## A note on function timeouts
Netlify stops a synchronous function at 10 seconds on the standard plan (26 on higher
plans). That is why the Campaign Engine never asks for a whole campaign in one call: it
splits the work into small passes — one session, one week of devotionals, one week of kit
assets at a time — each of which returns in a few seconds. An 8-week campaign runs as 34
passes, three at a time, with a live progress bar. If a single pass fails it retries once,
and if it still fails the build finishes without it and names the gap in the verification
box rather than discarding everything else.

## How it behaves before the keys are set
The site is fully usable the moment it's deployed. Until `ANTHROPIC_API_KEY` exists, the
Builder's three tools still generate complete drafts from the built-in Lifetogether engine
and simply skip the "Enhance with Claude" step with a clear message. Until
`STRIPE_SECRET_KEY` exists, card checkout records the order and shows the confirmation page
with its starter downloads. So you can launch, then switch on AI and payments whenever the
keys are ready — no code changes.

## What's where
- `netlify.toml` — publish dir, functions dir, `/api/*` routes, cache headers.
- `netlify/functions/claude.js` — server-side Anthropic proxy (whitelists model/tokens,
  never exposes the key).
- `netlify/functions/create-checkout-session.js` — re-prices the cart server-side and opens
  a Stripe Checkout session (client-sent amounts are ignored, so prices can't be tampered).
- `netlify/functions/stripe-webhook.js` — optional signed fulfillment hook.
- `netlify/functions/package.json` — declares `stripe`; Netlify installs it on deploy.

## Security notes
- API keys live only in Netlify env vars and only the functions read them; the browser
  never sees them.
- The checkout function recomputes every line's price from its own table, so a modified
  cart in the browser cannot change what Stripe charges.
- Set `ALLOWED_ORIGIN` to your domain to stop other sites from calling your functions.
