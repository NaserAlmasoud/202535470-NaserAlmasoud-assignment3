# Technical Documentation

## 1. Overview

A single-page static website with no server, database or build step. The browser loads `index.html`, which links two stylesheets (`css/theme.css`, then `css/styles.css`) and one deferred script (`js/script.js`). Besides the site's own files, the only network requests are the Font Awesome icons (CDN) and two public APIs: GitHub and Codeforces.

| Assignment 3 requirement | Implementation |
|---|---|
| API integration | GitHub repositories (`loadGitHub`), plus Codeforces stats from Assignment 2 (`loadCodeforces`) |
| Complex logic | Projects filtered by category **and** level, then sorted (`setupProjects`); form that enables **Send** only when every rule passes (`setupContactForm`); visit timer (`startVisitTimer`) |
| State management | Light/dark mode (`setupThemeToggle`), visitor name (`setupVisitorName`), collapsed sections (`setupSectionToggles`), all saved in `localStorage` |
| Performance | WebP portrait, unused files removed, deferred script, non-blocking icon stylesheet (section 8) |

## 2. Page structure (`index.html`)

| Element | Purpose |
|---|---|
| `<head>` script | Applies the saved theme before the page is drawn (section 6.1) |
| `<header>` / `<nav>` | Section links, theme button, mobile menu button |
| `#about` | Greeting, **What's your name?** link, intro, profile image, social links |
| `#skills` | Skills list, with a **Hide/Show** button |
| `#projects` | Category buttons, level and sort `<select>`s, and an empty `#projectGrid` that JavaScript fills |
| `#github` | `#ghList` (filled by JavaScript) and a hidden `#ghError` box with **Try again** |
| `#codeforces` | Panel with three states (`#cfLoading`, `#cfError`, `#cfContent`) and a **Hide/Show** button |
| `#contact` | Form with an error line under each field, a message counter, a disabled **Send** button and a `#formStatus` message |
| `<footer>` | Copyright, time-on-site timer, "Back to top" link |

Accessibility: filter buttons use `aria-pressed`; Hide/Show buttons use `aria-expanded`; the selects and theme button have `aria-label`; loading messages use `role="status"` and error boxes `role="alert"`; form errors use `aria-live="polite"` and `aria-invalid`.

## 3. Styling

- **`css/theme.css`** defines the dark palette as CSS variables on `:root`, the self-hosted fonts and the base element styles.
- **`css/styles.css`** holds the layout and components, plus a `[data-theme="light"]` block that overrides the same variables. Because every component uses variables (`var(--bg)`, `var(--ink)` …), switching theme is one attribute change on `<html>`.
- **Grid** is used for the About section, project cards, GitHub cards and Codeforces tiles; **Flexbox** for the nav, filters and footer.
- The GitHub list uses `repeat(auto-fit, minmax(260px, 1fr))`, so it shows 2–3 columns on desktop and 1 on phones with no media query.
- A collapsed section hides everything except its title and button with one rule: `.is-collapsed > :not(.section-title, .section-toggle) { display: none; }`.
- Class names follow a light BEM style (`block__element--modifier`); state classes start with `is-` (`is-active`, `is-open`, `is-invalid`, `is-visible`, `is-collapsed`).

| Width | Changes |
|---|---|
| > 900px | 3 project columns, 4 Codeforces tiles |
| ≤ 900px | 2 project columns, Codeforces tiles in a 2×2 grid |
| ≤ 680px | 1 column, hamburger menu, smaller photo above the text |

## 4. JavaScript (`js/script.js`)

One function per feature; all are started from a single `DOMContentLoaded` listener.

| Function | What it does |
|---|---|
| `setGreeting()` | "Good morning / afternoon / evening" from the visitor's clock, plus their saved name |
| `setupVisitorName()` | Asks for, saves or forgets the visitor's name |
| `setupMobileNav()` | Opens/closes the mobile menu |
| `setupProjects()` | Filters, sorts and renders project cards |
| `loadCodeforces()` | Codeforces stats with loading/error/data states |
| `loadGitHub()` | Latest GitHub repositories with an error state |
| `setupContactForm()` | Field rules, error messages, counter, **Send** enabled only when valid |
| `setupReveal()` | Fades sections in as they scroll into view |
| `startVisitTimer()` | Time-on-site counter |
| `setupThemeToggle()` | Switches and saves light/dark mode |
| `setupSectionToggles()` | Hides/shows sections and saves the choice |

## 5. APIs

### 5.1 GitHub REST API

- **Endpoint:** `GET https://api.github.com/users/NaserAlmasoud/repos?sort=updated&per_page=6` (public, no key).
- **Fields used:** `name`, `html_url`, `description`, `language`, `stargazers_count`.
- **Limit:** 60 requests per hour per visitor IP without a token, which is plenty for a portfolio.

```js
try {
  const response = await fetch(GH_URL);
  if (!response.ok) throw new Error(response.status); // 403 = rate limit, 404 = wrong user, …
  const repos = await response.json();
  list.innerHTML = repos.map((repo) => `<li class="gh__repo">…</li>`).join("");
} catch (err) {
  list.innerHTML = "";
  error.hidden = false;                               // offline, GitHub down or limit reached
}
```

- `fetch` only rejects on network errors, so `response.ok` is checked to also treat HTTP errors (like 403) as failures.
- Missing values get fallbacks: `repo.description || "No description yet."`, `repo.language || "Code"`.
- `innerHTML` is acceptable here because the repositories are my own; text typed by visitors is never inserted as HTML.
- **Try again** simply calls `loadGitHub()` again.

### 5.2 Codeforces API (from Assignment 2)

- **Endpoint:** `GET https://codeforces.com/api/user.info?handles=Naser_md`.
- Shows a spinner, then either four stat tiles or a friendly error with **Try again**. `finally` always hides the spinner. Values are inserted with `textContent`.

## 6. State management

All state is kept in `localStorage`, so it survives reloads and new visits on the same browser.

| Key | Values | Set by | Read when |
|---|---|---|---|
| `theme` | `"light"` / `"dark"` | Theme button | In `<head>`, before the page is drawn |
| `visitorName` | the name, or removed | **What's your name?** | `setGreeting()` on load and after a change |
| `hide-skills`, `hide-codeforces` | `"true"` / `"false"` | Hide/Show buttons | `setupSectionToggles()` on load |

### 6.1 Light/dark mode

```html
<script>
  document.documentElement.dataset.theme = localStorage.getItem("theme")
    || (matchMedia("(prefers-color-scheme: light)").matches ? "light" : "dark");
</script>
```

- This tiny script is in `<head>` instead of `script.js` so the right colours apply **before** the first paint; otherwise light-mode users would see a dark flash.
- With no saved choice, the device setting (`prefers-color-scheme`) is used.
- The button flips `data-theme` between `dark` and `light` and saves it.

### 6.2 Visitor name

- `prompt()` asks for the name. **Cancel** (`null`) changes nothing; an empty answer removes the saved name.
- `setGreeting()` builds the greeting from the time and the name, changes the link to **Not you?**, and fills the contact form's name field.

### 6.3 Show/hide sections

- Each `.section-toggle` button finds its section with `closest("section")` and uses the key `hide-<section id>`.
- One `apply(hidden)` function updates the class, button text, `aria-expanded` and storage, so the saved state and the page can never disagree.
- The code is generic: adding the button to any other section works without new JavaScript.

## 7. Application logic

### 7.1 Filter + sort projects

Each project in the `PROJECTS` array has `title`, `category`, `level`, `date` (`"YYYY-MM"`), `tech`, `description` and an optional `sample` flag.

```js
const SORTS = {
  newest: (a, b) => b.date.localeCompare(a.date),
  oldest: (a, b) => a.date.localeCompare(b.date),
  az: (a, b) => a.title.localeCompare(b.title),
};

const visible = PROJECTS
  .filter((p) => (filter === "all" || p.category === filter) && (level === "all" || p.level === level))
  .sort(SORTS[sortSelect.value]);
```

1. **Filter:** a project must match **both** the category button and the level select.
2. **Sort:** the chosen comparison function from `SORTS` is used. Because dates are written `YYYY-MM`, comparing them as text gives the correct date order.
3. **Render:** if nothing matches, "No projects match these choices." is shown; otherwise the cards are built in one `innerHTML` update.

Using a lookup object (`SORTS`) instead of an `if/else` chain keeps each rule in one place and makes adding a new sort option a one-line change.

### 7.2 Contact form

| Field | Rule |
|---|---|
| Name | At least 2 characters |
| Email | Matches `text@text.text` |
| Message | At least 10 characters (max 500, enforced by `maxlength`) |

1. **While typing** (`input` event): the counter updates, and **Send** is enabled only if every rule returns no error.
2. **On leaving a field** (`blur`): that field's error message is shown.
3. **On submit:** all fields are checked again (in case the button was enabled by another route), errors are shown together, and focus moves to the first invalid field. On success, a confirmation appears, the form resets, and **Send** is disabled again.

The same `rules` object drives all three steps, so a rule is never written twice.

### 7.3 Time on site

`setInterval` adds one second each tick and shows it as `m:ss` (`padStart(2, "0")` turns `5` into `05`).

## 8. Performance

| Change | Result |
|---|---|
| Portrait converted from a traced SVG to a 560×560 WebP (2× the display size for sharp screens) | 69 KB → 12 KB |
| Deleted unused `profile.jpeg` and `profile.svg` | 89 KB less in the repository |
| Removed 9 `@font-face` rules pointing to font files that do not exist | No dead references |
| `<script src="js/script.js" defer>` | HTML is parsed and drawn without waiting for the script |
| Font Awesome loaded with `media="print" onload="this.media='all'"` | The ~100 KB icon stylesheet no longer delays the first paint |
| `width`/`height` on the image | Space is reserved, so nothing jumps when it loads |

Also kept from Assignment 2: no frameworks, self-hosted WOFF2 fonts with `font-display: swap`, project covers made with CSS gradients instead of images, `IntersectionObserver` instead of scroll listeners, and animations that only use `opacity` and `transform`.

**Lighthouse** (Chrome DevTools → Lighthouse → Analyze page load):

| Performance | Accessibility | Best Practices | SEO |
|---|---|---|---|
| *(add score)* | *(add score)* | *(add score)* | *(add score)* |

## 9. Error handling and user feedback

| Situation | What the user sees |
|---|---|
| GitHub repos loading | "Loading repositories…" |
| GitHub unreachable, rate-limited or error | "Couldn't load repositories…" and **Try again** |
| Codeforces loading / failed | Spinner / friendly message, **Try again** and a profile link |
| No project matches the filters | "No projects match these choices." |
| Form field invalid | Red outline and a message explaining the fix |
| Form not complete | **Send** stays disabled (faded, "not-allowed" cursor) |
| Form sent | Green confirmation explaining nothing was actually emailed |
| JavaScript turned off | "Turn on JavaScript to see the project list." |

## 10. Browser compatibility

Everything used (`fetch`, `async/await`, `localStorage`, `IntersectionObserver`, `matchMedia`, CSS Grid, custom properties, `:not()` with a selector list, WebP) is supported by current Chrome, Edge, Firefox and Safari. Very new functions (like `Object.hasOwn`) are avoided.

| Browser | Desktop | Mobile |
|---|---|---|
| Chrome | ✅ | ✅ |
| Edge | ✅ | – |
| Firefox | *(update after testing)* | – |
| Safari | *(update after testing)* | *(update after testing)* |

## 11. Testing checklist

- [x] GitHub repos load; with the network blocked, the error and **Try again** appear
- [x] Category + level + sort combine correctly; "No projects match" appears when needed
- [x] **Send** is disabled until all fields are valid; counter updates; form resets after sending
- [x] Timer counts in `m:ss`
- [x] Theme follows the device on first visit, switches on click and survives a reload
- [x] Name appears in the greeting and form, survives a reload, Cancel changes nothing, empty input forgets it
- [x] Hidden sections stay hidden after a reload
- [x] Portrait displays correctly in both themes; no 404s or console errors
- [x] Layout works at 390px (phone) and 1280px (desktop)
- [ ] Lighthouse report run and scores added above

## 12. Future improvements

- Replace the five sample projects with real ones, with links to code and demos
- Connect the contact form to a service such as Formspree
- Show the Codeforces rating history as a chart
