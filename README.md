# Personal Portfolio – Assignment 3

My personal portfolio website, built with plain HTML, CSS and JavaScript. Assignment 3 builds on [Assignment 1](https://github.com/NaserAlmasoud/202535470-NaserAlmasoud-assignment1) and [Assignment 2](https://github.com/NaserAlmasoud/202535470-NaserAlmasoud-assignment2) and adds advanced features: a second live API (GitHub), projects that can be filtered and sorted, smarter form checks, a visit timer, remembered preferences (theme, name, open sections) and performance improvements.

**Live demo:** https://naseralmasoud.github.io/202535470-NaserAlmasoud-assignment3/

## What's new in Assignment 3

| Requirement | Feature | Where |
|---|---|---|
| API integration | **GitHub repositories**: my 6 latest public repos load from the GitHub API, with an error message and **Try again** button if it fails | `#github`, `loadGitHub()` |
| Complex logic | **Filter + sort projects**: category buttons, a level filter (Beginner / Advanced) and a sort (newest, oldest, A–Z) work together; a message shows when nothing matches | `#projects`, `setupProjects()` |
| Complex logic | **Contact form**: **Send** stays disabled until every field is valid; a counter shows message length (max 500) | `#contact`, `setupContactForm()` |
| Complex logic | **Time on site** timer in the footer | `startVisitTimer()` |
| State management | **Light/dark mode** toggle, remembered between visits (first visit follows the device setting) | nav button, `setupThemeToggle()` |
| State management | **Visitor name**: saved once, then used in the greeting and the contact form | `#about`, `setupVisitorName()` |
| State management | **Show/hide sections** (Skills, Competitive Programming), remembered between visits | `setupSectionToggles()` |
| Performance | Portrait changed from a 69 KB SVG to a 12 KB WebP, unused images and font rules removed, script deferred, icons loaded without blocking | `assets/`, `index.html` |

Kept from Assignments 1–2: About and Skills sections, time-of-day greeting, live Codeforces stats, scroll animations, responsive layout with a mobile menu, and accessibility features (skip link, focus styles, ARIA labels, reduced-motion support).

> Five of the seven projects are marked **Sample**. They are example entries used to test the filters and will be replaced with real projects.

## Tech stack

- HTML5, CSS3 (custom properties, Grid, Flexbox, media queries)
- Vanilla JavaScript (ES6+): `fetch`, `async/await`, `localStorage`, `IntersectionObserver`. No frameworks or build step.
- [GitHub REST API](https://docs.github.com/en/rest/repos/repos#list-repositories-for-a-user) and [Codeforces API](https://codeforces.com/apiHelp) (both public, no key needed)
- Font Awesome 6 icons (CDN) and self-hosted Manrope / Source Serif 4 fonts

## Project structure

```
.
├── README.md
├── index.html                  # Page markup (+ small head script that applies the saved theme)
├── css/
│   ├── theme.css               # Dark palette, fonts, base element styles
│   ├── styles.css              # Light palette, layout, components, animations, breakpoints
│   └── fonts/                  # Manrope + Source Serif 4 (WOFF2)
├── js/
│   └── script.js               # All interactivity, one function per feature
├── assets/
│   └── images/profile.webp     # Portrait
├── docs/
│   ├── ai-usage-report.md      # How AI tools were used (with declaration)
│   └── technical-documentation.md
└── .gitignore
```

## Run locally

No installation or build step. You need a web browser, and an internet connection for the API data and icons.

1. Clone the repository:
   ```bash
   git clone https://github.com/NaserAlmasoud/202535470-NaserAlmasoud-assignment3.git
   cd 202535470-NaserAlmasoud-assignment3
   ```
2. Open the site with **one** of these options:
   - **Simplest:** double-click `index.html`.
   - **VS Code:** install the *Live Server* extension, right-click `index.html` → **Open with Live Server**.
   - **Python:** run `python -m http.server 8000` and visit http://localhost:8000.
3. After pulling changes, hard-refresh with **Ctrl+Shift+R** (Windows/Linux) or **Cmd+Shift+R** (Mac).

## Using the site

- **Theme:** click the half-circle icon in the top bar to switch light/dark. Your choice is remembered.
- **Your name:** click **What's your name?** under the greeting. Click **Not you?** to change it, or leave the box empty to forget it.
- **Projects:** pick an area (**All / AI / Data Science / Web Dev**), a level, and a sort order. They combine, e.g. *Web Dev + Beginner + Oldest first*.
- **Hide/Show:** collapse Skills or Competitive Programming; they stay that way on your next visit.
- **GitHub and Codeforces:** load automatically. If a service can't be reached, a message explains it; press **Try again**.
- **Contact form:** **Send message** turns on only when the name, email and message are valid. Errors appear when you leave a field. There is no backend, so nothing is emailed.
- **Timer:** the footer shows how long you have been on the page.

> To reset saved preferences, clear the site's data in your browser (or run `localStorage.clear()` in the console).

## Deployment (GitHub Pages)

1. In the repository, go to **Settings → Pages**.
2. Under **Source**, choose **Deploy from a branch**, select `main` and `/ (root)`, then **Save**.
3. After about a minute the site is live at https://naseralmasoud.github.io/202535470-NaserAlmasoud-assignment3/.

## AI use summary

I used **Claude Code** to read the assignment guidelines, plan the features, and write a first draft of each one. I reviewed every feature before it was committed: I asked for the code to be as short and clean as possible (the GitHub feature went from 161 to 59 lines), chose what to keep, and approved each change separately. Every AI-assisted commit is marked `Assisted-by: Claude (Claude Code)`. Full details, the usage log and my declaration are in [docs/ai-usage-report.md](docs/ai-usage-report.md).

## Documentation

- [Technical documentation](docs/technical-documentation.md)
- [AI usage report](docs/ai-usage-report.md)

## Author

**Naser Almasoud**, Student ID `202535470`
