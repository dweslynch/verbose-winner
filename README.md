# Code Scanner

A mobile web app that uses your device camera and OCR to find and collect 10-character alphanumeric codes. No build step — just a single HTML file.

## Live App

**https://dweslynch.github.io/verbose-winner/**

## Using the App

1. Open the URL on your phone
2. Grant camera permission when prompted
3. Wait for "Ready" status (OCR loads in a few seconds)
4. Point the camera at text containing a 10-character alphanumeric code
5. The "Detected:" line shows what the OCR is reading in real time
6. Any standalone 10-character alphanumeric token found is added to the list automatically (duplicates ignored)
7. Tap **Copy All** to copy the full list to clipboard, one code per line

## Browser Support

| Browser | Support |
|---|---|
| Chrome (Android) | Full |
| Safari 14.5+ (iOS) | Full |
| Chrome (Desktop) | Full — useful for testing with webcam |
| Firefox | Camera works; clipboard may require user gesture |

> **Note:** Camera access requires HTTPS. The GitHub Pages URL satisfies this. For local testing, `localhost` is treated as a secure origin by all major browsers.

## Local Development

No build tools required. Serve the file over HTTP (not `file://`, which blocks camera on some browsers):

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

## Deploying to GitHub Pages

1. Push `index.html` to the `main` branch
2. Go to **Settings → Pages** in the GitHub repo
3. Set Source to **Deploy from branch**, branch `main`, folder `/ (root)`
4. Save — the site is live at `https://<your-username>.github.io/<repo-name>/` within ~30 seconds

No GitHub Actions workflow is needed; GitHub Pages serves the static file directly.

## How It Works

- Camera feed is captured via `getUserMedia` with `facingMode: environment` (rear camera on mobile)
- Every second, a frame is drawn to an offscreen `<canvas>` and passed to [Tesseract.js](https://github.com/naptha/tesseract.js) for OCR
- The OCR output is searched with `/\b[A-Za-z0-9]{10}\b/g` — word boundaries ensure only standalone 10-character tokens are matched, not substrings of longer words
- Matched codes are deduplicated via a `Set` and prepended to the list (newest first)
- The raw detected text is shown below the status line so you can see exactly what the OCR is reading
