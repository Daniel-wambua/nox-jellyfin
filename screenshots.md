# Nox — visual verification checklist

A manual pass for confirming a build looks right. The theme was developed against
measured computed styles rather than screenshots, so these captures exist to catch
the things measurement cannot: an overlap, a colour that reads wrong against real
artwork, a label that clips.

Capture at **1920 × 1080**, DPR 1, in a browser profile with no extensions. Name
files `nox-<page>-<state>.png` and keep them out of the repo — `.gitignore`
already excludes `*.png` and `screenshots/`.

---

## Before you start

- [ ] Installed via the `@import` line, and it is the **first** rule in the box.
- [ ] Hard-refreshed (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>R</kbd>).
- [ ] DevTools → Network: the stylesheet loaded, not served from disk cache.
- [ ] At least one library has real poster artwork and one item has a backdrop —
      a theme that only ever sees grey placeholders proves very little.
- [ ] DevTools → Rendering → **Emulate `prefers-reduced-motion`** for the motion pass.

---

## 1. Navigation rail (≥1100px)

| | |
|---|---|
| **Capture** | `nox-rail-rest.png`, `nox-rail-open.png` |
| **Setup** | Viewport ≥1100px wide |
| **Check** | Rail is a black pill inset from the top, bottom and left edges — not a bar across the top. Radius is visibly soft (~1.25rem). It has a shadow on all four sides, so it reads as floating. |
| **Open state** | Hovering, or tabbing into it, widens the rail and fades in a label under each icon. The labels must be legible and must not clip — the longest is usually "Notifications". |
| **Layout** | Menu sits alone at the top. Search, cast, notifications, settings and the user button sit at the bottom. |
| **Watch for** | Content jumping sideways when the rail opens (it must not — page padding is fixed to the collapsed width); the rail overlapping a card; a scrolled row visible *through* the rail. |
| **Contrast** | The active/first button carries a gold tint at 12% alpha. Over the rail's black this should read as a tint, not a colour. |

## 2. Navigation fallback (<1100px)

| | |
|---|---|
| **Capture** | `nox-topbar-<width>.png` at 900px, 768px, 390px |
| **Check** | The rail is gone entirely and a normal top bar is back. At the top of the page the bar is transparent; once scrolled it becomes translucent with a blur and a bottom hairline. |
| **Watch for** | Any remnant of the rail — leftover left padding, a visible pill, a stray `margin-top: auto`. All three are the failure modes of section 4 not winning. |
| **No-blur fallback** | With `backdrop-filter` disabled in DevTools, the scrolled bar must be **solid**, not half-transparent with nothing behind it. |

## 3. Home rows and cards

| | |
|---|---|
| **Capture** | `nox-home-rows.png`, `nox-card-hover.png` |
| **Check** | Posters are 2:3, ~160 × 240px, 0.5rem radius, ~1.2rem apart. Section titles are ~1.7rem in the secondary blue-grey, not white. |
| **Hover** | A card lifts ~2px, scales ~2%, and gains a shadow. The resting card must have **no** shadow — twelve shadowed posters in a row is the failure this rule exists to prevent. |
| **Progress bar** | In-progress items show an accent-coloured bar. It should be a clean 0.3rem line, not a full-width overlay. |
| **Arrows** | Scroller arrows appear on hover only, and are circular. |
| **Scrollbar** | Horizontal scroller shows no visible scrollbar. |
| **Watch for** | Titles wrapping to three lines and pushing the row height around; a card's hover shadow being clipped by the scroller's overflow. |

## 4. Hero — detail page

| | |
|---|---|
| **Capture** | `nox-hero-detail.png` |
| **Check** | Artwork fills a rounded frame (~2rem). A scrim darkens the lower-left so the title reads against **any** artwork — test with something bright, not a dark poster. |
| **Title** | Display face, `clamp(1.8rem, 4vw, 3rem)`, white. |
| **Plaque** | The action buttons sit on a black plaque with a ~1.5rem radius, separated from the hero by a visible notch of page colour. This is the signature detail — if the plaque looks like a plain black box, the outline is not applying. |
| **Play button** | Gold fill, black label. |
| **Watch for** | The overview text running under the plaque; the plaque overflowing the hero frame at narrow widths; the notch disappearing because the outline colour matches the surface. |

## 5. Hero — home screen

| | |
|---|---|
| **Capture** | `nox-hero-home.png` |
| **Check** | If a hero renders, it must match the detail-page treatment. Stock Jellyfin's home screen may render no hero at all without a plugin supplying backdrop artwork — that is expected, and the detail page is the real test. |

## 6. Cards on a detail page

| | |
|---|---|
| **Capture** | `nox-detail-cast.png` |
| **Check** | Cast and "More like this" rows inherit the home-row card treatment. Episode lists use the same radius and hairline as the rest of the theme. |

## 7. Video player

| | |
|---|---|
| **Capture** | `nox-player.png`, `nox-player-seek.png` |
| **Check** | Control bar is a black-to-transparent gradient — solid at the bottom edge, gone by the top of the bar. Title in the display face, time and technical text in mono. |
| **Seek bar** | The filled portion is the accent. This one is forced with `!important` because the client paints it inline; if the bar is the stock colour, that rule lost. |
| **Chapter thumbnails** | Rounded, with a themed bubble. |
| **Player stats** | Overlay text is themed and legible over video. |
| **Watch for** | Controls legible against a bright scene — scrub to something white and confirm the gradient still does its job. |

## 8. Dialogs, menus and toasts

| | |
|---|---|
| **Capture** | `nox-dialog.png`, `nox-menu.png`, `nox-toast.png` |
| **Check** | Modals are rounded (~1rem) with a blurred, dimmed backdrop. Menus and action sheets use the raised surface, not pure black. |
| **Tooltips** | Darkened and rounded. |
| **Watch for** | A dialog whose backdrop blur is missing but which is still dimmed (correct fallback), or one that is fully transparent (broken). |

## 9. Forms

| | |
|---|---|
| **Capture** | `nox-forms.png` |
| **Check** | Inputs have a hairline border, a slightly tighter radius than cards (~0.3rem), and an accent-tinted border plus ring on focus. |
| **Selects and switches** | Themed, including the dropdown panel — that panel is often portalled to the end of `<body>`, so check it is themed too. |
| **Buttons** | Raised buttons use the accent with black labels. |

## 10. Login and first-run setup

| | |
|---|---|
| **Capture** | `nox-login.png` at 1920px and 390px |
| **Check** | The login screen is themed — background, input borders, focus states, and a gold submit button with a black label. This is the first page anyone sees and the easiest one to forget. |
| **Responsive** | At phone width the form must not overflow and the submit button must stay full width. |
| **First-run wizard** | Walk the setup wizard. Every step is a form; confirm none falls back to stock blue. |
| **Watch for** | A logo area that still shows a stock Jellyfin image; the theme does not remove or replace it. |

## 11. Motion

| | |
|---|---|
| **Capture** | `nox-reduced-motion.png` |
| **Check** | With `prefers-reduced-motion: reduce` emulated: no rail expansion animation, no card lift or scale, no dialog transition. Everything appears in place. |
| **Watch for** | A transform still applying on hover even though the transition is gone — that is the bug the rule exists to prevent, and it is invisible without the emulation on. |

## 12. Focus and keyboard

| | |
|---|---|
| **Capture** | `nox-focus-ring.png` |
| **Check** | Tab through the header, a row of cards, and a dialog. Every stop shows a visible 2px accent ring. |
| **Rail** | Tabbing into the rail expands it — labels must be reachable without a mouse. |
| **Watch for** | A focus ring that is invisible on the gold play button (low contrast) or clipped by an `overflow: hidden` ancestor. |

## 13. Palettes

| | |
|---|---|
| **Capture** | One home-screen shot per palette you intend to ship |
| **Check** | Apply each palette class to `<html>`. The page background, card surfaces, text tiers and accent all move together. Nothing keeps the previous palette's colour. |
| **Light palettes** | `.nox-light`, `.nox-oyster`, `.nox-arctic-dawn`, `.nox-enterprise-light` — check the submit and play buttons specifically. Their labels are black on the accent, which is what keeps them legible. |
| **Watch for** | Hover tints staying gold after switching to a non-gold palette — that would mean a literal colour leaked into a rule instead of `rgba(var(--nox-accent-rgb), …)`. |

## 14. Stress cases

| | |
|---|---|
| **Long titles** | Add an item with a 120-character name. Cards should clamp rather than grow; the hero title should wrap without pushing the plaque off-screen. |
| **Missing artwork** | An item with no poster should show a themed placeholder, not a white box. |
| **Many rows** | A home screen with 20+ rows: section rhythm stays even, and scrolling stays smooth. |
| **Empty library** | Themed empty state, not a bare grey message. |
| **Wide** | 3440px: the rail keeps its inset and does not stretch. |

---

## Reporting a break

Note the Jellyfin version (Dashboard → General), the browser, the viewport width,
and whether the broken surface is inside section 16 (**BRITTLE**) of `nox.css`.
Section 16 is expected to need maintenance as Jellyfin changes; anything outside
it is a bug in the theme.
