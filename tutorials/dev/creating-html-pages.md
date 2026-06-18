# Tutorial: Creating and sharing HTML pages

A step-by-step walkthrough of creating a standalone HTML page and sharing it locally or via a public tunnel.

Based on the [html-share skill](../../skills/dev/html-share/SKILL.md).

## What you'll build

A one-page project proposal that you can share with teammates via a URL — no hosting, no deploy pipeline.

## Step 1: Create the HTML file

Create a file called `proposal.html`. Everything goes in one file — styles inline, no external dependencies.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Project Proposal: Risk Automation</title>
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
    h1 { margin-bottom: 0.5rem; }
    .meta { color: #666; font-size: 0.85rem; margin-bottom: 2rem; }
    h2 { margin: 1.5rem 0 0.5rem; color: #333; }
    p { margin-bottom: 1rem; }
    .highlight {
      background: #f0f7ff;
      border-left: 3px solid #0066ff;
      padding: 1rem;
      margin: 1rem 0;
    }
    table { border-collapse: collapse; width: 100%; margin: 1rem 0; }
    th, td { border: 1px solid #ddd; padding: 0.5rem 1rem; text-align: left; }
    th { background: #f5f5f5; }
  </style>
</head>
<body>
  <h1>Risk Automation — Project Proposal</h1>
  <div class="meta">Author: Jane Smith · Updated 2025-06-15 · Status: Draft</div>

  <div class="highlight">
    <p><strong>TL;DR:</strong> Automate 70% of manual risk ops workflows using AI agents, starting with compliance checks and audit trail generation.</p>
  </div>

  <h2>Problem</h2>
  <p>Risk teams spend most of their time on repetitive manual workflows — compliance checks, documentation, approvals. This slows down the business and introduces human error.</p>

  <h2>Proposed solution</h2>
  <p>Build autonomous agents that handle these workflows end-to-end, with human-in-the-loop for high-stakes decisions.</p>

  <h2>Timeline</h2>
  <table>
    <tr><th>Phase</th><th>Timeline</th><th>Deliverable</th></tr>
    <tr><td>Research</td><td>Week 1-2</td><td>Risk workflow audit</td></tr>
    <tr><td>Build</td><td>Week 3-6</td><td>Agent prototype</td></tr>
    <tr><td>Test</td><td>Week 7-8</td><td>Design partner validation</td></tr>
  </table>

  <h2>Open questions</h2>
  <ul>
    <li>Which risk workflow to automate first?</li>
    <li>Design partner or consulting engagement?</li>
    <li>Compliance requirements by industry?</li>
  </ul>
</body>
</html>
```

Key things this template does right:
- **Self-contained** — all styles are inline, no external files needed
- **Has context** — title, date, author at the top so the recipient knows what they're looking at
- **Mobile-friendly** — viewport meta tag included
- **Real content** — actual words, not lorem ipsum

## Step 2: Serve it locally

Start a static file server in the same directory as your HTML file:

```bash
cd /path/to/your/html
python3 -m http.server 9090
```

Verify it works:

```bash
curl -s -o /dev/null -w "%{http_code}" http://localhost:9090/proposal.html
# Should return: 200
```

Open it in your browser:

```bash
open http://localhost:9090/proposal.html   # macOS
xdg-open http://localhost:9090/proposal.html   # Linux
```

Check that it renders correctly. If it looks broken, fix the HTML before sharing.

## Step 3: Share it (optional)

If you want to send a public link to someone:

```bash
# Install bore if you don't have it
brew install bore-cli

# Start the tunnel
bore local 9090 --to bore.pub
```

This outputs something like:

```
INFO bore_cli::client: connected to server remote_port=42873
```

Your page is live at: `http://bore.pub:42873/proposal.html`

Share this URL with your teammate. They can view it in any browser.

Notes:
- The link works as long as your local server and bore are running
- bore.pub has no HTTPS — fine for internal sharing, not for sensitive content
- Kill the server and tunnel when done: `lsof -ti:9090 | xargs kill`

## Step 4: Customize for your needs

The starter template is minimal on purpose. Here's how to adapt it:

### For design mockups / prototypes

Add Tailwind via CDN for faster styling:

```html
<script src="https://cdn.tailwindcss.com"></script>
```

### For status dashboards

Use CSS grid for card layouts:

```html
<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem;">
  <div style="border: 1px solid #ddd; border-radius: 8px; padding: 1rem;">
    <strong>API</strong> <span style="color: green;">✓ Operational</span>
  </div>
  <div style="border: 1px solid #ddd; border-radius: 8px; padding: 1rem;">
    <strong>Database</strong> <span style="color: green;">✓ Operational</span>
  </div>
</div>
```

### For dark theme pages

Swap the body colors:

```css
body { background: #0f172a; color: #e2e8f0; }
h2 { color: #f1f5f9; }
.meta { color: #94a3b8; }
```

## What to remember

- **Inline everything** — styles, scripts, fonts. No local file references.
- **Verify locally first** — always `curl` localhost before tunneling
- **Real content, not lorem ipsum** — people review content, not placeholders
- **Kill the server when done** — bore is for sharing, not hosting
- **Tests are optional here** — this is content, not code

## Next steps

- Read the [html-share skill](../../skills/dev/html-share/SKILL.md) for the full reference including troubleshooting
- Try adding interactive elements (collapsible sections, tabs) with inline JavaScript