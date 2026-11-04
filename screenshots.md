# Nox — screenshot shot list

Capture both sides at the **same viewport** so pairs are directly comparable.
Recommended settings:

- Viewport: **1920 × 1080** for desktop, **375 × 812** for mobile.
- Device pixel ratio: **1** (so px measurements overlay cleanly).
- Browser zoom: **100 %**.
- Reference side: force dark mode before capturing
  (`chrome://flags` → force dark, or DevTools → Rendering → *Emulate
  prefers-color-scheme: dark*, then reload).
- Jellyfin side: fresh profile, no browser extensions, same
  `prefers-reduced-motion` state as the reference capture.

Name files `NN-area--side--viewport.png`, e.g. `04-card-hover--rive--1920.png`.

---

## A. Desktop pairs (1920 × 1080)

### 01 — Home hero
| | |
|---|---|
| **Capture** | Top of the home screen, first paint, unscrolled |
| **Reference** | `https://rivestream.ru/` |
| **Compare** | Hero band height (reference `90dvh`); corner radius on the hero frame (`2rem` / 32 px); whether the nav is transparent over the art; gradient direction and where it reaches full page background; title position and clamp |
| **Reference measurements** | hero height `90dvh`; carousel `80dvh`, `max-width calc(100vw - 5rem)` (1840 px at 1920), `margin 1rem 0 0`, radius `2rem`; meta panel width `20rem` / 320 px, radius `1.5rem` / 24 px, `margin-bottom:-2.5rem`, `outline:.7rem solid var(--bg-color); outline-offset:-1px` |

### 02 — Hero meta panel (close crop)
| | |
|---|---|
| **Capture** | 600 × 400 crop around the meta panel |
| **Compare** | The notch: the panel is outlined, not padded, so the page background reads as a cut-out. Radius `1.5rem`. Meta row gap `.7rem`, colour `--primary-2` (`#576b87`), size `1rem` |
| **Expected delta** | Jellyfin has no hero component. If no hero renders, this shot is *missing* on the Nox side — record it as a gap, not a failure |

### 03 — Carousel row
| | |
|---|---|
| **Capture** | One full row of posters, left edge visible |
| **Compare** | Row gap `1rem`; row padding `.7rem`; row margin `.3rem .5rem 1.3rem`; poster width `10rem` / 160 px; poster height `15rem` / 240 px; corner radius `.5rem` / 8 px; whether arrows are visible (reference: never; Nox: on hover only); whether a scrollbar is visible (reference: never) |

### 04 — Card hover
| | |
|---|---|
| **Capture** | Same poster, rest vs hover, two frames |
| **Compare** | Shadow `0 1px 2px var(--primary-3)`; title colour → `--primary-3`; whether the poster moves |
| **Expected delta** | Nox adds a 2 % scale + 2 px lift. The reference does not move the poster at all. Note this explicitly in the write-up |

### 05 — Resume progress bar
| | |
|---|---|
| **Capture** | Close crop of a partially-watched poster |
| **Compare** | Bar height `.3rem`; radius `.3rem`; track `--primary-2`; fill colour and position (absolute, left `0`) |

### 06 — Section heading
| | |
|---|---|
| **Capture** | 800 × 160 crop containing one row heading |
| **Compare** | Size `1.7rem` / 27.2 px; colour `--primary-3` / `#a4b3c9`; weight 600; family (reference resolves to Bricolage Grotesque, Nox falls back unless the optional `@import` is uncommented); letter spacing `-.01em` |

### 07 — Header at rest vs scrolled
| | |
|---|---|
| **Capture** | Two frames: page top, and after ~400 px of scroll |
| **Compare** | At rest: transparent, no border. Scrolled: blurred, hairline border, background blended toward the page colour |
| **Expected delta** | The reference's navigation is a fixed *left rail*; Jellyfin's is a top bar. The screenshot will differ structurally. Compare *treatment* (the `--primary-1` panel, radius `.5rem`, 1 rem gap, 1.3 rem icons), not position |

### 08 — Detail page top
| | |
|---|---|
| **Capture** | Top of a movie detail page |
| **Compare** | Backdrop coverage; scrim direction and where it reaches page background; title hierarchy; Play/Details pill pair and which one carries the accent |
| **Expected delta** | No reference equivalent — the reference is a single-page catalogue. Compare against the reference's *hero* anatomy instead |

### 09 — Detail action row (close crop)
| | |
|---|---|
| **Capture** | 900 × 200 crop of the button row |
| **Compare** | Pill radius; primary vs secondary fill; icon size; hover state on each |

### 10 — Player chrome
| | |
|---|---|
| **Capture** | Paused playback with the OSD visible, plus one frame with the seek bar mid-drag |
| **Compare** | Control-bar gradient (black → transparent, bottom to top); icon colour and hover; seek fill in the accent; subtitle family (Roboto Mono in the reference) |

### 11 — Dialog
| | |
|---|---|
| **Capture** | Any modal (e.g. *Edit metadata* or *Playback settings*) |
| **Compare** | Backdrop `rgba(0,0,0,.7)` + `blur(10px) grayscale(.2)`; dialog surface `color-mix(--primary-1 90%, --bg-color)`; border `color-mix(--primary-2 30%, transparent)`; radius `1rem`; shadow `0 24px 48px rgba(0,0,0,.6)`; max width 440 px |

### 12 — Toasts and popup menu
| | |
|---|---|
| **Capture** | Trigger a toast and open a context menu in the same frame |
| **Compare** | Toast surface `--primary-1`, text `--primary-3`, border `--primary-2`; menu radius; hover wash |

### 13 — Login
| | |
|---|---|
| **Capture** | Signed out, fresh session |
| **Compare** | Card surface, radius `.75rem`, raised shadow; heading family and weight; input styling and focus ring |
| **Expected delta** | The reference lays login out as a two-column grid (`1.25fr .75fr`, padding `2rem 4rem`). Jellyfin's login is a single centred column. Compare treatment, not layout |

### 14 — Forms and settings
| | |
|---|---|
| **Capture** | Dashboard → General |
| **Compare** | Input surfaces, borders, focus rings; switch track/thumb; checkbox accent; submit button |

### 15 — Focus ring
| | |
|---|---|
| **Capture** | Two `Tab` frames: a header icon, and a card in a row |
| **Compare** | Ring colour (accent), offset, halo. Reference uses a 2 px translucent accent shadow; Nox uses a 1 px solid ring plus the same halo |

---

## B. Mobile pairs (375 × 812)

### 16 — Home, mobile
| | |
|---|---|
| **Capture** | Top of home, unscrolled |
| **Compare** | Section title drops to `1.4rem`; poster width `7.5rem`; row gap `.75rem`; arrow buttons absent (touch); gutter `1rem` |

### 17 — Card + detail, mobile
| | |
|---|---|
| **Capture** | A row and a detail page top |
| **Compare** | Tap targets ≥ 44 px; text legibility; no hover-only affordance survives |

### 18 — Player, mobile landscape
| | |
|---|---|
| **Capture** | Playback at 812 × 375 |
| **Compare** | Control density; that the OSD does not eat more than the bottom third |

---

## C. Test matrix

Run every row against a real Jellyfin 10.9+ instance. Mark each
**pass / pass-with-delta / fail** and note the delta.

| # | Surface | Desktop 1920 | Mobile 375 | Notes to record |
|---|---|---|---|---|
| 1 | Login | ☐ | ☐ | Card, inputs, focus |
| 2 | Home (movies) | ☐ | ☐ | Hero presence, row spacing, card size |
| 3 | Home (TV) | ☐ | ☐ | Series poster ratio, next-up row |
| 4 | Movie detail | ☐ | ☐ | Backdrop scrim, action row |
| 5 | TV detail + seasons | ☐ | ☐ | Season tabs, episode list radius |
| 6 | Episode detail | ☐ | ☐ | Overview legibility over backdrop |
| 7 | Search | ☐ | ☐ | Field radius, result list |
| 8 | Settings / Dashboard | ☐ | ☐ | Inputs, switches, save button |
| 9 | Playback (video) | ☐ | ☐ | OSD gradient, seek fill, chapters |
| 10 | Playback (audio) | ☐ | ☐ | Now-playing bar, queue |
| 11 | Dialogs | ☐ | ☐ | Overlay blur, surface, radius |
| 12 | Toasts | ☐ | ☐ | Colour pair, radius |
| 13 | Popup menus / drawer | ☐ | ☐ | Hover wash, selected state |
| 14 | Keyboard only | ☐ | — | Every stop visible, ring contrast |
| 15 | Reduced motion | ☐ | ☐ | Lift, header transition, smooth scroll all stop |
| 16 | `backdrop-filter` unsupported | ☐ | — | Force via DevTools; header and dialogs stay legible |
| 17 | `color-mix()` unsupported | ☐ | — | Force via DevTools; theme still reads |

### Cross-browser

| Engine | Version | Result |
|---|---|---|
| Chrome / Edge (Blink) | | ☐ |
| Firefox (Gecko) | | ☐ |
| Safari / iOS (WebKit) | | ☐ |
| Jellyfin Media Player (CEF) | | ☐ |
| Android app (WebView) | | ☐ |
| Jellyfin for Android TV | | ☐ |

---

## D. Known deltas to expect in every capture

These are the differences that are *by design or by CSS's limits* — list them
once in the write-up rather than re-litigating them per shot.

1. **Navigation position.** Reference: fixed left rail. Nox: top bar with the
   rail's visual treatment. CSS cannot move JS-positioned header children.
2. **Hero presence.** Reference: always. Nox: only when a hero component exists
   or the server has backdrops.
3. **Card hover motion.** Nox adds a 2 % scale and 2 px lift; the reference does
   not move the poster.
4. **Scroll snap.** Nox adds x-proximity snap; the reference has none.
5. **Row arrows.** Nox reveals Jellyfin's arrows on hover; the reference has no
   arrows anywhere.
6. **Page scrollbar.** Nox keeps a thin themed page scrollbar; the reference
   hides scrollbars globally.
7. **Hero gradient.** Nox paints a scrim from the sampled page colour; the
   reference relies on its artwork and paints no gradient.
8. **Login layout.** Reference: two-column grid. Nox: single centred column
   (Jellyfin's structure).
9. **Accent.** Reference default in CSS is `gold`, its live session resolved to
   `#2196f3`, and it is a user preference, not a brand colour. Nox defaults to
   the picker's Red `#f44336`.
10. **Typography.** The reference loads Bricolage Grotesque from Google Fonts.
    Nox makes no external request by default, so the display face differs unless
    the optional `@import` in `tokens.css` is uncommented.
