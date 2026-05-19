# inner-map-legal

Public hosting for the legal documents of the **Inner Map** mobile
application, published by **Innermap LLC**, a Florida limited
liability company.

This repository exists solely to host the Privacy Policy and Terms of
Service as static HTML so they can be linked from the App Store /
Google Play listings and from inside the app. It contains no
application code.

## Contents

| File | Purpose |
|------|---------|
| `index.html` | Landing page linking to both documents |
| `privacy-policy.html` | Inner Map Privacy Policy |
| `terms-of-service.html` | Inner Map Terms of Service |

The HTML files are standalone — no external CSS, JavaScript, fonts,
or other dependencies. Each renders correctly opened directly in a
browser.

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
