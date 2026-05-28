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

The legal-document HTML files are standalone — no external CSS,
JavaScript, fonts, or other dependencies.

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

### 1. `apple-app-site-association` — replace `REPLACE_WITH_APPLE_TEAM_ID`

Find the Apple Team ID:

```
# On the build machine (must have EAS CLI installed + logged in)
eas credentials -p ios

# Look for "Apple Team ID" in the output. Format: 10 alphanumeric
# characters, e.g. "A1B2C3D4E5".
```

OR from App Store Connect → Membership Details → Team ID.

The final `appIDs` entry must read `TEAM_ID.com.srulischor.innermap`
(no separator other than the dot).

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
