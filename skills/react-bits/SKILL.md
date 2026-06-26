---
name: react-bits
description: Source pre-built, animated, customizable React components from React Bits (reactbits.dev — 130+ animated text, backgrounds, UI components & creative tools) instead of authoring motion-heavy UI from scratch. Auto-invoke alongside design-taste and ui-ux-pro-max whenever a UI task in a React/Next.js project calls for animated text, animated backgrounds, scroll/hover/cursor effects, or a polished interactive component (carousel, dock, bento, nav, gallery, card). Skip for non-React stacks or when the design system already supplies the component.
---

# React Bits

A library-sourcing skill: when a React UI task wants motion or an interactive flourish, **check React Bits before hand-rolling it**. 130+ free, animated, fully customizable components you copy in (via CLI or paste) and tune via props or by editing the source.

- **Site / docs:** [reactbits.dev](https://reactbits.dev) · install guide: [reactbits.dev/get-started/installation](https://reactbits.dev/get-started/installation) · tools: [reactbits.dev/tools](https://reactbits.dev/tools)
- **Repo:** [github.com/DavidHDev/react-bits](https://github.com/DavidHDev/react-bits) (David Haz, ~42k★)
- **License:** MIT **+ Commons Clause** — free to use the components in your own sites/apps (commercial included). You may NOT resell or host React Bits _itself_ as a competing product. Fine for every client/app build; don't repackage the library.

## When to invoke

Auto-trigger (compose with `design-taste` + `ui-ux-pro-max`) when a **React or Next.js** task involves:

- **Animated text** — hero headlines, reveals, decrypt/scramble/glitch/shiny/gradient text, typewriter, count-up, scroll-driven text
- **Animated backgrounds** — aurora, particles, beams, waves, grids, shaders, plasma, galaxy, noise
- **Cursor / pointer effects** — splash, trail, magnet, crosshair, spotlight
- **Scroll & hover effects** — scroll reveal/float/velocity/stack, tilt, glare, border glow
- **Interactive components** — carousel, dock, bento, card nav, galleries, menus, steppers, counters
- Any "make this hero/landing/section feel alive / more impressive" request

## When NOT to invoke

- Non-React stacks (SwiftUI, Flutter, plain HTML/Vue/Svelte) — the components are React-only
- The project's own design system / `@*/ui` package already provides the component (defer to it — don't introduce a parallel source)
- Dense, high-frequency, or accessibility-critical UI where motion hurts (forms, tables, dashboards mid-task) — `design-taste` rules win; reach for React Bits on expressive surfaces, not utilitarian ones
- A simple transition you'd write in 3 lines of CSS — don't pull a dependency for it

## Install (per component, on demand)

Every component page on reactbits.dev gives copy-ready commands. Two CLI paths plus manual paste:

```bash
# shadcn registry (preferred when the project already uses shadcn)
npx shadcn@latest add @react-bits/<Name>-<VARIANT>
# e.g. npx shadcn@latest add @react-bits/BlurText-TS-TW

# jsrepo
npx jsrepo add <path-from-the-component-page>
```

**4 variants — pick to match the project** (check lockfile / existing files first):
`JS-CSS` · `JS-TW` (JS + Tailwind) · `TS-CSS` · `TS-TW` (TS + Tailwind). Per global rules: TypeScript + Tailwind (`TS-TW`) is the default for our stack unless the repo is JS or non-Tailwind. Use `bunx` instead of `npx` in Bun projects.

Manual paste also works — select stack on the site and copy the source. Components are **minimal-dependency and tree-shakeable**; heavier backgrounds pull `three` / `ogl` / `gsap` — confirm the dep is acceptable before adding.

## Catalog (130+ — names are the component import names)

**Text Animations:** ASCIIText, BlurText, CircularText, CountUp, CurvedLoop, DecryptedText, FallingText, FuzzyText, GlitchText, GradientText, RotatingText, ScrambledText, ScrollFloat, ScrollReveal, ScrollVelocity, ShinyText, Shuffle, SplitText, TextCursor, TextPressure, TextType, TrueFocus, VariableProximity

**Animations (effects):** AnimatedContent, Antigravity, BlobCursor, ClickSpark, Crosshair, Cubes, ElectricBorder, FadeContent, GhostCursor, GlareHover, GradualBlur, ImageTrail, LaserFlow, LogoLoop, MagicRings, Magnet, MagnetLines, MetaBalls, MetallicPaint, Noise, OrbitImages, PixelTrail, PixelTransition, Ribbons, ShapeBlur, SplashCursor, StarBorder, StickerPeel, Strands, TargetCursor

**Backgrounds:** Aurora, Balatro, Ballpit, Beams, ColorBends, DarkVeil, Dither, DotField, DotGrid, EvilEye, FaultyTerminal, Ferrofluid, FloatingLines, Galaxy, GradientBlinds, Grainient, GridDistortion, GridMotion, GridScan, Hyperspeed, Iridescence, LetterGlitch, LightPillar, LightRays, Lightfall, Lightning, LineWaves, LiquidChrome, LiquidEther, Orb, Particles, PixelBlast, PixelSnow, Plasma, PlasmaWave, Prism, PrismaticBurst, Radar, RippleGrid, ShapeGrid, SideRays, Silk, SoftAurora, Threads, Waves

**Components:** AnimatedList, BorderGlow, BounceCards, BubbleMenu, CardNav, CardSwap, Carousel, ChromaGrid, CircularGallery, Counter, DecayCard, Dock, DomeGallery, ElasticSlider, FlowingMenu, FluidGlass, FlyingPosters, Folder, GlassIcons, GlassSurface, GooeyNav, InfiniteMenu, Lanyard, MagicBento, Masonry, ModelViewer, PillNav, PixelCard, ProfileCard, ReflectiveCard, ScrollStack, SpotlightCard, Stack, StaggeredMenu, Stepper, TiltedCard

> The catalog grows weekly — if you don't see a fit, check reactbits.dev for newer additions before concluding it's absent.

## Creative tools (no-code, export assets)

- **Background Studio** — customize an animated background, export as video/image/code
- **Shape Magic** — inner rounded corners between shapes; export SVG / React / clip-path
- **Texture Lab** — 20+ effects (noise, dithering, ASCII) on images/videos, HQ export

All at [reactbits.dev/tools](https://reactbits.dev/tools).

## Using it well (composes with design-taste)

1. **Source, then tune** — pull the component, then apply `design-taste` rules: durations < 300ms for UI, `ease-out` not `ease-in`, only animate `transform`/`opacity`, gate hover behind `@media (hover:hover)`.
2. **Respect `prefers-reduced-motion`** — many background/cursor effects are heavy; provide a static fallback or disable on reduced-motion. React Bits components don't all do this for you.
3. **One showpiece per view** — an animated background _and_ cursor trail _and_ glitch text on one screen is slop. Pick the hero effect; keep the rest calm.
4. **Mind performance** — shader/`three`/`ogl` backgrounds are GPU-heavy and add bundle weight. Lazy-load below-the-fold, avoid on low-power/mobile, measure.
5. **Match the variant to the repo** (`TS-TW` default) and confirm any pulled dependency (`three`, `gsap`, `ogl`) is wanted before installing.
6. **Don't fork the design system** — if a project `/design-system` or `@*/ui` already owns this component type, extend that instead.

## Composition with other skills

| Skill                    | Role                                                             |
| ------------------------ | ---------------------------------------------------------------- |
| `react-bits` (this)      | **Source** pre-built animated React components                   |
| `uiverse`                | **Source** small UI primitives (button/loader/toggle, any stack) |
| `design-taste`           | **Tune** the motion — easing, duration, restraint, a11y          |
| `ui-ux-pro-max`          | General UI/UX vocabulary & layout                                |
| `color-strategy`         | Color decisions for the surrounding design                       |
| `gsap`                   | Author custom animation when no React Bits fit exists            |
| project `/design-system` | Brand tokens & canonical components (takes precedence)           |

## Maintenance

- Catalog grows weekly — refresh with:
  `for d in TextAnimations Animations Backgrounds Components; do gh api repos/DavidHDev/react-bits/contents/src/content/$d --jq '.[].name'; done`
- Verify install command syntax against the live component page if a CLI call fails (registry paths can change).
