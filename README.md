# Hero Wallet Deeplinks

Static deeplink bounce pages for the Hero Wallet ecosystem. Each page reads the
query string, redirects to the app's custom scheme, and falls back to a tappable
button after 1.5s if the app did not open.

These pages exist so that links shared in WhatsApp (and other apps that only
linkify `https://`) are tappable. They are plain static HTML — no build step.

## Contents

| Folder     | Scheme                                            | App           |
| ---------- | ------------------------------------------------- | ------------- |
| `contact/` | `herowallet://contact?id=`                        | Hero Wallet   |
| `pay/`     | `herowallet://pay?id=`                            | Hero Wallet   |
| `staff/`   | `herowalletbusiness://staff?token=&merchantId=`   | Hero Business |

`staff/` additionally renders a desktop view with a QR code and an APK download
link, since staff invites are often opened on a laptop.

## Hosting

Uploaded to S3 behind CloudFront, one bucket per environment:

| Environment | Bucket                            |
| ----------- | --------------------------------- |
| development | `deeplink.herowallet.development` |

Upload is currently manual. The folder layout in this repo mirrors the bucket
layout exactly, so the whole tree can be synced as-is:

```bash
aws s3 sync . s3://deeplink.herowallet.development \
  --exclude "README.md" --exclude ".git/*"
```

## Consumers

- Hero Wallet — `lib/core/config/deep_links.dart` builds the URLs;
  `lib/core/services/deep_link_service.dart` handles the incoming scheme.
- Hero Business — `lib/core/services/deep_link_service.dart` handles
  `herowalletbusiness://staff`.

Android registers each scheme via `intent-filter` in `AndroidManifest.xml`.

## Known issues

- **Dead QR endpoint.** `staff/index.html` builds its QR via
  `chart.googleapis.com/chart?cht=qr`, which Google has shut down. The image
  does not render; only the text APK link below it works.
- **Hardcoded APK link.** The Hero Business APK points at a fixed Google Drive
  *preview* URL, not a direct download. Do not copy `staff/index.html` to
  sandbox or production as-is.
- **Tablet detection.** The desktop check is `innerWidth > 768` plus a
  `Mobi|Android` UA test, so iPads get the desktop QR view instead of the
  deeplink attempt.
- **No verified App Links.** These pages rely on custom-scheme redirects. There
  is no `assetlinks.json` or `apple-app-site-association` anywhere in the
  ecosystem, so links are not verified by the OS.
