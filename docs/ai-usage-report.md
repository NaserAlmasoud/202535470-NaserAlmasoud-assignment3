# AI Usage Report – Assignment 3

## 1. Tools used & use cases

| Tool | How I used it |
|---|---|
| **Claude Code** (claude.ai/code) | Read the Assignment 3 guidelines and explained the requirements. Copied my Assignment 2 site into the new repository as the starting point. Drafted each feature one at a time (GitHub API, filter + sort, form checks, timer, theme, visitor name, show/hide sections, performance), tested it in a headless browser, and showed me the code. Nothing was committed until I approved it. Also drafted the README, technical documentation and this report. |

I did not use any other AI tools for this assignment.

## 2. Usage log

| ID | Tool (model) | Date | Purpose | Files affected | What I changed and how I verified it |
|---|---|---|---|---|---|
| C01 | Claude Code (Opus 5.5) | 2026-11-22 – 2026-11-23 | Explain Assignment 3; build every feature step by step; write documentation | `index.html`, `css/styles.css`, `css/theme.css`, `js/script.js`, `assets/images/`, `README.md`, `docs/*` | Set the rule that every feature must be shown to me before it is committed, and approved each one separately (9 feature/doc commits). Asked for the code to be "clean and minimal", which cut the GitHub feature from 161 to 59 lines. Chose to commit the projects HTML/CSS and JS separately. Interrupted one commit to check it first. Checked each feature in my browser. **TODO:** add anything you changed yourself. |
| A01 | Claude Code | 2026-11-22 | First version of the GitHub repos section (161 lines: cache in `sessionStorage`, separate rate-limit message, cards built with DOM methods) | `js/script.js`, `index.html`, `css/styles.css` | Too long for what it does. I asked for minimal code; the shorter version (A02) was committed. Saved in `artifacts/` (copied from the conversation). |
| A02 | Claude Code | 2026-11-22 | Minimal GitHub section (59 lines) | same | Approved and committed as `c95dfbd`. Checked success and failure states. |
| A03 | Claude Code | 2026-11-22 | Projects: level filter + sorting | `index.html`, `css/styles.css`, `js/script.js` | Reviewed the project levels it proposed. Committed as `aa01422` (HTML/CSS) and `92bb7dc` (JS). |
| A04 | Claude Code | 2026-11-22 | Contact form counter + **Send** enabled only when valid | same | Approved; commit `93e7ba4`. |
| A05 | Claude Code | 2026-11-22 | Time-on-site timer | `index.html`, `js/script.js` | Approved; commit `99bed19`. |
| A06 | Claude Code | 2026-11-22 | Light/dark mode saved in `localStorage` | `index.html`, `css/styles.css`, `js/script.js` | Approved; commit `6a3049a`. |
| A07 | Claude Code | 2026-11-22 | Visitor name saved and used in the greeting | same | Its first draft had a bug (the setup call was inside the click handler, so the button did nothing); it found and fixed it during testing before showing me. Approved; commit `c7f03d4`. |
| A08 | Claude Code | 2026-11-22 | Show/hide sections saved in `localStorage` | same | Approved; commit `e73901c`. |
| A09 | Claude Code | 2026-11-22 | Performance: WebP portrait, delete unused files, `defer`, non-blocking icons | `assets/images/`, `css/*`, `index.html` | Approved; commit `dd030e5`. This also fixed the portrait showing black on navy in light mode. |
| A10 | Claude Code | 2026-11-22 – 2026-11-23 | README, technical documentation, this report | `README.md`, `docs/*` | Commits `038d21a`, `eed6940`. Updated details only I can fill. |

To export the intermediate versions for `artifacts/`, save the code blocks from the conversation (A01), or run, for example:
```bash
git show c95dfbd:js/script.js > artifacts/A02-script.js
```

## 3. Benefits & challenges

**Benefits**
- **Clear plan:** the AI turned the guidelines into a checklist of features mapped to each rubric item, building on my Assignment 2 code instead of starting over.
- **Small reviewable steps:** each feature was a separate diff I could read, question and approve, so the commit history shows the progress.
- **Testing I could not easily do:** it tested each feature in a headless browser, including failure cases (network blocked, GitHub rate limit) and light/dark screenshots.
- **Found a hidden bug:** while doing the performance step it noticed the portrait was unreadable in light mode and fixed it.

**Challenges**
- **Too much code at first:** the first GitHub version was 161 lines with caching I did not need. I had to ask for minimal code.
- **Mistakes in its own edits:** one edit script deleted part of the CSS by matching the wrong comment, and another put a function call in the wrong place. It caught and fixed both during testing, but it showed me that AI edits must be checked.
- **Environment limits:** its sandbox could not reach the real GitHub API or the icon CDN, and running Lighthouse was blocked, so I had to check the live site and run Lighthouse in my own browser.
- **Commit hook pressure:** a hook kept asking it to commit; it waited for my approval as I had asked.

## 4. Learning outcomes

- **`fetch` error handling:** `fetch` only fails on network errors, so `response.ok` must be checked to catch HTTP errors such as GitHub's 403 rate limit.
- **Combining logic:** `filter()` with two conditions, then `sort()` with a comparison function from a lookup object (`SORTS`); `YYYY-MM` dates sort correctly as text.
- **State with `localStorage`:** saving preferences as key/value strings (`"true"` / `"false"`), restoring them on load, and keeping one `apply()` function so the page and storage never disagree.
- **Avoiding a theme flash:** a tiny script in `<head>` applies the saved theme before the page is drawn; `prefers-color-scheme` gives a sensible default.
- **CSS variables for theming:** overriding the same colour tokens under `[data-theme="light"]` switches every component at once.
- **Performance:** WebP images, `defer` on scripts, and the `media="print"` trick to load a stylesheet without blocking the page.
- **Workflow:** reviewing diffs before committing and writing `Assisted-by:` in commit messages.

## 5. Responsible use & modifications

- **Review before commit:** I required every feature to be shown to me first; nothing was pushed without my explicit approval.
- **Simplicity over volume:** I rejected the long first version of the GitHub feature and asked for clean, minimal code in every later step.
- **Testing:** each feature was tested by the AI (headless browser) and checked by me in my own browser. **TODO:** confirm, and note any browsers/devices you tested.
- **Understanding:** I can explain every function in `js/script.js`; the technical documentation describes how each one works.
- **Transparency:** every AI-assisted commit has `Assisted-by: Claude (Claude Code)`. The full conversation and intermediate artifacts are in the AI Evidence ZIP on Blackboard, not in this repository.

| AI output | My change | Why |
|---|---|---|
| 161-line GitHub section with caching | Asked for a minimal version (59 lines) | Easier to read and explain; caching was not needed |
| One commit for the projects feature | Committed HTML/CSS and JS separately | My choice for the history |

## 6. Innovation

- Used AI as a **reviewer-in-the-loop workflow**: AI drafts → AI tests → I review the diff → I approve → commit, one feature at a time.
- Used AI to **test failure paths** (blocked network, rate limit) with fake API responses before relying on the error messages.
- Showed my real activity with **two personal APIs** (GitHub repos and Codeforces rating) instead of a generic data source.

## 7. Declaration

I declare that this report and the attached evidence document all of my AI use in this assignment, with complete and unedited conversations, and that I understand and can explain all the code I submitted. I understand that undeclared AI use or a false declaration will be treated as plagiarism according to KFUPM regulations.

- **Name:** Naser Almasoud
- **Student ID:** 202535470
- **Date:** 26/11/2026
