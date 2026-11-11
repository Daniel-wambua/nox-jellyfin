# Nox Jellyfin theme redesign — implementation record

**Status:** Implemented locally in the connected `Daniel-wambua/nox-jellyfin` repository.

**Target:** Jellyfin Web Custom CSS.

**Design direction:** An original cinematic streaming interface with a dominant editorial hero, layered plum surfaces, lime action accent, spacious poster rails, rounded cards, and an expandable navigation rail. The design takes visual cues from the supplied RiveStream reference without copying its source, assets, markup, or stylesheet code.

## Every detail matters

This document records the inputs, decisions, outputs, validation, and remaining limitations. No implementation detail should be treated as incidental: Jellyfin renders more than one application shell, its Custom CSS is client-side, and some visual surfaces depend on DOM hooks that can change between Jellyfin releases.

## Inputs and reference findings

The requested repository was identified as `Daniel-wambua/nox-jellyfin`. The initial branch was `main` at commit `30f1be9`, tagged `0.1.4`.

The supplied Abyss repository describes a token-driven Jellyfin theme with broad component coverage, responsive behavior, motion controls, and explicit support for multiple Jellyfin shells. Those principles were used as engineering reference only. No Abyss stylesheet was copied. [1]

The supplied RiveStream homepage presents a left navigation rail, one large artwork-led carousel/hero with title and `Watch`/`Details` actions, followed by titled horizontal media rails. That hierarchy drove the new hero and shelf treatment. [2]

Jellyfin documents that Custom CSS is loaded after the default web styles, applies to Jellyfin Web clients, and may reference external resources from the client. Nox therefore remains self-contained and does not add external fonts, images, scripts, or plugins. [3]

## What changed

### 1. Default design tokens

The default palette in `nox.css`, `tokens.css`, `palette.json`, and `manifest.json` now uses:

- Background: `#0c0912`, a deep plum-black page field.
- Surface: `#120e1c`, for navigation, plaques, dialogs, and primary chrome.
- Raised surface: `#1b1528`, for cards and secondary panels.
- Primary text: `#fbfaff`.
- Secondary text: `#c2bcd2`.
- Muted text: `#887f9e`.
- Accent: `#c9ff68`, with matching RGB triplet `201, 255, 104`.
- Accent foreground: `#0c1205`.

Card, button, hero, plaque, and dialog radii were increased to create a softer streaming-product silhouette. Card and rail shadows were retuned to be atmospheric rather than heavy.

The fourteen alternate palettes remain available. The redesign only changes the shipped default palette and the associated machine-readable metadata; alternate palette class definitions were preserved.

### 2. Cinematic hero system

A new section 17 was added to `nox.css` and mirrored into `nox.min.css`. It enhances Jellyfin’s existing `.detailPagePrimaryContainer` and `[data-type="backdrop"]` hooks without changing client behavior.

The hero now has:

- A responsive minimum height based on viewport size.
- A large rounded frame with a subtle border and soft shadow.
- Full-frame artwork with object-fit cover and restrained saturation/contrast.
- A left-to-right and bottom-to-top scrim so white or bright artwork remains readable.
- A restrained accent-colour bloom that adds depth without becoming a second brand color.
- Editorial copy anchored into the lower-left area.
- A larger, balanced title scale.
- A translucent action plaque with a hairline border, blur, and a visible page-colour notch.
- Larger, clearer play and secondary action buttons.

This is an original approximation of the requested “big card” hierarchy: artwork first, title and metadata second, actions last. It does not depend on a new plugin or JavaScript.

### 3. Media rails and cards

Home and detail rows now use a more editorial rhythm:

- Section spacing scales with viewport width.
- Section titles use the primary text tier and a small accent marker.
- Scrollers keep smooth horizontal movement and existing keyboard behavior.
- Cards lift slightly on hover/focus instead of relying on permanent shadows.
- Card hover adds a restrained accent outline and depth shadow.
- Indicators become compact pills with a light border.
- Existing progress bars and overlay actions continue to use the tokenized accent.

### 4. Navigation and app shell

The existing legacy `.skinHeader` and modern MUI header treatment were retained. The redesign only adds the new surface, blur, border, and hover language to both shells. This preserves the existing strategy for clients where the old header is hidden and the MUI app bar is visible.

### 5. Responsive behavior

Desktop retains the floating rail and reserves only the collapsed rail width. The rail opening does not shift the page. Tablet and phone layouts keep Jellyfin’s horizontal top bar, use a shorter hero, reduce padding, clamp overview copy, and allow action buttons to share the available width.

### 6. Accessibility and resilience

The pre-existing focus rings, forced-colors support, reduced-motion support, fallbacks for blur and `color-mix()`, and Jellyfin compatibility bridge were preserved. The new card lift is explicitly removed under `prefers-reduced-motion: reduce`.

The theme continues to use no external assets by default. This avoids a runtime dependency for the login page and aligns with Jellyfin’s warning that externally referenced resources must be reachable by every client. [3]

## Full-package architecture pass

The first redesign did not go far enough: it improved the palette, hero, and cards but left the page architecture centered on the original rail. This pass fixes that. Section 18 now treats Nox as a complete Web client skin.

The shell is shared across the legacy and MUI headers. Desktop uses a centered floating glass header with primary navigation, contextual controls, and account actions in one consistent bar. Smaller screens keep an inset responsive header. The drawer, menus, and popovers share the same raised glass surface and selected-state language.

The page layer now supports a full atmospheric backdrop, a max-width content frame, pill-shaped tabs, floating footer surfaces, detail ribbons, home-library action cards, dashboard/settings cards and form controls, and player OSD panels. The content layer remains separate: the cinematic hero, rails, cards, detail page, dialogs, login, and accessibility sections continue to own their respective surfaces.

This architecture is inspired by the coverage and separation of concerns in Abyss, but the selectors, tokens, visual language, and implementation are original to Nox.

## Fine-tuning pass

The full-package architecture was visually too spacious in its first form. This pass reduces `--nox-row-gap` and `--nox-card-gap`, tightens shelf title spacing, and reduces content-frame padding so the interface shows more useful media above the fold without becoming cramped.

The spotlight/detail hero now uses a shorter `clamp(20rem, 48vh, 34rem)` frame, a smaller title scale, shorter synopsis line-height, and a tighter action plaque. When a compatible home spotlight exposes `.featurediframe`, it receives the same wide rounded-card treatment rather than a full-screen takeover.

The sidebar now uses a compact inset width, tighter grouped spacing, icon bubbles, compact labels, and a lime selected surface. The navigation remains keyboard and Jellyfin controlled; only the visual hierarchy changed.

The player OSD is now a floating dock with smaller controls, a reduced margin footprint, a shorter progress bar, compact title/technical text, and smaller supporting panels. The original Jellyfin control order and behavior remain intact.

## Files changed

- `nox.css` — source stylesheet; default tokens plus sections 17 and 18 for the cinematic visual system and full-package shell architecture.
- `nox.min.css` — minified build regenerated from `nox.css`.
- `tokens.css` — standalone token layer synchronized with the new default.
- `palette.json` — default palette values synchronized with the source.
- `manifest.json` — version bumped to `0.2.0`, metadata and changelog updated.
- `README.md` — installation version, default palette, overview, and file-size description updated.
- `solution.md` — this implementation record.

## Validation performed

1. `manifest.json` was parsed with `python3 -m json.tool` successfully.
2. `palette.json` was parsed with `python3 -m json.tool` successfully.
3. The minified stylesheet was regenerated directly from the final source stylesheet after edits.
4. The source and minified stylesheet sizes were checked:
   - `nox.css`: 59,558 bytes.
   - `nox.min.css`: 35,699 bytes.
5. The Git working tree was inspected after the changes. Only the intended theme, metadata, documentation, and minified output files are modified.
6. The repository’s existing visual verification checklist remains the manual rendering gate. It calls for checks at desktop, tablet, and phone widths, hero readability over bright artwork, card hover/focus states, dialogs, player OSD, reduced motion, forced colors, and alternate palettes.

## Known limitations

The repository does not include a live Jellyfin fixture, screenshot test harness, or a running Jellyfin instance. CSS can be syntax- and metadata-validated here, but the final visual pass still needs to be performed in the user’s Jellyfin Web client with real backdrop and poster content.

The home-screen hero remains conditional because stock Jellyfin does not guarantee a home hero element. The new styling is guaranteed for existing detail-page backdrop hooks and any plugin or client surface that renders `[data-type="backdrop"]`.

The theme continues to target Jellyfin Web. Native TV, mobile, and other clients that do not load Jellyfin Web Custom CSS will not receive these styles, consistent with Jellyfin’s documentation. [3]

## Installation after merge

Use the release-pinned import after the PR is merged and a `0.3.1` release is published:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@0.3.1/nox.css');
```

The import must be the first rule in Jellyfin’s Custom CSS field. Save the setting and hard-refresh the browser.

For an immediate branch test before release packaging:

```css
@import url('https://cdn.jsdelivr.net/gh/Daniel-wambua/nox-jellyfin@main/nox.css');
```

## References

[1]: https://github.com/AumGupta/abyss-jellyfin "Abyss Jellyfin theme repository"
[2]: https://rivestream.ru/ "RiveStream homepage reference"
[3]: https://jellyfin.org/docs/general/clients/css-customization/ "Jellyfin CSS customization documentation"
