# SBN Integration Simulation Hub -- Maintenance Guide

This guide covers everything you need to maintain, update, and deploy the SBN Integration Simulation Hub. It is written for someone who may not have deep experience with git or web deployment.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Project Structure](#2-project-structure)
3. [How the HTML File Is Organized](#3-how-the-html-file-is-organized)
4. [How to Make Changes](#4-how-to-make-changes)
5. [How to Test Locally](#5-how-to-test-locally)
6. [How to Push Updates](#6-how-to-push-updates)
7. [Important Notes and Warnings](#7-important-notes-and-warnings)
8. [Troubleshooting](#8-troubleshooting)
9. [Architecture Reference](#9-architecture-reference)

---

## 1. Project Overview

The SBN Integration Simulation Hub is a **single-page web application** built entirely in one HTML file. It has:

- **Zero external dependencies** -- no frameworks, no CDNs, no npm packages
- **No build step** -- edit the file and you're done
- **No server-side code** -- it runs entirely in the browser

The app is hosted for free on **GitHub Pages**, which automatically publishes the latest version whenever you push changes to the `main` branch on GitHub.

---

## 2. Project Structure

```
sbn-integration-hub/
  index.html              The entire application (HTML + CSS + JS in one file)
  Screenshots/            GITP and overview screenshots (19 PNG files)
  Managed GatewaySS/      Managed Gateway screenshots (9 PNG files)
  README.md               Project description (shown on GitHub)
  MAINTENANCE.md          This file
  .gitignore              Tells git which files to ignore
```

---

## 3. How the HTML File Is Organized

The `index.html` file is approximately 6,200 lines. It is divided into three major sections:

| Section | Lines | Description |
|---|---|---|
| **CSS Styles** | 7 -- 1066 | All visual styling in a single `<style>` block |
| **HTML Content** | 1068 -- 4842 | All page content, modals, popups, and structure |
| **JavaScript** | 4843 -- 6179 | All interactivity in a single `<script>` block |

### CSS Design System

The design system uses CSS custom properties (variables) defined at the top of the `<style>` block. To change brand colors, edit these values:

```css
:root {
    --sap-blue: #0a6ed1;       /* Primary brand blue */
    --sap-blue-dark: #074a91;  /* Darker blue for gradients */
    --sap-gold: #e8a317;       /* Accent gold */
    --sap-green: #2b8c3e;      /* Success / positive */
    --sap-red: #cc1919;        /* Error / negative */
    --sap-orange: #e76500;     /* Warning / simulation banner */
    /* ...and more */
}
```

### Page Structure

Each page in the app is a `<div>` with a class of `page` and an ID. Only the active page is visible at a time. Here is the mapping between navigation tabs and page IDs:

| Navigation Tab | Page ID | Content |
|---|---|---|
| Home | `page-welcome` | Hero banner, quick-start cards, self-assessment |
| Overview | `page-overview` | Integration basics and models |
| 7-Phase Process | `page-process` | Timeline of all integration phases |
| Document Standards | `page-cxml` | cXML and other standard references |
| Managed Gateway | `page-managed-gateway` | Gateway setup wizard |
| GITP | `page-gitp` | GITP guided walkthrough |
| Test Central | `page-testing` | Test Central overview and screenshots |
| UAT Scenario Builder | `page-testscript` | Test scenario configuration tool |
| Timeline Planner | `page-timeline` | Project timeline calculator |
| Resources & FAQ | `page-faq` | FAQ accordion and resource links |

---

## 4. How to Make Changes

### Editing Text Content

1. Open `index.html` in a text editor (VS Code recommended)
2. Use **Ctrl+F** (or Cmd+F on Mac) to search for the text you want to change
3. Make your edit and save the file
4. Test locally (see Section 5) before pushing

### Editing Styles / Colors

- All CSS is in the `<style>` block (lines 7--1066)
- To change brand colors globally, modify the `:root` variables (see Section 3 above)
- To change a specific element's style, search for its CSS class name

### Editing JavaScript Behavior

- All JavaScript is in the `<script>` block (lines 4843--6179)
- The code is wrapped in an IIFE (immediately invoked function expression) so all variables are scoped
- Key functions you may need to modify:
  - `navigate(pageId)` -- switches between pages
  - `notify(message, type)` -- shows toast notifications
  - `renderTS()` -- renders the UAT Script Builder table
  - `renderTCTable()` -- renders the Test Central scenarios table
  - `tlRenderPhases()` -- renders the Timeline Planner phases

### Adding or Replacing Images

1. Place the new image in the appropriate folder:
   - GITP / overview screenshots go in `Screenshots/`
   - Managed Gateway screenshots go in `Managed GatewaySS/`
2. Reference it in the HTML with a relative path:
   ```html
   <img src="Screenshots/my-new-image.png" alt="Description of the image">
   ```
3. **Naming convention**: use hyphens or underscores instead of spaces in filenames (e.g., `my-new-image.png`, not `my new image.png`)
4. **Optimize images** before adding -- keep PNGs under 500 KB for fast loading. You can use tools like [TinyPNG](https://tinypng.com/) to compress them.

### Adding Downloadable PDFs or Documents

If you want to make PDFs or other documents available for download from the site:

1. Create a `docs/` folder in the project root (if it doesn't exist)
2. Place the file there: `docs/my-guide.pdf`
3. Add a download link in the HTML:
   ```html
   <a href="docs/my-guide.pdf" target="_blank">Download My Guide (PDF)</a>
   ```
4. Remember to `git add docs/my-guide.pdf` before committing (see Section 6)

---

## 5. How to Test Locally

Always test your changes before pushing to the live site.

### Method 1: Open Directly (Simplest)

Double-click `index.html` in File Explorer. It opens in your default browser. This works for most features.

> **Note**: The `file://` protocol may block some browser features like clipboard access (the "Copy" buttons on code blocks).

### Method 2: VS Code Live Server (Recommended)

1. Install [VS Code](https://code.visualstudio.com/) if not already installed
2. Install the **Live Server** extension (search "Live Server" in the Extensions panel)
3. Open the project folder in VS Code: **File > Open Folder > `C:\Projects\sbn-integration-hub`**
4. Right-click `index.html` in the file explorer and select **Open with Live Server**
5. The page opens at `http://127.0.0.1:5500/` and auto-reloads whenever you save a file

### Method 3: Python HTTP Server (Command Line)

If you have Python installed:

```bash
cd C:\Projects\sbn-integration-hub
python -m http.server 8000
```

Then open `http://localhost:8000` in your browser. Press **Ctrl+C** in the terminal to stop the server.

### Testing Checklist

Before pushing any changes, verify:

- [ ] All navigation tabs load their content (click through each one)
- [ ] Images display correctly (especially on GITP and Managed Gateway pages)
- [ ] Self-assessment wizard generates recommendations
- [ ] PO editor modal opens, adds/removes line items, and saves
- [ ] Test Central: Apply unlocks Add Scenario; scenarios add and remove correctly
- [ ] Timeline Planner calculates dates when you enter a start date
- [ ] "Save Page" button (top-right) downloads a working HTML file
- [ ] No errors in the browser console (press **F12** to open Developer Tools, click **Console** tab)

---

## 6. How to Push Updates

Every time you push to the `main` branch on GitHub, the live site updates automatically within 1--2 minutes. No additional action is needed.

### Basic Workflow

Open a terminal (Git Bash, PowerShell, or VS Code terminal) in the project folder:

```bash
# 1. Check what you changed
git status

# 2. Review your changes (optional but recommended)
git diff

# 3. Stage the files you changed
git add index.html

# 4. Commit with a descriptive message
git commit -m "Update hero section text on welcome page"

# 5. Push to GitHub (triggers auto-deploy)
git push
```

### Common Scenarios

**I only changed text in `index.html`:**
```bash
git add index.html
git commit -m "Update text on overview page"
git push
```

**I added a new image:**
```bash
git add Screenshots/new-screenshot.png index.html
git commit -m "Add new screenshot to GITP section"
git push
```

**I want to undo my unpushed changes:**
```bash
# Discard all changes to index.html (reverts to last commit)
git checkout -- index.html
```

**I want to see what the live site has vs. my local copy:**
```bash
git log --oneline -5
```

---

## 7. Important Notes and Warnings

### Case Sensitivity
GitHub Pages runs on Linux, which is **case-sensitive**. If your image is named `MGHome.png`, the HTML must reference `MGHome.png` exactly -- not `mghome.png` or `MGHOME.PNG`. Windows does not enforce this, so always double-check filenames.

### Don't Edit on GitHub's Web Interface
The `index.html` file is too large (~6,200 lines) for comfortable editing on GitHub's web editor. Always edit locally in VS Code or your preferred text editor.

### Always Test Before Pushing
Once you push, the live site updates within minutes. There is no staging environment or approval step. Test locally first.

### Don't Commit Confidential Files
This is a **public** repository. Never add files containing:
- Internal SAP credentials or API keys
- Personal employee information
- Documents marked "Confidential" or "Internal"

The `.gitignore` file helps prevent accidental commits of common system files, but it cannot catch everything.

### Working From Multiple Computers
If you edit from more than one computer, always pull the latest changes before starting work:

```bash
git pull
```

This prevents conflicts when you try to push.

### Repository Size
GitHub Pages has a **1 GB** repository size limit and a **100 MB** per-file limit. The current site is well under these limits (~4 MB total), but be mindful when adding large images or documents.

---

## 8. Troubleshooting

### Images are broken on the live site
- **Check the filename**: it must match exactly, including capitalization (see Case Sensitivity above)
- **Check the path**: images must use relative paths like `Screenshots/filename.png`, not absolute paths
- **Check that you committed the image**: run `git status` to see if the image file was pushed

### Changes are not appearing on the live site
1. Verify you committed **and** pushed: `git log --oneline -1` should show your latest commit, and `git status` should show "nothing to commit"
2. Check the GitHub Actions tab in your repository for deployment status
3. Hard-refresh your browser: **Ctrl+Shift+R** (or Cmd+Shift+R on Mac) to bypass the cache
4. Wait 2--3 minutes -- GitHub Pages deployments can take a moment

### Git says there are merge conflicts
This happens when changes were made on GitHub or from another computer. To resolve:

```bash
git pull
# Git will mark conflicted files. Open them, resolve the conflicts, then:
git add index.html
git commit -m "Resolve merge conflict"
git push
```

### The page is blank or broken
- Open Developer Tools (**F12**) and check the **Console** tab for JavaScript errors
- Common causes: a missing closing tag (`</div>`), an unclosed string in JavaScript, or a missing bracket
- Use **Ctrl+Z** in your editor to undo recent changes and test again

### Git won't let me push
- Run `git pull` first to incorporate remote changes
- If you see "divergent branches", run: `git pull --rebase` then `git push`

---

## 9. Architecture Reference

This section is for advanced users who need to understand the app's internals.

### Session Storage Keys

The app uses `sessionStorage` (browser tab-scoped, cleared when the tab closes) for state persistence:

| Key | Used By | Purpose |
|---|---|---|
| `gitpPopupDismissed` | GITP page | Remembers if user dismissed the access popup |
| `sbn_tool_assessment` | Self-assessment | Saves assessment answers and recommendation |
| `sbn_tool_testcentral` | Test Central | Saves the full UAT scenario builder form state |
| `sbn_tool_uatscript` | UAT Script Builder | Saves the test case list |
| `sbn_tool_timeline` | Timeline Planner | Saves start date, region, phases, and risk data |

### "Save Page" Feature

The "Save Page" button (top-right corner) serializes the current DOM state -- including form values and dynamically rendered tables -- into a standalone HTML file. This works because the entire app is self-contained in one file with no external dependencies.

### Font Stack

The CSS references the SAP `'72'` font, but does not load it from a CDN. It falls back to:
```
'72', 'Segoe UI', Arial, sans-serif
```
On non-SAP machines, Segoe UI (Windows) or Arial will be used. The layout is designed to work with any of these fonts.

### No External Dependencies

The app makes **zero network requests** after the initial page load. There are no:
- CDN-hosted CSS or JS libraries
- API calls to external services
- Analytics or tracking scripts
- Web fonts loaded from external URLs

This means the app works fully offline once loaded, and there are no third-party dependencies that could break.

### Holiday Calendar

The Timeline Planner includes public holiday calculations for 14 countries: AU, US, GB, CA, DE, FR, IN, JP, SG, KR, CN, BR, ZA, AE. Easter dates are computed using the Anonymous Gregorian algorithm. Holiday-aware business day calculations are used to schedule project phases.
