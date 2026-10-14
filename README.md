# inner-map-legal

Public hosting for the legal documents of the **Inner Map** mobile
application, published by **Innermap LLC**, a Florida limited
liability company.

This repository hosts the Privacy Policy + Terms of Service as static
HTML for the App Store / Google Play listings AND the magic-link
sign-in landing page + Universal-Link / App-Link discovery files.
Deployed via **Cloudflare Pages** to https://my-inner-map.com.

## Contents

| Path | Purpose |
|------|---------|
| `index.html` | Landing page linking to legal documents |
| `privacy-policy.html` | Inner Map Privacy Policy |
| `terms-of-service.html` | Inner Map Terms of Service |
| `auth/email/index.html` | Magic-link sign-in landing — deep-links into the native app via `innermap://auth/email?token=…` |
| `.well-known/apple-app-site-association` | iOS Universal Link declaration (paths `/auth/*` route to the app silently) |
| `.well-known/assetlinks.json` | Android App Link declaration (same role on Android) |
| `_headers` | Cloudflare Pages config — serves both `.well-known` files as `application/json` |
| `support.html` | Support page — the App Store requires a reachable one |

The legal-document HTML files as authored here are standalone — no external
CSS, JavaScript, fonts, or other dependencies.

**And as of 2026-10-14 the SERVED pages are standalone too** — verified, not
assumed: zero `<script>` tags of any kind, zero `/cdn-cgi/` references, and all
four contact addresses rendering as readable text.

This was not true until that date. Cloudflare's Scrape Shield → Email Address
Obfuscation had been rewriting every contact address in the delivered HTML to
`[email protected]` and injecting `/cdn-cgi/scripts/.../email-decode.min.js` to
put them back — so the privacy policy executed a third-party script, and
`privacy@my-inner-map.com`, the address a data subject needs to exercise a GDPR
right, was unreadable without JavaScript. That setting is now off. **If anyone
ever turns it back on, the standalone claim above goes false again.** Check with:

    curl -sL https://my-inner-map.com/privacy-policy.html | grep -c email-decode

## Everything in this repository is published

Cloudflare Pages serves this root wholesale. `_headers` sets content types
and excludes nothing, there is no `_routes.json`, and this repository is
public on GitHub as well. Any file committed here is readable at
`https://my-inner-map.com/<path>` whether or not the table above lists it and
whether or not anything links to it.

This repository is for the published site only. Working notes, drafts,
internal memos and correspondence belong in a private repository. If a file
is not something you would hand to a stranger who typed its name into the
address bar, it does not go here.

## Magic-link sign-in setup

The `auth/email/index.html` fallback page works immediately — when
the user clicks the magic-link email, the browser opens this page,
JavaScript redirects to `innermap://auth/email?token=…`, and the
native app's deep-link handler completes sign-in. No app-side
configuration required beyond the custom-scheme registration (which
ships in `app.config.js`'s `scheme: 'innermap'`).

The `.well-known` files upgrade the UX from "browser opens → page
redirects → app launches" (one extra browser flash) to "tap link →
app opens silently". This requires filling in two production
credentials in placeholder form:

### 1. `apple-app-site-association` — DONE (2026-09-15)

`appIDs` now reads `ZC33M6LPYR.com.srulischor.innermap`. The Team ID came
off the EAS build credentials summary, which prints it as
`Apple Team ZC33M6LPYR (Innermap LLC (Company/Organization))` — the same
value App Store Connect shows under Membership Details → Team ID.

If it ever needs re-checking:

```
# On the build machine (must have EAS CLI installed + logged in)
eas credentials -p ios
```

The `appIDs` entry must stay `TEAM_ID.com.srulischor.innermap` — a dot
and nothing else between the two halves.

### 2. `assetlinks.json` — replace the placeholder fingerprint

Find the Android SHA-256 fingerprint of the production signing cert:

```
eas credentials -p android

# Navigate: "production" build profile → "Keystore" → "Show keystore
# information". Look for "SHA256 Fingerprint".
# Format: 32 hex bytes separated by colons, e.g.:
#   AB:CD:EF:01:23:45:67:89:AB:CD:EF:01:23:45:67:89:AB:CD:EF:01:23:45:67:89:AB:CD:EF:01:23:45:67:89
```

Paste the fingerprint (with colons) into the
`sha256_cert_fingerprints` array.

### Deploy + verify

After filling in both values:

```bash
git add .well-known/
git commit -m "magic-link: fill in production Team ID + SHA-256"
git push origin main
# Cloudflare Pages redeploys automatically.
```

Verify the files serve correctly:

```bash
curl -sI https://my-inner-map.com/.well-known/apple-app-site-association
# Must include: content-type: application/json

curl -sI https://my-inner-map.com/.well-known/assetlinks.json
# Must include: content-type: application/json
```

On iOS, Universal Links register at install time — uninstall +
reinstall the app to refresh. On Android, App Link verification
happens on app install; check via `adb shell pm get-app-links
com.srulischor.innermap` (should show `verified`).

## Hosting

The documents are served via **GitHub Pages**. Once Pages is enabled
for this repository (Settings → Pages → Deploy from branch `main`,
root folder), the documents are available at:

- https://srulischor-innner.github.io/inner-map-legal/privacy-policy.html
- https://srulischor-innner.github.io/inner-map-legal/terms-of-service.html

## Updating

Edit the relevant `.html` file directly, update the **Last Updated**
date near the top of the document, commit, and push to `main`. GitHub
Pages redeploys automatically.

Document substance should be reviewed by counsel before any material
change ships.

## Contact

- Privacy questions: privacy@my-inner-map.com
- Legal / compliance: legal@my-inner-map.com
- General support: support@my-inner-map.com
