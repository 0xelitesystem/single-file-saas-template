# single-file-saas-template

A complete BYOK SaaS scaffolded into one HTML file. Free tier with daily limit, Pro tier unlocked by a license code, key never leaves the user's browser. Adapt by replacing the demo tool with your own logic.

**Live demo:** https://0xelitesystem.github.io/single-file-saas-template/

## Why this exists

You can ship a real, paid product as one HTML file. No backend, no database, no server costs. The user pays the LLM provider directly (their key, their bill); you charge a one-time license fee for unlimited usage and the convenience of your tool.

This pattern works for indie SaaS in the Pieter Levels style. It is not the right pattern for everything: enterprise needs SSO, teams, audit logs. Single-user developer tools and creator products fit it well.

## What's in this template

A working scaffold that demonstrates:

- **Plan selection**: Free vs Pro card UI with active-state highlighting
- **License activation**: client-side code validation against a regex pattern (replace with your own server check for real revenue)
- **BYOK key entry**: validates the user's Anthropic key with a 1-token request before saving
- **In-memory state**: nothing in localStorage, sessionStorage, or cookies
- **Daily limit on free tier**: counts calls per day, resets on calendar day rollover
- **Reference tool**: an "AI text rewriter" you can replace with your real product
- **Light and dark themes** with OS preference detection
- **Editorial / mid-century aesthetic**: warm off-white, deep navy, mustard accent, serif headlines

The whole thing is one HTML file, around 700 lines. Open it in any browser, no build step.

## Use

Open `index.html` in a browser. Or visit the hosted demo at `https://0xelitesystem.github.io/single-file-saas-template/` once GitHub Pages is enabled.

For testing the Pro flow: use a code matching `PRO-XXXX-XXXX-XXXX` (any letters/digits). Real implementations should verify codes against a server.

Steps:

1. Open `index.html`.
2. Under Choose a plan, click Use free tier, or click I have a code, enter a code in the format `PRO-XXXX-XXXX-XXXX`, and click Activate.
3. Under Connect your key, paste an Anthropic API key and click Validate & save.
4. Under Use the tool, paste text into Text to rewrite and click Rewrite.

## Adapt this for your product

1. **Replace the demo tool.** Edit the `CONFIG.systemPrompt` constant and the `callApi()` function. Anything that takes user input, sends it to Anthropic, and shows a result fits this slot.
2. **Replace the Stripe link.** The `<a class="primary" href="https://buy.stripe.com/...">` is a placeholder. Use a real Stripe Checkout link or Payment Link.
3. **Decide your license check.** Three escalating options:
   - **Pattern-only (current)**: client-side regex. Easy to bypass; fine for honor-system pricing.
   - **Server check**: small endpoint (Cloudflare Worker, Vercel function) that validates codes against your DB. Add a `fetch()` call in `license-submit-btn` handler.
   - **Stripe webhook**: when a Checkout completes, your webhook generates a code and emails it. The user pastes it. Real revenue protection without auth complexity.
4. **Decide your free tier limit.** `CONFIG.freeTierDailyLimit`. The current limit (5/day) is illustrative. Some products will charge from call 1; others will be free up to 50/day to attract users.
5. **Replace the colors.** All design tokens are CSS variables in `:root`. Search and replace.
6. **Replace the headline and subtitle.** Text in `<h1>` and `.subtitle`.

## Why no localStorage?

This template intentionally holds state (key, plan, daily count) in JavaScript variables only. Two reasons:

- **Security**: API keys in localStorage are vulnerable to XSS. In-memory keys are wiped on tab close.
- **Honesty**: a free tier limit stored in localStorage is one DevTools click away from being reset. Persisting it gives users a false sense of "the system tracked me." If you want a real persistent limit, you need a server.

If your specific product needs persistence (e.g. saved drafts of user content), add localStorage selectively for non-sensitive data only.

## Hosting

Static HTML, deploys anywhere:

- **GitHub Pages** (free, what's recommended): push to a repo, enable Pages, done.
- **Netlify / Vercel / Cloudflare Pages**: drag-and-drop or `git push`.
- **Your own domain**: any static host works. S3, Firebase, surge, anything.

Your only ongoing cost is the domain (~$10/yr) if you want a custom URL. Otherwise it's free to host indefinitely.

## What it doesn't do

- No user accounts. The user is whoever is sitting at the browser.
- No analytics. Add your own if you want them; just be aware that free analytics scripts are XSS surface for the API key.
- No multi-user state. Single-user tool.
- No real backend. Everything is in the browser plus calls to Anthropic.
- No server-side license verification. Add one if revenue protection matters.

## Privacy

Your API key, plan, and daily call count live in JavaScript memory in this tab only. Nothing is written to localStorage, sessionStorage, or cookies, so closing or refreshing the tab clears them. The license code is checked against a pattern inside the page and is not sent anywhere.

The page does make network calls, all sent directly from your browser to `https://api.anthropic.com/v1/messages` using your key: one 1-token request when you click Validate & save, and one request carrying your text and the template's system prompt each time you click Rewrite. No other server receives anything. The Buy via Stripe button is a placeholder link that only opens if you click it. There are no analytics.

## Run locally

```
git clone https://github.com/0xelitesystem/single-file-saas-template
cd single-file-saas-template
```

Open `index.html` in a browser, or serve the folder with `python -m http.server` and visit http://localhost:8000.

## Build

No build step. The whole tool is one `index.html` file.

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT. See [LICENSE](LICENSE).

## Related

- [byok-patterns](https://github.com/0xelitesystem/byok-patterns), three deeply-built BYOK reference implementations
- [claude-eval-harness](https://github.com/0xelitesystem/claude-eval-harness), compare Claude models side-by-side
- [prompt-cost-calculator](https://github.com/0xelitesystem/prompt-cost-calculator), estimate token cost across providers
- [prompt-templates](https://github.com/0xelitesystem/prompt-templates), production prompts targeting LLM failure modes
