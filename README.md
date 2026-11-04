# Nox

A dark theme for Jellyfin Web. Near-black page, a floating black navigation rail that expands on hover, gold accent, rounded poster cards, scrimmed heroes, and a login screen that matches the rest of the client.

Built entirely from standard CSS custom properties. One import line, no plugins, no build step, no external assets.

---

## Install

Jellyfin Web → **Dashboard → General → Custom CSS**, and paste one of these.

**From jsDelivr** (recommended — you get updates):

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css');
```

The `@import` must be the first rule in the box. If Jellyfin's editor already has something above it, move it up or the import is silently dropped.

**Pinned to a release** (nothing changes under you):

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.1.1/nox.css');
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
| **Navigation** | Replaces the top bar with a floating black pill rail on the left: icon over label, widening on hover or keyboard focus. Below 1100px it falls back to the normal top bar. |
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

Below 1100px the rail is switched off entirely and section 4 restyles the normal top bar, because a fixed side rail and a phone-width viewport do not coexist.

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
| `nox.css` | The theme. This is the file you install. ~1570 lines, 16 numbered sections. |
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

---

## Update resilience

Jellyfin Web changes its DOM between releases, and any theme that reaches into it will eventually break somewhere. Nox is built to break *locally and visibly* rather than globally:

- **Class hooks only** — no `#id` selectors, no `> :nth-child()` chains, no attribute selectors on generated markup.
- **Everything flows from tokens.** A broken layout rule cannot take the palette down with it, and a palette change cannot break layout.
- **One BRITTLE section.** Every selector that depends on something Jellyfin has *not* promised to keep — the `--jf-palette-*` MUI bridge, the now-playing bar, settings cards, skeleton placeholders — is collected in section 16. If a Jellyfin update breaks the theme, start there. If something breaks outside section 16, it is a bug worth reporting.
- **Fallbacks, not assumptions.** `@supports` guards for `backdrop-filter` and `color-mix()`, `forced-colors` support, and a `prefers-reduced-motion` block that disables all motion rather than merely shortening it.
- **`!important` only where inline styles force it** — 17 declarations, each with a comment saying why. Jellyfin writes background colours onto `<body>` and the header as inline styles once a dashboard backdrop is configured, and an inline style beats any stylesheet rule.

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

1. **Not yet verified in a live Jellyfin instance.** The theme was developed and measured against a reproduction of Jellyfin Web's DOM, not against a running server. The class names, the header structure and the `title`-attribute behaviour come from Jellyfin Web's own markup, but some surfaces — most notably the video OSD, the now-playing bar and plugin-rendered panels — have not been seen rendering against real content. Expect to find one or two rough edges there.
2. **The rail needs 1100px.** Below that, the theme uses the standard top bar. A collapsing rail at tablet width was judged worse than a good top bar.
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
https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css
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