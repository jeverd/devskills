---
name: html-share
description: Create HTML pages/plans and share them publicly via bore tunnel
category: dev
tags: [html, bore, tunnel, sharing, plans, pages, mockups]
---

# html-share

Create standalone HTML pages and share them with anyone via a public tunnel. No hosting, no deploy pipeline — write HTML, serve it, tunnel it.

## When to use

- Sharing a plan, proposal, or design mockup as a live web page
- Sending a clickable link to a teammate for review
- Quick visual docs that look better than markdown
- Prototyping a UI concept and getting eyes on it fast
- Any time you want "a URL I can send someone" for local HTML

## Steps

### 1. Create the HTML page

Write a self-contained HTML file. Everything inline — no external CSS files, no build step.

Minimal starter:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Your Title</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      -webkit-font-smoothing: antialiased;
      color: #1a1a1a;
      background: #fafafa;
      line-height: 1.6;
      max-width: 800px;
      margin: 0 auto;
      padding: 2rem;
    }
    h1 { margin-bottom: 1rem; }
    h2 { margin: 1.5rem 0 0.5rem; color: #333; }
    p { margin-bottom: 1rem; }
    code { background: #f0f0f0; padding: 2px 6px; border-radius: 4px; font-size: 0.9em; }
    .meta { color: #666; font-size: 0.85rem; margin-bottom: 2rem; }
    .section { margin-bottom: 2rem; }
  </style>
</head>
<body>
  <h1>Your Plan Title</h1>
  <div class="meta">Created {{date}} · Shared via bore</div>

  <div class="section">
    <h2>Section heading</h2>
    <p>Content here.</p>
  </div>
</body>
</html>
```

For richer pages with layout, cards, or interactive elements, use Tailwind via CDN:

```html
<script src="https://cdn.tailwindcss.com"></script>
```

Tips for good shareable pages:
- **Inline everything** — styles, scripts, fonts. The page must work with zero local dependencies.
- **Real content, not lorem ipsum** — actual words, actual data. People review content, not placeholders.
- **Self-documenting** — include a title, date, and author at the top. The person opening the link should know what they're looking at.
- **Mobile-friendly** — add the viewport meta tag. People open links on phones.

### 2. Serve the page locally

Start a static file server from the directory containing your HTML:

```bash
# Python (available everywhere)
cd /path/to/your/html
python3 -m http.server 9090

# Or: Node.js
npx serve -l 9090 .

# Or: Ruby
ruby -run -e httpd . -p 9090
```

Verify locally:

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:9090/your-page.html
# Should return: 200
```

Open in your browser to sanity-check:

```bash
# macOS
open http://localhost:9090/your-page.html

# Linux
xdg-open http://localhost:9090/your-page.html
```

### 3. Expose via bore tunnel

Install bore if you don't have it:

```bash
# macOS
brew install bore-cli

# Or via cargo
cargo install bore-cli
```

Start the tunnel:

```bash
bore local 9090 --to bore.pub
```

This outputs something like:

```
INFO bore_cli::client: connected to server remote_port=42873
```

Your page is now live at: `http://bore.pub:42873/your-page.html`

For a predictable port:

```bash
bore local 9090 --to bore.pub --port 55000
# → http://bore.pub:55000/your-page.html
```

Verify the tunnel:

```bash
curl -s -o /dev/null -w "%{http_code}" http://bore.pub:42873/your-page.html
# Should return: 200
```

### 4. Share the link

Send the full URL: `http://bore.pub:PORT/your-page.html`

Notes:
- The link works as long as your local server and bore are running
- bore.pub has no HTTPS — the connection is unencrypted
- For internal/team sharing this is fine. For public-facing or sensitive content, consider a self-hosted bore server or ngrok

### 5. Clean up

When done, kill the server and tunnel:

```bash
# Find and kill
lsof -ti:9090 | xargs kill 2>/dev/null
# bore exits when you Ctrl+C its process
```

## Common page patterns

### Project plan / proposal

```html
<body>
  <h1>Project Name — Q3 Plan</h1>
  <div class="meta">Author: Jane · Updated 2025-01-15 · Status: Draft</div>

  <div class="section">
    <h2>Problem</h2>
    <p>What problem are we solving and why it matters.</p>
  </div>

  <div class="section">
    <h2>Proposed solution</h2>
    <p>What we're going to build and how.</p>
  </div>

  <div class="section">
    <h2>Timeline</h2>
    <table>
      <tr><td>Week 1-2</td><td>Research & design</td></tr>
      <tr><td>Week 3-6</td><td>Implementation</td></tr>
      <tr><td>Week 7-8</td><td>Testing & rollout</td></tr>
    </table>
  </div>

  <div class="section">
    <h2>Open questions</h2>
    <ul>
      <li>Question 1</li>
      <li>Question 2</li>
    </ul>
  </div>
</body>
```

### Design mockup / wireframe

For visual prototypes, use Tailwind via CDN for faster styling:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Mockup</title>
</head>
<body class="bg-gray-50 min-h-screen">
  <!-- Your mockup here using Tailwind classes -->
</body>
</html>
```

### Status dashboard

```html
<div class="section">
  <h2>System Status</h2>
  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem;">
    <div style="border: 1px solid #ddd; border-radius: 8px; padding: 1rem;">
      <strong>API</strong> <span style="color: green;">✓ Operational</span>
    </div>
    <div style="border: 1px solid #ddd; border-radius: 8px; padding: 1rem;">
      <strong>Database</strong> <span style="color: green;">✓ Operational</span>
    </div>
  </div>
</div>
```

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| `curl` returns `000` | bore or server died | Restart both (server first, then bore) |
| `Address already in use` | Stale process on port | `lsof -ti:PORT \│ xargs kill -9` |
| `could not connect to bore.pub:7835` | bore.pub is down or blocked | Try again, or self-host a bore server |
| Page loads but looks broken | Missing CSS/font imports | Make sure all resources are inline or from CDN URLs |
| Page not found (404) | Wrong filename in URL | Check the exact filename and path, bore serves from the directory you started the server in |
| bore tunnel works but page is blank | HTML syntax error | Open `http://localhost:9090/your-page.html` in browser to see the actual error |

## Re-establishing a broken tunnel

If the link you shared stops working (server restart, laptop sleep, network change):

1. Kill stale processes:
   ```bash
   lsof -ti:9090 | xargs kill -9 2>/dev/null
   ```

2. Restart the HTTP server:
   ```bash
   cd /path/to/your/html && python3 -m http.server 9090 &
   ```

3. Restart bore:
   ```bash
   bore local 9090 --to bore.pub --port 55000 2>&1 | tee /tmp/bore-output.log &
   ```

4. Verify:
   ```bash
   curl -s -o /dev/null -w "%{http_code}" http://localhost:9090/your-page.html
   curl -s -o /dev/null -w "%{http_code}" http://bore.pub:55000/your-page.html
   ```

Both should return `200`.

## Pitfalls

- **Not verifying locally before tunneling** — always `curl` localhost first. If it's broken locally, tunneling won't fix it.
- **Using relative paths to local files** — everything must be inline or from CDN. The person opening the link doesn't have your `/assets/` folder.
- **Forgetting the filename in the URL** — `http://bore.pub:55000/` serves the directory listing or `index.html`. Share the exact path if your file isn't `index.html`.
- **Leaving bore running indefinitely** — it's for sharing, not hosting. Kill it when the review is done.
- **Expecting HTTPS** — bore.pub is HTTP only. Don't share sensitive data through it without understanding the implications.

## Verification

- ✅ `curl localhost:PORT/page.html` returns `200`
- ✅ `curl bore.pub:REMOTE_PORT/page.html` returns `200`
- ✅ Page renders correctly in a browser (not just curl)
- ✅ All styles and scripts are inline or from CDN
- ✅ Page has a title, date, and context so the recipient knows what they're looking at