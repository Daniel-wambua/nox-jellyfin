# Nox

A dark theme for Jellyfin Web. Near-black page, a floating black navigation rail that expands on hover, gold accent, rounded poster cards, scrimmed heroes, and a login screen that matches the rest of the client.

Built entirely from standard CSS custom properties. One import line, no plugins, no build step, no external assets.

---

## Install

Jellyfin Web → **Dashboard → General → Custom CSS**, and paste one of these.

**Pinned to a release** (recommended):

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.1.4/nox.css');
```

The `@import` must be the first rule in the box. If Jellyfin's editor already has something above it, move it up or the import is silently dropped.

**Tracking the branch** — `@main` follows the head of the repository, but jsDelivr caches a branch ref for up to twelve hours and purging it does not reliably reach every edge. A stale branch copy loads silently: no console error, no failed request, just an older build of the theme. If the theme applies but looks like a version you have already replaced, switch to the pinned release above:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css');
```

**Pinned to an exact commit** — cannot go stale in either direction:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@1d89e4fcbf0c62c087c724569386768122f92787/nox.css');
```

**Self-hosted** — copy `nox.css` (or `nox.min.css`) onto your server and point at it:

```css
@import url('https://your.server/path/nox.css');
```

Hard-refresh after saving — <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd>, or <kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd> on macOS. Jellyfin caches the stylesheet aggressively and a normal reload will often keep the old one.

---

## What it covers

| Surface | What Nox does |
|---|---|
| **Navigation** | Replaces the top bar with a floating black pill rail on the left: icon over label, widening on hover or keyboard focus. Covers both app shells — the legacy `.skinHeader` and the MUI header newer clients render. Below 1100px the rail is switched off and the bar keeps Jellyfin's layout with the theme's surface, hover fill and accent. |
| **Home rows** | Poster cards at 10 × 15 rem with a 0.5 rem radius, a hover lift and shadow, progress bars in the accent, scroll arrows that appear on hover only. |
| **Hero** | Full-width artwork in a rounded frame, a dark scrim for text contrast, the title in a display face, and the action buttons on a black plaque cut into the card. |
| **Detail pages** | Same hero treatment, plus themed tabs, metadata, and the full action row. |
| **Player** | Gradient control bar, themed seek bar and volume, chapter thumbnails, subtitle styling. |
| **Dialogs** | Rounded, blurred backdrop, themed menus, action sheets, toasts and tooltips. |
| **Forms** | Inputs, selects, checkboxes and switches restyled for both dark and light palettes. |
| **Login & setup** | The login page and first-run wizard are themed, not left as stock Jellyfin. |
| **Empty states** | Themed, including the section headings above them. |

---

## The navigation rail

This is the part that changes the shape of the client, so it is worth explaining.

Above 1100px wide, `.skinHeader` stops being a bar across the top and becomes a fixed pill inset 0.75rem from the top, bottom and left edges, 4.5rem wide, with a 1.25rem radius and a shadow on all sides — it reads as a physical object floating over the page rather than a strip stuck to the top.

Each button is laid out as a column: icon on top, label underneath. The label is not markup Nox adds — Jellyfin does not expose one. It comes from the `title` attribute the client already writes onto every header button for accessibility, surfaced through a pseudo-element:

```css
.headerButton::after { content: attr(title); }
```

The `title` attribute is Jellyfin's own localised string, so the labels are translated on a non-English server with no work from the theme. The trade-off is that labels can only appear where Jellyfin sets `title`; if a future version drops it on a button, that one button shows its icon alone and the rest are unaffected.

On hover, or on keyboard focus anywhere inside the rail (`:focus-within`), the rail widens to 10.25rem and the labels fade in. The page's left padding is derived from the collapsed width, so content never shifts when the rail opens.

The rail places the Menu button at the top and the remaining actions — search, cast, notifications, settings, user — at the bottom, using `margin-top: auto` on the right-hand group.

### Two things Jellyfin's header does that shape these rules

Both were found by measuring a real page rather than by reading the stylesheet, and both produce the same symptom: the theme loads, the page retints, and the header stays exactly as Jellyfin shipped it.

**The buttons are not children of the header.** Jellyfin wraps `.headerLeft` and `.headerRight` in `.headerTop`, a row-flex wrapper that also carries `.flex-grow`. A rule that turns `.skinHeader` into a column without turning `.headerTop` into one too leaves the two groups sitting side by side inside a 4.5rem box — `.headerLeft` collapses to zero width and the rail renders as an empty strip. Section 3 re-lays-out `.headerTop` as the pill's single column.

**Specificity ties are decided by load order.** Jellyfin's own `.skinHeader { position: fixed; left: 0; right: 0; top: 0 }` has exactly the same specificity as a plain `.skinHeader` rule of ours, and it is served from a `<link>` in `<head>` while Custom CSS is injected as a `<style>` in the body. Whichever the browser parses last wins, so a plain selector can silently do nothing at all. Every header rule in `nox.css` is therefore written `body .skinHeader ...` — (0,1,1) against Jellyfin's (0,1,0). It wins on specificity rather than on luck, and it does so without `!important`.

The header has been measured in both orders, theme-first and theme-last, and computes identically either way.

### There are two app shells

Recent Jellyfin clients ship a second, MUI-based shell around the same page content. The old markup is still in the DOM, but parked inside a `display: none` wrapper — `.skinHeader`, `.mainDrawer` and the drawer handle are all in there — while the header a viewer actually sees is `<header class="MuiAppBar-root MuiPaper-root …">`. On those clients a theme written only against `.skinHeader` styles a subtree nobody renders. The palette still lands, because it is set on `:root`; the header looks untouched. That is the signature of this failure, and it is easy to mistake for a broken install.

Nox handles both. Section 3 styles the legacy `.skinHeader`; section 3b styles the MUI header as the same pill, with the navigation stack at the top, the account actions pinned to the bottom, and the page offset by the collapsed rail width.

Two details of MUI's CSS shape section 3b. Its component rules are emitted as **two-class** selectors — `.css-h48fi.MuiAppBar-root` — which outrank one class plus any number of element names, so `body header.MuiAppBar-root` silently loses on `width`, `padding`, `border-radius`, `inset` and `z-index`, and the rail reverts to a full-width bar. Every rule there therefore also names the element's second MUI class (`header.MuiAppBar-root.MuiPaper-root`), which wins on class count rather than on `!important`. Separately, Jellyfin pins the Search button with `position: absolute` outside the toolbar's flow; that one declaration is forced, and it is the only `!important` the rail needs.

The MUI class names are stable (they are MUI's own, not Jellyfin's generated ones), but the *shape* of the header is not — if a future client reworks it, section 3b is the first place to look.

### Below 1100px

The rail is switched off entirely and section 4 restyles the normal top bar, because a fixed side rail and a phone-width viewport do not coexist. That bar keeps Jellyfin's layout — the buttons stay where the client's own keyboard and screen-reader order expects them — but takes the theme's surface: transparent over the top of the page, a translucent blurred surface with a hairline once the page scrolls, a round hover fill on each icon button, and the accent on whichever of Home or Menu is the leading entry.

---

## Customising

### Change the accent

Two properties, near the top of `nox.css` or in `tokens.css`:

```css
:root {
  --nox-accent: #ffd700;          /* the colour itself */
  --nox-accent-rgb: 255, 215, 0;  /* the same colour as a triplet, for tints */
}
```

Every accent surface — play buttons, tab underlines, progress fills, seek bar, focus rings, the highlighted menu item — reads from those two. `--nox-accent-rgb` exists separately because tinted states are built with `rgba(var(--nox-accent-rgb), 0.12)`, which cannot take a hex value. Keep the two in sync.

`--nox-on-accent` is the text colour used *on* an accent fill. It is black by default because gold cannot carry white text at any size. Check the contrast before changing it (see [Accessibility](#accessibility)).

### Switch palette

Fourteen alternates ship alongside the default. Apply one as a class on `<html>` — or, if you cannot add a class, paste the block from `tokens.css` into Custom CSS with `:root` in place of the class name:

| Class | Mood |
|---|---|
| *(default)* | Warm near-black, gold |
| `.nox-charcoal` | Neutral greys, gold |
| `.nox-evergreen` | Deep green, mint |
| `.nox-midnight` | Navy, ice blue |
| `.nox-aurora` | Indigo, violet |
| `.nox-obsidian-luxury` | Warm black, antique gold |
| `.nox-nordic-frost` | Cool slate, pale cyan |
| `.nox-slate-corporate` | Steel, corporate blue |
| `.nox-royal-amethyst` | Plum, orchid |
| `.nox-warm-terracotta` | Brown, terracotta |
| `.nox-monochrome-pro` | Pure greyscale |
| `.nox-light` | Light, dark gold |
| `.nox-oyster` | Warm off-white, dark gold |
| `.nox-arctic-dawn` | Blue-white, sky blue |
| `.nox-enterprise-light` | Neutral white, blue |

### Retune the layout

Everything spatial is a token too: `--nox-radius-card`, `--nox-card-width`, `--nox-card-gap`, `--nox-rail-w`, `--nox-rail-w-open`, `--nox-dur-rail`. Override any of them in Custom CSS after the import and the theme follows.

### Optional mono font

The theme asks for Roboto Mono and falls back to the platform's monospace. It does not load the web font: a media server should not need a Google Fonts request to render its own login screen. If you want it anyway, uncomment the `@import` in `tokens.css` and put it at the very top of the CSS box, above everything else.

---

## Files

| File | Purpose |
|---|---|
| `nox.css` | The theme. This is the file you install. ~1900 lines, 17 numbered sections. |
| `nox.min.css` | Minified build of the same file. Same rules, smaller transfer. |
| `tokens.css` | Standalone token layer: the `:root` block, all 15 palettes and the Jellyfin bridge. Optional. |
| `palette.json` | Every token with its value, the rule that declares it, and the selectors that consume it. Includes measured contrast ratios. |
| `manifest.json` | Metadata, palette list and file map. |
| `screenshots.md` | A checklist for verifying a build by hand. |
| `LICENSE` | MIT. |

`nox.css` is organised in numbered sections so you can jump straight to what you want to change:

```
 0. Design tokens          9. Detail page
 1. Global base           10. Video player / OSD
 2. Header base           11. Dialogs, toasts, menus
 3. Navigation rail       12. Forms, inputs, buttons
 4. Top bar (<1100px)     13. Login and first-run setup
 5. Section headings      14. Responsive
 6. Hero and backdrop     15. Motion, fallbacks, accessibility
 7. Carousel rows         16. BRITTLE  ← fragile selectors, all in one place
 8. Cards
```

Section 3 carries both headers: 3 styles the legacy `.skinHeader`, 3b the MUI header newer clients render.

---

## Update resilience

Jellyfin Web changes its DOM between releases, and any theme that reaches into it will eventually break somewhere. Nox is built to break *locally and visibly* rather than globally:

- **Class hooks only** — no `#id` selectors, no `> :nth-child()` chains, no attribute selectors on generated markup.
- **Everything flows from tokens.** A broken layout rule cannot take the palette down with it, and a palette change cannot break layout.
- **One BRITTLE section.** Every selector that depends on something Jellyfin has *not* promised to keep — the `--jf-palette-*` MUI bridge, the now-playing bar, settings cards, skeleton placeholders — is collected in section 16. If a Jellyfin update breaks the theme, start there. If something breaks outside section 16, it is a bug worth reporting.
- **Fallbacks, not assumptions.** `@supports` guards for `backdrop-filter` and `color-mix()`, `forced-colors` support, and a `prefers-reduced-motion` block that disables all motion rather than merely shortening it.
- **`!important` only where a stylesheet cannot win** — 24 declarations, each with a comment giving its reason. They fall into five groups: Jellyfin writes the dashboard backdrop colour onto `<body>` as an inline style; it paints the header's background inline the same way; it paints the seek and volume fills and the tooltips inline; MUI's skeleton placeholders set `--base-color` and `--highlight-color` as inline custom properties; and MUI pins one header button with `position: absolute`. The reduced-motion block is a sixth — it has to beat every transition the theme declares. Everywhere else, a `body` prefix or a longer selector does the job instead, including the rest of the MUI shell, which is matched on class count rather than forced.

---

## Accessibility

- **Focus is visible everywhere.** `:focus-visible` draws a 2px translucent accent ring that works over flat panels and artwork alike.
- **The rail opens on keyboard focus**, not just hover, via `:focus-within` — the labels are reachable without a mouse.
- **Reduced motion is honoured.** All transitions and animations drop to 0.001ms, and the card hover transform is removed outright.
- **Forced-colours mode** is left to the OS: Nox gives up its own colours there rather than fighting the user's contrast settings.
- **Contrast**, measured from the shipped hex values for the default palette: body text 17.5:1, secondary text 8.2:1, accent on background 12.5:1, accent fill against its own text 15.0:1. `--nox-muted` is 3.2:1 and is used only for large text and non-text UI. The light palettes are all above 4.5:1 on text; full figures are in `palette.json`.

---

## Known gaps

Honest list, so you know what you are installing:

1. **Measured against Jellyfin Web 13.0.0, not walked through every screen.** The header markup, the button classes and the `title`-attribute behaviour come from `jellyfin-web` 13.0.0's own source and its compiled stylesheets, and the theme has been checked in a reproduction built from those exact files, in both stylesheet orders. It has also been confirmed to load in a live Jellyfin Server 12.4.0 instance. What has *not* been seen rendering against real content: the video OSD, the now-playing bar and plugin-rendered panels. Expect one or two rough edges there.
2. **The rail needs 1100px.** Below that width the theme keeps Jellyfin's horizontal bar rather than squeezing a rail into it. That bar is themed, but its layout is still Jellyfin's, so a window narrower than 1100px will look less changed than a wider one. If the header still reads as stock, check `innerWidth` in the console before assuming the theme failed.
3. **Labels depend on `title` attributes.** Where Jellyfin does not set one on a header button, that button shows its icon only.
4. **Home-screen hero is conditional.** Nox styles a hero when one exists in the DOM. Stock Jellyfin's home screen may not render one without a plugin that adds hero artwork; the hero styling is exercised on detail pages regardless.
5. **No external fonts or images by default.** Deliberate. If you install the optional mono `@import`, Google Fonts becomes a runtime dependency of your server's login screen.
6. **Light palettes are secondary.** They work, and they pass contrast, but the theme's spacing and shadow choices were tuned for dark surfaces first.

---

## If nothing changes at all

A theme that applies but looks wrong is a design problem. A theme where *nothing*
changes is an install problem, and there are only three candidates. Work through
these in order — each takes seconds.

### 1. Does Jellyfin apply the CSS box at all?

Paste this into Custom CSS, replacing whatever is there, and save:

```css
#reactRoot { outline: 6px solid red !important; }
```

Hard-refresh. **No red outline around the page?** Jellyfin is not applying Custom
CSS at all. Check:

- You are in **Dashboard → General → Custom CSS** (scroll to the bottom), not a
  per-user or per-library setting.
- You clicked **Save** below the box — the setting does not autosave.
- You are testing in a **web browser**. Custom CSS only reaches the web client;
  the Android TV, Android, iOS and Roku apps ignore it entirely.

If you do get the red outline, the box works and the problem is step 2 or 3.

### 2. Can your browser reach the CDN?

Open this in a new tab:

```
https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.1.4/nox.css
```

It should display the stylesheet as plain text. If it 404s, hangs, or is blocked,
your network or DNS filter cannot reach jsDelivr — common on self-hosted setups
behind Pi-hole or with outbound filtering. Use the self-hosted route below.

### 3. Is the `@import` the first rule?

If anything at all precedes the `@import` — a comment, a variable, a leftover
rule from another theme — browsers **drop the import silently**, with no console
message. Delete everything above it.

### The route that cannot fail

Paste the **entire contents** of `nox.css` into the box instead of importing it.
No network request, no import-ordering rule, no CDN cache. If step 1 gave you a
red outline, this works. Open the file, select all, paste, save, hard-refresh.

### The page retinted but the header is still Jellyfin's

The theme is applied — this is not an install problem. Five causes, in order of
likelihood.

**You are on a stale CDN copy.** If you import through `@main`, jsDelivr can keep
serving an older build of the file for up to twelve hours after a change, and the
symptom is indistinguishable from a broken theme: the palette updates, the header
does not. There is no error anywhere. Compare what your browser actually has
against jsDelivr by opening both in new tabs and checking the file size —
the current build is 51,621 bytes:

```
https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css
https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.1.4/nox.css
```

If the `@main` copy is smaller, switch the import to `@0.1.2`.

**You are under 1100px.** The rail only exists at 1100px and wider; below that
the theme deliberately keeps Jellyfin's horizontal bar. Widen the window and the
pill should appear. To see what your browser actually reports, type `innerWidth`
in the console.

**You are on 0.1.1 or earlier.** In those versions every header rule had the same
specificity as Jellyfin's own, so whether it applied at all came down to which
stylesheet the browser parsed last — and even when it did apply, the rail
collapsed to an empty strip because Jellyfin wraps the header buttons in a
wrapper the theme did not handle. Both are fixed in 0.1.2 and later. Update, then
hard-refresh.

**Your client renders the MUI app shell, and you are on 0.1.3 or earlier.**
Recent clients wrap the page in an MUI header (`<header class="MuiAppBar-root …">`)
and park the old `.skinHeader` markup inside a `display: none` wrapper. Before
0.1.4 the theme styled only the hidden copy, so the palette changed and the
header did not. Check in the console:

```js
document.querySelector('header.MuiAppBar-root')   // null on old clients
```

If that returns an element, update to 0.1.4 or later.

**A stale stylesheet.** The rail's width, radius and background all live in the
same rules, so a cached older `nox.css` shows the old behaviour with the new
palette. Hard-refresh, and if you install through jsDelivr, purge the cache for
the tag you pinned.

### Self-hosting

Put `nox.css` somewhere your reverse proxy already serves, and import it with a
relative path:

```css
@import url('/nox.css');
```

If you copy it into Jellyfin's own web root (inside a Docker container this is
usually `/usr/share/jellyfin/web/`), note that the file is removed by a Jellyfin
image upgrade and must be copied back.

---

## Verifying an install

After saving the CSS and hard-refreshing:

1. The page background is `#1b1919`, not Jellyfin's default blue-grey.
2. Above 1100px the header is a black pill on the left, not a bar across the top.
3. Hovering that pill widens it and reveals labels under each icon.
4. Play buttons and progress bars are gold, not the default accent.
5. Open a library item — the artwork sits in a rounded frame with the action buttons on a black plaque.
6. Sign out — the login screen is themed too, including the submit button.

If none of that happened, the `@import` was not the first rule in the box, or the browser served a cached stylesheet.

---

## License

MIT — see [LICENSE](LICENSE).

Nox is an independent theme. It contains no third-party artwork, fonts, logos or stylesheet code; the palette and measurements are its own.