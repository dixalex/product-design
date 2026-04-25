# Operate-extracted tokens

Provenance: extracted from Operate.so's production Tailwind v4 build (downloaded 2026-04-17).
Snapshot path: `app.operate.so/_next/static/chunks/8e09615d517053b6.css`. Replace tokens freely;
your team's brand palette can live here.

Status: opt-in. The skill is behavior-first — these literals are only loaded when a design
decision needs specific color / shadow / motion values. See `motion.md § Easing` for how
`--ease-elegant` is used, and `foundations.md § Aesthetic-Usability Effect` for why a coherent
token set matters to perceived quality.

## CSS custom properties

```css
:root {
  /* Typography scale — 13.5px base, Linear-dense */
  --font-sans: var(--font-muoto);
  --font-mono: var(--font-geist-mono), monospace;
  --font-muoto: var(--font-muoto);
  --font-geist-mono: var(--font-geist-mono);
  --font-weight-normal: 400;
  --font-weight-book: 450;
  --font-weight-medium: 500;
  --unitless-text-base: 13.5;
  --text-base: 13.5px;
  --text-sm: calc(var(--text-base) * 1);
  --text-xs: calc(var(--text-base) * .889);
  --text-md: calc(var(--text-base) * 1.25);
  --text-lg: calc(var(--text-base) * 1.4);
  --tracking-tight: -.025em;
  --tracking-normal: 0em;
  --tracking-widest: .1em;
  --leading-tight: 1.25;
  --leading-snug: 1.375;

  /* Radius */
  --radius-xs: .125rem;
  --radius-sm: .25rem;
  --radius-md: .375rem;
  --radius-lg: .5rem;

  /* Spacing base */
  --spacing: .25rem;
  --container-xs: 20rem;
  --container-sm: 24rem;
  --container-md: 28rem;
  --container-lg: 32rem;
  --container-4xl: 56rem;

  /* Color scales — srgb fallback; lab() values override under @supports */
  --color-transparent: #0000;
  --color-white: #fff;
  --color-black: #000;
  --color-gray-50: #fff;
  --color-gray-75: #fcfcfc;
  --color-gray-100: #f7f7f7;
  --color-gray-200: #edefef;
  --color-gray-300: #e0e5e5;
  --color-gray-400: #cad3d2;
  --color-gray-500: #9ba8a7;
  --color-gray-600: #71807e;
  --color-gray-700: #5f6d6b;
  --color-gray-800: #294d47;
  --color-gray-900: #09352e;
  --color-green-500: #007f24;
  --color-green-600: #006f19;
  --color-green-700: #00681a;
  --color-green-800: #005a06;
  --color-green: var(--color-green-500);
  --color-ember-500: #ea5a13;
  --color-ember-600: #d64d00;
  --color-ember-700: #cc4900;
  --color-ember-800: #b63e00;
  --color-ember: var(--color-ember-500);

  /* Semantic named colors */
  --color-graphpaper: #87c295;
  --color-blueberry: #6358b7;
  --color-acid: #e3ed53;
  --color-cornflower: #4184e7;
  --color-ocean: #009fd4;
  --color-copper: #b85725;

  /* Accent palette */
  --color-accent-gray: #dbdbdb;
  --color-accent-dark-gray: #a3a5a3;
  --color-accent-purple: #6358b7;
  --color-accent-teal: #009fd4;
  --color-accent-green: #007f24;
  --color-accent-yellow: #f6eb15;
  --color-accent-orange: #fc7a09;
  --color-accent-brown: #b85725;
  --color-accent-pink: #e4b4c8;
  --color-accent-red: #f34600;

  /* Surface / role tokens */
  --color-background: var(--color-gray-100);
  --color-canvas: var(--color-gray-75);
  --color-elevated: var(--color-gray-50);
  --color-foreground: var(--color-gray-900);
  --color-border: #0000001a;
  --color-border-opaque: var(--color-gray-300);
  --color-hover: #00000008;
  --color-hover-success: #007f2408;
  --color-hover-danger: #ea5a1308;
  --color-link: var(--color-green);
  --color-brand: var(--color-green-600);
  --color-info: var(--color-blueberry);
  --color-success: var(--color-green);
  --color-danger: var(--color-ember);
  --color-warning: var(--color-acid);
  --color-scrollbar: var(--color-gray-400);

  /* Shadow system — layered "dynamic-shadow" stack */
  --dynamic-shadow-border-color: #0a362e05;
  --dynamic-shadow-border:
    0px .5px 0px -.5px var(--dynamic-shadow-border-color),
    0px .5px .5px -.5px var(--dynamic-shadow-border-color),
    0px .5px 1px -.5px var(--dynamic-shadow-border-color),
    0px 1px 2px -1px var(--dynamic-shadow-border-color),
    0px 1.5px 3px -1.5px var(--dynamic-shadow-border-color);
  --dynamic-shadow-100:
    var(--dynamic-shadow-border),
    0px 11px 10px -7px #08121105,
    0px 0px 5.5px -.5px #0812110a;
  --dynamic-shadow-200:
    var(--dynamic-shadow-border),
    0px 5px 6px -4px #0a362e0d,
    0px 1px 1px .5px #afcbc614;
  --dynamic-shadow-300:
    var(--dynamic-shadow-border),
    0px 1.5px 2.5px -.5px #08121112,
    0px 4px 4px -1px #08121108;
  --dynamic-shadow-400:
    var(--dynamic-shadow-border),
    0px 1px 3px 0px #0812110d,
    0px 6px 6px 0px #0812110a;
  --dynamic-shadow-500:
    var(--dynamic-shadow-border),
    0px 4px 4px 0px #0a362e0a,
    0px 10px 6px 0px #0a362e08,
    0px 18px 7px 0px #0a362e03;
  --dynamic-shadow-canvas:
    0 0 0 1px var(--color-white) inset,
    0 0 4px 0 #0000000a,
    0 2px 2px -1px #0000000a,
    0 1px 1px -.5px #0000000a,
    0 0 0 .5px #0000001a;
  --dynamic-shadow-elevated:
    0 4px 4px -2px #00000005,
    0 2px 2px -1px #0000000a,
    0 1px 1px -.5px #0000000f,
    0 0 0 .5px #0000001a;
  --dynamic-shadow-button: var(--dynamic-shadow-200);
  --dynamic-shadow-tooltip: var(--dynamic-shadow-400);
  --dynamic-shadow-peek: var(--dynamic-shadow-400);
  --dynamic-shadow-pop-off: var(--dynamic-shadow-500);
  --drop-shadow-lg: 0 4px 4px #00000026;

  /* Motion — the house easing is a 100-stop linear() curve */
  --default-transition-duration: .15s;
  --default-transition-timing-function: cubic-bezier(.4, 0, .2, 1);
  --ease-in-out: cubic-bezier(.4, 0, .2, 1);
  --ease-out-quad: cubic-bezier(.25, .46, .45, .94);
  --duration-elegant: .45s;
  /* Abridged: curve peaks ~1.03 at 42%, settles to 1.0 at 100%.
     Full 100-stop linear() is in the source CSS (see Provenance);
     re-extract if you need the literal value for production. */
  --ease-elegant: linear(
    0 0%, .005911 1%, .022348 2%, .047507 3%, .079758 4%, .117643 5%,
    .159861 6%, .205261 7%, .252832 8%, .301694 9%, .351086 10%,
    .40036 11%, .448966 12%, .496448 13%, .542433 14%, .586621 15%,
    .62878 16%, .668737 17%, .706369 18%, .741602 19%, .774398 20%,
    .804754 21%, .832695 22%, .858271 23%, .881551 24%, .90262 25%,
    .921574 26%, .93852 27%, .953572 28%, .966847 29%, .978466 30%,
    .988548 31%, .997215 32%, 1.00458 33%, 1.01077 34%, 1.01588 35%,
    1.02002 36%, 1.0233 37%, 1.02581 38%, 1.02763 39%, 1.02886 40%,
    1.02956 41%, 1.02982 42%, 1.0297 43%, 1.02925 44%, 1.02853 45%,
    /* …curve peaks ~1.03 near 42%, settles to ~1.0 by 100% (overshoot) */
    1.00012 74%, .999894 75%, .999704 76%, /* … */ .999659 100%
  );
  --animate-spin: spin 1s linear infinite;
  --animate-pulse: pulse 2s cubic-bezier(.4, 0, .6, 1) infinite;
  --animate-dropdown-content: dropdown-content .125s ease-out;
  --animate-splash: splash 1s ease-in-out infinite;
  --blur-xs: 4px;

  /* Z-index — all stacking peers land at z:50, peek steals 60, dialog-close pins 100 */
  --z-context-menu: 50;
  --z-dialog: 10;
  --z-dialog-close: 100;
  --z-drawer: 50;
  --z-dropdown-menu: 50;
  --z-peek: 60;
  --z-popover: 50;
  --z-tooltip: 50;

  /* Border */
  --border-width: var(--border-width-hairline, .0625rem);
  --default-border-width: var(--border-width);
  --default-border-color: var(--color-border);
}

/* Under lab() / color-mix() support, scales switch to perceptual values. */
@supports (color: lab(0% 0 0)) {
  :root {
    --color-gray-500: lab(67.8039% -4.8897 -1.13781);
    --color-green-500: lab(46.2274% -46.1765 38.8844);
    --color-ember-500: lab(57.8751% 54.7666 64.0395);
    --color-blueberry: lab(42.3379% 23.6475 -49.8857);
    /* …all scales re-declared in lab() — see source for full table */
  }
}
/* The lab() feature query acts as a proxy for color-mix(in oklch) support —
   when a browser understands one, it typically understands the other. */
@supports (color: color-mix(in lab, red, red)) {
  :root {
    --color-border: color-mix(in oklch, var(--color-black) 10%, transparent);
    --color-hover: color-mix(in oklch, var(--color-black) 3%, transparent);
  }
}
```

## Tailwind v4 config

```typescript
// tailwind.config.ts — reference shape, not an exhaustive copy
export default {
  theme: {
    colors: {
      transparent: 'var(--color-transparent)',
      white: 'var(--color-white)',
      black: 'var(--color-black)',
      gray: {
        50: 'var(--color-gray-50)',
        75: 'var(--color-gray-75)',
        100: 'var(--color-gray-100)',
        // …200–900
      },
      green: { 500: 'var(--color-green-500)', /* …600–800 */ },
      ember: { 500: 'var(--color-ember-500)', /* …600–800 */ },
      graphpaper: 'var(--color-graphpaper)',
      blueberry: 'var(--color-blueberry)',
      acid: 'var(--color-acid)',
      cornflower: 'var(--color-cornflower)',
      ocean: 'var(--color-ocean)',
      copper: 'var(--color-copper)',
      accent: { /* gray, dark-gray, purple, teal, green, yellow, orange, brown, pink, red */ },
    },
    fontFamily: {
      sans: ['var(--font-muoto)'],
      mono: ['var(--font-geist-mono)', 'monospace'],
      muoto: ['var(--font-muoto)'],
    },
    fontSize: {
      xs: 'var(--text-xs)',
      sm: 'var(--text-sm)',
      base: 'var(--text-base)',
      md: 'var(--text-md)',
      lg: 'var(--text-lg)',
    },
    fontWeight: {
      normal: 'var(--font-weight-normal)', // 400
      book:   'var(--font-weight-book)',   // 450
      medium: 'var(--font-weight-medium)', // 500
    },
    boxShadow: {
      100: 'var(--dynamic-shadow-100)',
      200: 'var(--dynamic-shadow-200)',
      300: 'var(--dynamic-shadow-300)',
      400: 'var(--dynamic-shadow-400)',
      500: 'var(--dynamic-shadow-500)',
      canvas:   'var(--dynamic-shadow-canvas)',
      elevated: 'var(--dynamic-shadow-elevated)',
      button:   'var(--dynamic-shadow-button)',
      tooltip:  'var(--dynamic-shadow-tooltip)',
      peek:     'var(--dynamic-shadow-peek)',
      'pop-off': 'var(--dynamic-shadow-pop-off)',
    },
    transitionTimingFunction: {
      elegant: 'var(--ease-elegant)',
      'out-quad': 'var(--ease-out-quad)',
    },
    transitionDuration: { elegant: 'var(--duration-elegant)' },
    zIndex: {
      'context-menu':  'var(--z-context-menu)',
      dialog:          'var(--z-dialog)',
      'dialog-close':  'var(--z-dialog-close)',
      drawer:          'var(--z-drawer)',
      'dropdown-menu': 'var(--z-dropdown-menu)',
      peek:            'var(--z-peek)',
      popover:         'var(--z-popover)',
      tooltip:         'var(--z-tooltip)',
    },
  },
}
```

## Design decisions captured

- **13.5px base** instead of 14/16 — Linear-dense readability. Every `--text-*` size derives from `--text-base` via multiplier (`.889`, `1`, `1.25`, `1.4`), and `--unitless-text-base: 13.5` lets utility classes compute icon sizing with `calc(n / var(--unitless-text-base) * 1em)`.
- **`--font-weight-book: 450`** — non-standard weight for body text (between regular 400 and medium 500). Requires a variable font; only Muoto Variable and similar VF faces expose this smoothly.
- **`--ease-elegant`** is a 100-stop `linear()` curve — custom easing, distinctive signature. The curve overshoots slightly (peaks at ~1.03 around 42%) then settles — a subtle bounce baked into every transition that uses it. Paired with `--duration-elegant: .45s`. See `motion.md § Easing`.
- **Muoto Variable** is commercial (205TF foundry) — swap if you don't have license; Geist, Inter, Söhne are alternatives. Keep the `--font-weight-book: 450` binding only if your substitute ships a variable axis.
- **OKLCH color space throughout with `color-mix()`** — modern but requires fallback for older browsers. The build ships hex fallbacks, then overrides under `@supports (color: lab(...))` with perceptual `lab()` values, then layers `color-mix(in oklch, …)` for hover/border alphas where supported. `color-mix(in oklch, …)` support landed in Chrome 111 / Safari 16.4 / Firefox 113. Older browsers render the hex fallback layer.
- **Green is the brand anchor** — `--color-brand` points at `--color-green-600`, `--color-link` and `--color-success` at `--color-green`, while `--color-danger` flips to the warm `--color-ember` family. The semantic name palette (graphpaper, blueberry, acid, cornflower, ocean, copper) is used for accents and tag/badge colors, not core UI.
- **Layered shadow system** (`--dynamic-shadow-*`) stacks a hairline "dynamic-shadow-border" with 2–3 progressively larger blurs. Role aliases (`button`, `tooltip`, `peek`, `pop-off`) map to numbered tiers — compose, don't invent new ones.
