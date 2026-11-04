# Nox

A dark theme for **Jellyfin Web**, built from design tokens measured on a modern
streaming UI and re-expressed as CSS for Jellyfin's own class structure.

```
nox.css        the theme — self-contained, this is the only file Jellyfin needs
nox.min.css    the same stylesheet on one line
tokens.css     the token layer, standalone (for reference / forking)
palette.json   every token with the stylesheet + selector it was sampled from
manifest.json  plugin skeleton
```

No artwork, logos, fonts or stylesheets were copied from the reference. Every
value in `palette.json` cites where it came from.

---

## Install

### A. One line (recommended)

Dashboard → **General → Custom CSS**:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css');
```

Save, then hard-reload the web client (`Ctrl`/`Cmd` + `Shift` + `R`).

That is the whole install. The `@import` must be the **first rule** in the box —
anything before it (other than comments) makes browsers drop it.

> **Pin a version instead of `@main`.** Branch refs on jsDelivr are cached for
> up to 12 hours, and `@main` follows whatever you last pushed. For a stable
> install your users can rely on:
> ```css
> @import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.1.0/nox.css');
> ```
> Tags are immutable and cached permanently, so they are also faster.

### B. Paste

Copy the entire contents of `nox.css` into the Custom CSS box. Use this when you
want to edit the theme locally or run on a server with no outbound internet.

### C. As a plugin theme

1. Create `<config>/plugins/Nox_0.1.0/`
2. Copy `nox.css`, `tokens.css`, `nox.min.css` and `manifest.json` into it.
3. Replace the placeholder `guid` in `manifest.json` with a real UUID
   (`uuidgen`) before publishing — the all-zero GUID is a placeholder and two
   plugins sharing it will collide.
4. Restart Jellyfin. The theme appears under **Dashboard → General → Theme**.

### Change the accent

One property drives every accent surface:

```css
:root { --nox-accent: #f44336; --nox-accent-rgb: 244,67,54; }
```

Update **both** — the focus ring composes `rgba()` from `--nox-accent-rgb`, and
`box-shadow` cannot use `color-mix()` in every engine Jellyfin runs in.

Append it to your Custom CSS *after* the import:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css');
:root { --nox-accent: #2196f3; --nox-accent-rgb: 33,150,243; }
```

### Switch palette

Add a class to `<html>` — a small custom-JS snippet, or your reverse proxy:

```js
document.documentElement.classList.add('nox-midnight');
```

Fourteen alternates ship in `nox.css` section 0: `nox-light`, `nox-charcoal`,
`nox-evergreen`, `nox-midnight`, `nox-oyster`, `nox-aurora`,
`nox-obsidian-luxury`, `nox-arctic-dawn`, `nox-nordic-frost`,
`nox-slate-corporate`, `nox-royal-amethyst`, `nox-warm-terracotta`,
`nox-monochrome-pro`, `nox-enterprise-light`.

---

## Updating the CDN copy

jsDelivr caches branch refs (`@main`) for up to 12 hours. After a push, you can
force a refresh instead of waiting:

```bash
curl https://purge.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css
```

Or cut a release and point the import at the tag — tags never go stale:

```bash
git tag 0.1.1 && git push origin 0.1.1
```

---

## Coverage

| Area | Status | What Nox does |
|---|---|---|
| Global base | ✅ | Near-black page, display + body font stack, accent selection, thin themed page scrollbar, accent focus rings, reduced-motion guard |
| Top nav | ✅ | Transparent header, blur + hairline border once scrolled, icon spacing and hover wash, logo area neutralised |
| Home hero | ⚠️ partial | Gradient scrim, meta panel with the sampled outline notch, Watch/Details pill pair. **Needs a hero to exist** — see gaps |
| Home carousel rows | ✅ | Reference gap/padding/margins, smooth scroll, hidden-until-hover scrollbar, hover-revealed circular arrows, optional x-proximity snap |
| Cards | ✅ | Rounded poster, 2:3 ratio, hover shadow + subtle lift, 2-line clamped title, footer gradient, accent progress bar |
| Detail page | ✅ | Backdrop with scrim, hierarchy, Play + secondary pill buttons, rounded episode/season list, accent tab underline |
| Player | ✅ | Minimal OSD, gradient control bar, accent seek fill, mono subtitles, themed stats/quality badges |
| Dialogs / toasts / menus | ✅ | Sampled overlay (`rgba(0,0,0,.7)` + `blur(10px) grayscale(.2)`), raised dialog card, themed toasts, drawer, tooltips |
| Login | ✅ | Centred raised card, display-face heading, themed inputs |
| Forms / inputs | ✅ | Themed text fields, selects, checkboxes, switches, submit buttons |
| Responsive | ✅ | 375 px and 1920 px tuned; touch devices drop hover affordances |

---

## Jellyfin classes targeted, and why

Every selector below is a stable upstream hook. Nothing here is generated at
runtime or hashed.

### Global

| Selector | Why |
|---|---|
| `html`, `body` | Page background and the font stack cascade from here |
| `::selection` | The reference marks selection with the accent |
| `:focus-visible` | Keyboard ring — the reference uses a 2 px translucent accent halo |
| `::-webkit-scrollbar*` | Page scrollbar; the reference hides scrollbars entirely (see gaps) |
| `h1`–`h6`, `.pageTitle`, `.sectionTitleText` | Display-face headings |
| `.backgroundContainer`, `.backdropContainer`, `.backdropImage` | Pages that paint a user backdrop behind the app |

### Header

| Selector | Why |
|---|---|
| `.skinHeader` | The whole top bar; transparency and transition live here |
| `.skinHeader.headroom--not-top` | Headroom.js scroll state — the blur trigger |
| `.skinHeader-withBackground` | Pages with no hero must not float a transparent bar |
| `.pageTitle`, `.pageTitleWithDefaultLogo` | Logo/title sizing; the default Jellyfin logo image is removed so no upstream artwork is involved |
| `.headerButton`, `.headerButtonIcon`, `.headerRight` | Icon size, spacing, hover wash |

### Home

| Selector | Why |
|---|---|
| `.verticalSection`, `.sectionTitleContainer`, `.sectionTitle`, `.sectionTitleText` | Row structure and the 1.7 rem / 27.2 px section heading |
| `.emby-scroller-container`, `.emby-scroller`, `.scrollSlider`, `.itemsContainer` | The horizontal row: gap, padding, scrollbar hiding, snap |
| `.emby-scrollbuttons`, `.emby-scrollbuttons-button` | Hover-revealed arrows |
| `.homePage .verticalSection:first-child` | Used as the hero host when no plugin renders `.nox-hero` |

### Cards

| Selector | Why |
|---|---|
| `.cardBox` | Snap target and outer spacing |
| `.card`, `.cardScalable`, `.cardImageContainer`, `.cardPadder-poster` | Radius, ratio, hover shadow, hover lift, image brightness |
| `.cardText`, `.cardText-first`, `.cardText-secondary`, `.cardText-icon` | Title clamp, muted metadata, star colour |
| `.innerCardFooter`, `.cardIndicators`, `.playedIndicator` | Overlay strip and badges |
| `.cardOverlayButton`, `.cardOverlayContainer` | Play/info/favourite overlay buttons |
| `.itemProgressBar`, `.itemProgressBarForeground`, `.itemProgressBar-denied` | Resume bar — the reference's `.3rem` track and radius |

### Detail

| Selector | Why |
|---|---|
| `.detailPageWrapperContainer`, `.detailPagePrimaryContainer`, `.backdropContainer`, `.detailPageContent` | Backdrop hero and the scrim that keeps text legible over arbitrary artwork |
| `.detailLogo`, `.itemName`, `.itemMiscInfo`, `.mediaInfoItem`, `.itemOverview` | Hierarchy and colour |
| `.detailButtons`, `.btnPlay`, `.detailButton`, `.mainDetailButtons` | Pill action row |
| `.detailSection`, `.itemDetailsGroup`, `.listItem*`, `.emby-tabs`, `.emby-tab-button` | Seasons, episodes, related items |

### Player

| Selector | Why |
|---|---|
| `.videoOsdBottom`, `.osdControls`, `.osdTitle`, `.osdTextContainer` | Chrome and typography |
| `.mdl-slider-background-flex*`, `.sliderBubble`, `.sliderMarker` | Seek/volume fill in the accent |
| `.chapterThumbContainer`, `.chapterThumbImage`, `.chapterThumbText` | Chapter previews |
| `.upNextContainer`, `.upNextDialog`, `.videoSubtitles`, `.playerStats` | Up-next card, subtitles, stats overlay |

### Dialogs, forms, login

| Selector | Why |
|---|---|
| `.dialogBackdrop`, `.dialog`, `.formDialog`, `.dialogHeader*`, `.dialogFooter`, `.dialogContentInner` | The sampled overlay + raised card treatment |
| `.toast`, `.toastContainer`, `.actionSheet*`, `.navMenuOption`, `.mainDrawer`, `.tooltip` | Toasts, popup menus, drawer, tooltips |
| `.emby-input`, `.emby-textarea`, `.emby-select`, `.emby-checkbox`, `.mdl-switch*`, `.emby-button`, `.raised`, `.button-flat` | Form controls |
| `.loginPage`, `.readOnlyContent`, `.btnForgotPassword` | Login card |
| `.settingsCard`, `.paperList`, `.searchfields`, `.countIndicator`, `.starIcon` | Settings, search, badges, ratings |

---

## What CSS cannot change

Stated plainly, because these are the places Nox will visibly differ from the
reference no matter how the tokens are tuned.

1. **The navigation rail.** The reference's navigation is a fixed *left vertical
   rail*. Jellyfin's `.skinHeader` is a horizontal top bar whose children are
   positioned by JS. CSS can restyle the bar, and it can technically rotate the
   children into a column — but doing so detaches the drawer button and the
   search field from where `layoutManager` expects them, and collides with the
   mobile drawer. An opt-in version is included commented-out in section 14d.
   Making the rail real needs JS, not CSS.

2. **A hero section on the home screen.** Jellyfin has no hero component. Nox
   can style one into existence *if something renders it*, or approximate it
   from the first home row when the server has backdrop images. Without either,
   the home screen opens straight into a row.

3. **Carousel paging.** The reference's rows scroll freely with no arrows and no
   snap. Nox can add `scroll-snap` and reveal Jellyfin's arrow buttons, but it
   cannot add paging logic, inertia tuning, or drag-to-scroll.

4. **Component structure.** Card internals, dialog internals and the OSD are
   built at runtime by `jellyfin-web`. Nox can only colour what those functions
   emit. Reordering or recomposing them is a JS problem.

5. **Hiding scrollbars globally.** The reference hides every scrollbar. Doing
   that to Jellyfin's document makes long libraries feel broken, so Nox keeps a
   thin themed page scrollbar and hides scrollbars only inside horizontal rows.
   This is a deliberate, documented deviation.

6. **Motion on route changes.** Jellyfin's page transitions are handled in JS.
   Nox styles hover/press/focus states only.

### Optional JS ideas (a later phase, not in this release)

- Inject a real hero component on the home screen from the first backdrop item.
- Add drag-to-scroll with inertia to `.emby-scroller`.
- Add arrow-button paging that scrolls by exactly one viewport.
- Mirror a `data-nox-theme` attribute onto `<html>` so the palette can follow
  the user's Jellyfin display-mode setting.
- Drive `--nox-accent` from a plugin configuration page, mirroring how the
  reference exposes the accent as a user preference rather than a brand colour.

---

## Update resilience

- **Stable hooks only.** Everything in sections 1–13 targets class names
  Jellyfin has kept stable across the 10.x line. No hashed names, no
  `nth-child` chains into generated markup.
- **One brittle section.** Section 14 of `nox.css` collects everything
  version-fragile: the `--jf-palette-*` bridge, the Headroom.js states, the
  `data-type="backdrop"` hero host, the optional rail, and the runtime-built OSD
  internals. **If a Jellyfin update breaks Nox, comment out section 14 first**
  and re-test. The theme is fully usable without any of it.
- **`!important` is rare and justified.** It appears three times in active code:
  1. `.skinHeader.headroom--not-top` background — Jellyfin writes an inline
     `background-color` on the header when a backdrop is configured.
  2. The `@supports not (backdrop-filter)` fallback — must beat the blur rule.
  3. `prefers-reduced-motion` — Jellyfin's own stylesheets declare transitions
     on nearly every interactive element and load after Custom CSS in some
     builds.
  Each carries an inline comment explaining it.

---

## Known gaps

- **The hero is conditional.** On a server with no backdrop images, the home
  screen has no hero band. See "What CSS cannot change".
- **Left rail not enabled by default.** Available commented-out; enabling it is
  a deliberate trade, not an oversight.
- **Accent default is a judgement call.** The reference does not ship a fixed
  brand accent — `--ascent-color` is a runtime user preference. Its CSS root
  default is `gold`, its light-mode root default is `blue`, and the live headless
  session resolved to `#2196f3`. The brief asked for red; Nox therefore defaults
  to the `Red` (`#f44336`) option from the reference's own accent picker. For
  fidelity to the *sampled default* instead, set `--nox-accent: gold` (or
  `#2196f3` for the live value). Documented rather than silently "corrected".
- **Card hover includes a scale.** The reference only swaps shadow and title
  colour. A restrained 2 % lift was added deliberately; delete the
  `.card:hover` transform rule for exact upstream behaviour.
- **Scroll snap is authored, not sampled.** The reference has no `scroll-snap`
  anywhere. Delete the two marked rules in section 5 for upstream behaviour.
- **Hero gradient is authored.** The reference paints no CSS gradient over its
  hero — it relies on the artwork. Jellyfin backdrops are arbitrary user images,
  so Nox adds a scrim anchored to the sampled `--bg-color`.
- **`color-mix()` is required.** A `@supports` fallback exists, but on a very old
  client the derived surfaces collapse to flat sampled colours and lose subtlety.
- **Focus ring is 1 px solid, not 2 px translucent.** A deliberate contrast
  choice against a near-black page; noted in `nox.css` section 1.
- **No light-mode auto-switching.** The light palettes ship as classes you apply
  manually. Automatic switching needs JS.
- **Not yet rendered in a live Jellyfin instance.** Values are verified against
  the reference by computed style and against the theme by static review. The
  side-by-side checklist in `screenshots.md` is the manual step.

---

## Verify

1. **Install check** — add the `@import` line, save, hard-reload. The login
   screen should be a raised dark card on a `#1b1919` page, and the header
   should be transparent until you scroll.
2. **Scroll check** — scroll the home screen. The header should gain a blur and
   a hairline border once it leaves the top, and lose them at the top.
3. **Hover check** — hover a poster. It should lift, gain a light shadow, and
   its title should stay `#a4b3c9`.
4. **Player check** — start playback, move the mouse. The control bar should be
   a black gradient with a red seek fill; subtitles should render in Roboto Mono
   where that font is installed.
5. **Keyboard check** — `Tab` through the header and a card row. Every stop
   should show a red ring with a soft halo, and the row arrows should appear.
6. **Contrast check** — body text (`#fff` on `#1b1919`) and secondary text
   (`#a4b3c9` on `#1b1919`) both clear 4.5:1. Muted text (`#576b87` on `#1b1919`)
   is roughly 4.0:1 and is used for metadata only, never for body copy.
7. **Reduced motion** — enable it in the OS, reload. Card lift, header
   transition and scroll behaviour should all stop.

See [`screenshots.md`](screenshots.md) for the full side-by-side shot list and
the test matrix.

---

## Credits

Design tokens were measured from [rivestream.ru](https://rivestream.ru/) for
study. No assets, code or stylesheets were copied — only measurements, which are
facts about a design and are not themselves copyrightable. Nox's own name,
structure and CSS are original.

## License

MIT. Jellyfin is licensed separately; Nox ships no Jellyfin code.
