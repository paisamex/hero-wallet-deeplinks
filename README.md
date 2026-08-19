# Hero Wallet Deeplinks

This repository contains static deeplink landing pages for Hero Wallet and Hero Business. Each page reads the query string, tries to open the native app through a custom URL scheme, and shows a fallback button if the app does not launch.

These pages are used when a link is shared through WhatsApp or other platforms that only preserve an HTTPS URL. They are plain static HTML files with no build step.

## Pages

| Path | Example | Purpose |
| ---- | ------- | ------- |
| `contact/` | `/contact/?id=123` | Opens `herowallet://contact?id=123` |
| `pay/` | `/pay/?id=123` | Opens `herowallet://pay?id=123` |
| `staff/` | `/staff/?token=abc&merchantId=42` | Opens `herowalletbusiness://staff?token=abc&merchantId=42` |

The `staff/` page also renders a desktop-friendly view with a QR code and an APK download link for laptop opens.

## Local testing

Serve the folder locally and open the pages in a browser:

```bash
python3 -m http.server 8000
```

Then visit:

- http://localhost:8000/contact/?id=demo
- http://localhost:8000/pay/?id=demo
- http://localhost:8000/staff/?token=demo&merchantId=1

## Deployment

The repository mirrors the S3/CloudFront bucket layout. Uploads are currently manual:

```bash
aws s3 sync . s3://deeplink.herowallet.development \
  --exclude "README.md" --exclude ".git/*"
```

