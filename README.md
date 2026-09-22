# Lightbox Draft — OpenAI edition

A single-file, self-contained proof-of-concept web app: upload a CT image, get an AI-drafted
structured radiology report (EXAM / CLINICAL HISTORY / TECHNIQUE / COMPARISON / FINDINGS /
IMPRESSION / LIMITATIONS), edit it, and export a PDF.

This build calls **OpenAI's API directly from the browser** using a key you provide — it does not
go through Claude or any server of ours.

## Running it

There's nothing to install. Two options:

1. **Just open it.** Double-click `lightbox-draft-openai.html` (or drag it into a browser window).
   This works in Chrome, Edge, and Firefox.
2. **Serve it locally** (optional, avoids rare `file://` quirks in some browsers):
   ```
   cd lightbox-draft-openai
   python3 -m http.server 8000
   ```
   then open `http://localhost:8000/lightbox-draft-openai.html`.

To share it with someone else (e.g. a radiologist friend for feedback), just send them this HTML
file, or host it on any static file host (GitHub Pages, Netlify, S3, etc.) and send the link. Each
person enters their **own** OpenAI API key in the app — the key never leaves their browser except
to call `api.openai.com` directly.

## Using it

1. Paste an OpenAI API key (starts with `sk-`) into the "OpenAI API key" box in the left panel.
   Get one at [platform.openai.com/api-keys](https://platform.openai.com/api-keys). The model
   field defaults to `gpt-4o` (vision-capable); change it if you want to try another vision model
   your account has access to (e.g. `gpt-4o-mini` for a cheaper/faster pass).
2. "Remember on this device" (checked by default) saves the key in the browser's local storage so
   you don't have to re-enter it. Uncheck it, or click "Clear saved key", if you'd rather it only
   live in memory for the current tab.
3. Upload or drag in a CT image (a single exported slice/photo — JPG or PNG, no visible patient
   identifiers).
4. Click **Analyze with AI**. You'll see the model's answer stream into the "AI output" panel live,
   then get split into the report fields on the right as it finishes.
5. Edit anything — the AI draft is a starting point, not a final answer.
6. Click **Download PDF** to export.

## Things worth knowing before you share this

- **The API key lives in the browser only.** It's sent straight to OpenAI over HTTPS on every
  analyze call; it's never sent to us or to any other server. Because it's client-side JavaScript,
  anyone with access to that browser tab's dev tools could see the key — fine for a private POC
  test with a trusted colleague, not something to put on a public link with a key you care about.
  Each tester should use their own key.
- **Cost is per-call, on the key owner's OpenAI account.** Every "Analyze" or "Regenerate" click is
  a normal OpenAI API request billed to whoever's key is entered.
- This is still the same proof-of-concept disclaimers as before: not a medical device, not
  validated for diagnosis, every field needs a licensed radiologist's review before it means
  anything.

## What changed from the Claude-artifact version

The earlier version of this app ran inside a published Claude artifact and used Claude's built-in
`sample()`/`downloads()` capabilities, which only work when the page is loaded through claude.ai.
This version is fully standalone: it calls OpenAI's `chat.completions` endpoint directly with
`fetch()`, streams the response the same way, and uses a plain browser download instead of the
artifact's downloads capability — so it runs anywhere, no claude.ai account or viewer needed.
