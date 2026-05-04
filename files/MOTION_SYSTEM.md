# Northwind CRM — Motion System

This document captures the motion idiom used throughout the Northwind CRM prototype. Future iterations should stay consistent with what's defined here. The goal is one signature feel applied everywhere, not five different animation styles.

---

## Signature Idiom: Linear-Style Horizontal Slide Reveals

Inspired by Linear's product UI. Elements arrive from the left with a confident, deceleration-heavy curve. Decisive, professional, fast — appropriate for a productivity tool, not a marketing site.

**One direction:** slide-in from the left (`translate3d(-24px, 0, 0)` → `translate3d(0, 0, 0)`). Used for:
- Initial cinematic mount (sidebar, main content)
- Page-level reveals on view switch
- Table row entrance stagger
- Modal entrance

**One curve everywhere:** `cubic-bezier(0.65, 0, 0.35, 1)` — the Awwwards staple. Strong acceleration, dramatic deceleration. Documented in the awwwards-style skill as the most-used UI curve on award-winning sites in 2025-2026.

**Exception (snap feedback only):** `cubic-bezier(0.2, 0, 0, 1)` (Material Emphasized) for button press scale. Snappier than the staple curve, gives tactile feedback that the click registered.

---

## Easing Tokens

```css
--ease-out:  cubic-bezier(0.65, 0, 0.35, 1);  /* primary signature curve */
--ease-in:   cubic-bezier(0.65, 0, 0.35, 1);  /* same curve for symmetry */
--ease-press: cubic-bezier(0.2, 0, 0, 1);     /* button press only */
```

If you find yourself reaching for a different curve, you're probably about to add a second motion idiom. Don't.

---

## Duration Rhythm

| Motion type            | Duration | Notes                                          |
|------------------------|----------|------------------------------------------------|
| Button press (scale)   | 100ms    | Must feel instant                              |
| Hover state changes    | 200ms    | Color, border, background                      |
| Magnetic snap-back     | 400ms    | When cursor leaves a magnetic button           |
| Modal entrance         | 460ms    | Slides in from left                            |
| Table row reveal       | 500ms    | Staggered 40ms apart                           |
| Page-level reveal      | 600ms    | Page header, KPI cards, content area           |
| Cinematic intro        | 600-700ms | One-time mount of topbar/sidebar/main         |

If a new animation doesn't fit one of these, find the closest and use that duration. Avoid one-off durations.

---

## Stagger Pattern

Consistent 60ms between sibling reveals (skill range: 40-80ms):

```css
.reveal-stagger-1.in { transition-delay: 60ms; }
.reveal-stagger-2.in { transition-delay: 120ms; }
.reveal-stagger-3.in { transition-delay: 180ms; }
.reveal-stagger-4.in { transition-delay: 240ms; }
```

Table rows use a slightly tighter 40ms stagger (`280ms + idx * 40ms` delay) because there can be many of them and a slow cascade feels sluggish.

---

## Magnetic Buttons

Primary CTAs (Add Customer, New Order, Create Customer) pull toward the cursor with **STRENGTH = 0.3** (per skill component recipe). Snap-back uses the staple curve at 400ms.

Disabled on touch devices (`@media (hover: none)`) and on `prefers-reduced-motion`.

---

## What's Intentionally NOT Here

The skill suggests several patterns that don't fit a CRM dashboard. They were considered and rejected:

- **Custom cursor** — was added once, removed at user request after live testing. Distracts from data tasks.
- **Scroll-pinned sections** — there's nothing to scroll-pin on a dashboard page.
- **WebGL gradient mesh hero** — overkill for an internal tool, also tanks battery on the projector laptop.
- **Character-split text reveals** — too cinematic for utility UI. Reserved for marketing pages.
- **Bento grid 3D tilt** — would distract from data scanning.
- **Spring overshoot on modal** — replaced with the staple curve to keep one signature.

If you're tempted to add one of these later, ask: *Does it help the user complete a CRM task faster?* If no, don't add it.

---

## Editorial Typography Layer

Beyond motion, the visual identity rests on one editorial pairing:

- **Inter** for UI, body, data. 15px body / 1.6 line-height (per skill spec body of 18-20px scaled down for dashboard density).
- **Instrument Serif italic** for accent words inside titles (e.g. *"Your **customers**, in one place"*).

The accent treatment uses the `<em>` tag inside titles, restyled to use `var(--font-display)` italic. Page titles, modal titles, KPI labels, and the Customer 360 hero name all share this treatment.

---

## Accessibility (Non-Negotiable)

Per the skill's hard requirements:

- **`prefers-reduced-motion: reduce`** — all animations collapse to 0.01ms duration. Lenis smooth scroll bails out entirely.
- **Visible focus rings** — every interactive element (buttons, chips, nav items, inputs, table rows) has a `:focus-visible` style. The CRM is fully keyboard-navigable end to end.
- **`tabindex="0"` and `role="button"`** on table rows, chips, and nav items so they enter the tab order.
- **Enter/Space activation** wired up for all keyboard-focusable interactive elements.
- **Magnetic buttons disable on touch** (`@media (hover: none)`).

---

## Texture: Noise Overlay

Flat navy regions (topbar, Customer 360 hero) carry a 4% opacity SVG fractal-noise overlay using `mix-blend-mode: overlay`. Defeats banding on projectors and adds tactile depth without being decorative.

---

## Before Shipping a Change

Run through the skill's quality bar:

1. Would this fit on Awwwards as part of an award-winning product UI? (If you're adding a fifth motion style, no.)
2. Does the motion still have one signature idiom carried throughout?
3. Does it work, and look intentional, with reduced-motion enabled?
4. Lighthouse 90+ on mobile?
5. Keyboard-navigable end to end?

If any answer is no, fix it before merging.
