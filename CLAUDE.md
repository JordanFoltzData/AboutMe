# CLAUDE.md — AI Assistant Guide for Jordan's Portfolio Website

## Project Overview

This is a **static personal portfolio website** for Jordan Foltz, a data analyst. It is a single-page application (SPA) built with vanilla HTML5, CSS3, and JavaScript — no build tools, no frameworks, no package manager.

**Live sections**: Home (hero), About, Resume, Projects, Contact

**Additional pages**:
- `brand.html` — Personal Brand (Mission, Vision, Values)

---

## Repository Structure

```
AboutMe/
├── index.html                                    # Main portfolio — entire single-page structure
├── brand.html                                    # Personal brand page (Mission, Vision, Values)
├── style.css                                     # All styles, theming, and responsive CSS (shared)
├── brand.css                                     # Brand-page-specific layout styles
├── script.js                                     # All JavaScript interactivity (ES6 classes)
├── README.md                                     # User-facing project documentation
├── CLAUDE.md                                     # This file
├── Headshot Professional Resized.jpg             # Profile photo
├── Jordan Foltz Data Analyst Resume.docx         # Resume (downloadable, editable format)
├── Jordan_Foltz_Data_Analyst_Resume.pdf          # Resume (downloadable, primary format)
├── Facebook_Engagemnt_Algorithm_AB_Test_Case_Study.pdf   # Case study asset (note "Engagemnt" typo in filename)
├── Mobile_Site_Survey_AB_Testing_Case_Study.pdf  # Case study asset
└── Referral_Campaign_Jordan_Foltz.pdf            # Case study asset
```

**There is no `package.json`, no build step, no node_modules.** The site runs by opening `index.html` directly in a browser or serving the directory with any static file server.

---

## Architecture

### HTML (`index.html`)
- Single file, semantic HTML5
- Sections use `id` attributes for anchor navigation: `#home`, `#about`, `#projects`, `#resume`, `#contact`
- External dependencies loaded via CDN (no local copies):
  - **Font Awesome 6.0.0** — icons (`cdnjs.cloudflare.com`)
  - **Google Fonts** — Montserrat + Open Sans

### CSS (`style.css`)
- **CSS custom properties** (variables) are the design system — defined in `:root`
- **Dark mode** uses `[data-theme="dark"]` attribute on `<html>`, overriding the same variables
- **No CSS preprocessor** (no Sass/Less); plain CSS only
- Layout uses CSS Grid and Flexbox
- Responsive breakpoints handled via `@media` queries
- Key variable categories:
  - Colors: `--primary-green`, `--secondary-green`, `--accent-gold`, `--light-gold`
  - Theme-aware: `--bg-primary`, `--bg-secondary`, `--text-primary`, `--nav-bg`, `--card-bg`, `--border-color`
  - Typography: `--font-primary` (Montserrat), `--font-secondary` (Open Sans)
  - Spacing: `--section-padding`, `--container-padding`, `--border-radius`
  - Transitions: `--transition-fast` (0.3s), `--transition-slow` (0.5s)

### JavaScript (`script.js`)
- **All vanilla JS, no libraries**
- Organized as ES6 classes, one per feature
- All classes instantiated at `DOMContentLoaded`
- Uses `IntersectionObserver` API for scroll-triggered animations (no scroll event polling for animations)
- Uses `localStorage` for theme persistence

**Class inventory:**

| Class | Responsibility |
|---|---|
| `ThemeManager` | Dark/light toggle, persists to `localStorage` |
| `MobileNavigation` | Hamburger menu open/close |
| `NavigationManager` | Smooth scroll to sections, active nav link highlighting |
| `NavbarManager` | Navbar background change on scroll |
| `EnhancedScrollAnimations` | Fade-in animations via `IntersectionObserver` |
| `ContactForm` | Form validation and live submission via Formspree |
| `TypingAnimation` | Cycling typewriter text in hero subtitle; override phrases per page with `data-typing-texts="One|Two|Three"` on `.hero-subtitle` (used on brand.html) |
| `TimelineDraw` | Adds `.drawn` to `.timeline` on scroll — CSS then draws the line and pops the dots |
| `ProjectFilter` | Filter buttons show/hide project cards by category |
| `StatsCounter` | Animates hero stat numbers counting up |
| `EnhancedProjectInteractions` | Hover effects + ripple click effect on project cards |

Removed (do not reintroduce): `SkillsAnimation` (skill percentage bars were replaced with descriptive lists), `ResumeTracker` (faked a download spinner/success toast), `ParallaxEffect` (janky whole-hero transform), the floating hero cards, the radial-gradient `.hero-pattern`, and the artificial 1-second page loader on `window.load`.

**Hero signature animation**: the hero background contains an inline SVG (`.hero-chart`) — a line chart that draws itself on load (CSS `stroke-dashoffset` via `pathLength="1"`, staggered `.chart-dot` pop-ins). All animation timing lives in `style.css`; both it and the timeline draw respect `prefers-reduced-motion` (block at the end of `style.css`).

---

## Design System

### Color Palette
| Token | Value | Usage |
|---|---|---|
| `--primary-green` | `#2d5016` | Section titles, primary buttons, accents |
| `--secondary-green` | `#4a7c59` | Secondary elements |
| `--accent-gold` | `#d4af37` | Highlights, spinner border, badges |
| `--light-gold` | `#f4e4a6` | Subtle gold backgrounds |
| `--pure-black` | `#000000` | Dark mode background |
| `--pure-white` | `#ffffff` | Light mode background |

### Typography
- **Headings**: Montserrat (300, 400, 600, 700)
- **Body**: Open Sans (300, 400, 600)
- Section titles: `2.5rem`, color `var(--primary-green)`, centered with gold underline pseudo-element

### Spacing
- Max content width: `1200px` (`.container`)
- Section padding: `80px 0`

---

## Key Conventions

### Adding a New Section
1. Add a `<section id="new-section">` block in `index.html`
2. Add a nav link `<a href="#new-section" class="nav-link">` in the navbar
3. Style in `style.css` using existing CSS variables — never hardcode colors
4. If the section needs scroll animation, add its elements to the `querySelectorAll` selector inside `EnhancedScrollAnimations.init()`

### Modifying the Color Scheme
Only change values in the `:root` block and `[data-theme="dark"]` block at the top of `style.css`. Never use raw color values elsewhere in CSS — always use CSS variables.

### Adding a Project Card
Copy an existing `.project-card` div in the `#projects` section of `index.html`. Set `data-category` to one or more of: `case-study`, `video-presentation` (space-separated). The `ProjectFilter` class reads these automatically. If you add a new category, add a matching `.filter-btn` — but only for categories with real, completed work.

**Credibility rule — IMPORTANT**: Every metric shown on a project card must come directly from the linked case-study PDF or video. Never invent placeholder metrics, and never add blurred/"in development" cards with fabricated results.

### Dark Mode
The JS `ThemeManager` class toggles `data-theme="dark"` on `document.documentElement`. CSS handles the rest via variable overrides in `[data-theme="dark"]`. Do not add inline style dark-mode overrides in JS — keep it in CSS variables.

### Contact Form
The form uses Formspree (`action="https://formspree.io/f/mgopvvvd"`). `ContactForm.handleSubmit()` submits via `fetch()` with `Accept: application/json`. The class has a null guard (`if (!this.form) return`) so pages without `#contact-form` don't error.

### Cross-Page Navigation — CRITICAL
`NavigationManager` in `script.js` intercepts **all** `.nav-link` clicks with `e.preventDefault()` and tries to scroll within the current page. This breaks cross-page links like `href="index.html#projects"`.

**Nav link class conventions:**
- `.nav-link` — use on `index.html` only, for in-page anchor scrolling (intercepted by JS)
- `.nav-link-ext` — use on other pages (e.g., `brand.html`) for links back to `index.html#section` (looks identical to `.nav-link`, NOT intercepted by JS)
- `.nav-link-brand` — use for the "My Brand" pill button in all navbars

### Adding a New Page
1. Create the `.html` file loading both `style.css` (shared variables/navbar/theme) and a page-specific `.css` file
2. Use `.nav-link-ext` for all navbar section links (never `.nav-link`)
3. Load `script.js` — `ThemeManager`, `NavbarManager`, and `MobileNavigation` work on all pages; other classes are no-ops if their elements don't exist
4. Add a `#footer-year` span in the footer (auto-populated by `script.js`)

---

## Development Workflow

### Running Locally
No build step needed. Options:
```bash
# Option 1: Python simple server
python3 -m http.server 8080

# Option 2: Node http-server (if installed)
npx http-server .

# Option 3: VS Code Live Server extension — open index.html, click "Go Live"
```

### Making Changes
1. Edit the relevant file (`index.html`, `style.css`, or `script.js`)
2. Refresh the browser — no compile step
3. Test both light and dark themes after CSS/HTML changes
4. Test at mobile widths (375px) after layout changes

### Testing Checklist (manual)
- [ ] Theme toggle works and persists on reload
- [ ] Mobile hamburger menu opens/closes
- [ ] Smooth scroll navigation works
- [ ] Project filter buttons show/hide correct cards
- [ ] Contact form validates and shows success/error messages
- [ ] Page loads without console errors

---

## Deployment

This is a static site. Deploy by uploading all files (except `.git/`) to:
- **GitHub Pages**: push to `main`/`master`, enable Pages in repo settings
- **Netlify / Vercel**: connect repo, no build command needed, publish directory is `.` (root)
- **Any web host**: upload files via FTP/SFTP

---

## Assets

| File | Purpose |
|---|---|
| `Jordan_Foltz_Data_Analyst_Resume.pdf` | Primary resume download (generated from the DOCX — regenerate when the DOCX changes) |
| `Jordan Foltz Data Analyst Resume.docx` | Editable resume download |
| `Facebook_Engagemnt_Algorithm_AB_Test_Case_Study.pdf` | Case study: two-proportion z-test, +14.4% relative lift, p = 0.043 |
| `Mobile_Site_Survey_AB_Testing_Case_Study.pdf` | Case study: Welch t-test, no significant lift, p = 0.149 |
| `Referral_Campaign_Jordan_Foltz.pdf` | Case study: counterfactual ROI model, 16.52% ROI |
| `Headshot Professional Resized.jpg` | Profile photo (hero on index.html and brand.html) — ~1 MB, should be compressed |

---

## Known Issues / TODOs

- `Headshot Professional Resized.jpg` is ~1 MB — compress to ~100 KB (WebP or optimized JPG)
- Asset filenames with spaces (`Headshot Professional Resized.jpg`, `Jordan Foltz Data Analyst Resume.docx`) should be renamed to underscores; `Engagemnt` typo in the Facebook PDF filename
- SVG favicon added (`favicon.svg` — normal-distribution bell curve in brand green/gold, linked from `index.html` + `brand.html`). Open Graph/Twitter card tags and JSON-LD structured data still not added
- YouTube iframes load eagerly — add `loading="lazy"`
- `prefers-reduced-motion` and `prefers-color-scheme` are not respected
- The `@keyframes ripple` animation is injected via JS `createElement('style')` — consider moving to `style.css`
- Tableau Public profile (https://public.tableau.com/app/profile/jordan.foltz/vizzes) is not yet linked from the site — waiting on a new dashboard project before showcasing
