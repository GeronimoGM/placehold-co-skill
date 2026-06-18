# placehold-co skill

A skill that teaches code agents how to generate placeholder images using [placehold.co](https://placehold.co/) — a free, fast, zero-dependency image placeholder service.

## What this skill covers

- Full URL syntax: size, format, colors, text, font, retina/HiDPI
- All supported image formats: SVG, PNG, JPEG, GIF, WebP, AVIF
- All available fonts with their slugs
- Common URL recipes ready to copy-paste
- **Next.js integration** — `remotePatterns` config for `next.config.ts` and `next/image` usage examples
- Reusable TypeScript `placeholder()` helper function
- Usage examples for React, HTML, CSS, and Angular

## Installation

```bash
npx skills add https://github.com/GeronimoGM/placehold-co-skill
```

## When an agent uses this skill

The agent will automatically apply this skill when you ask things like:

- *"Add placeholder images to my Next.js project"*
- *"I need some dummy images for the card components"*
- *"Throw in placeholder images for now"*
- *"Generate a 600×400 placeholder with a dark background"*
- *"How do I use placehold.co with next/image?"*

## Example outputs

The agent will generate ready-to-use URLs and code:

```
# Simple placeholder
https://placehold.co/600x400

# Dark card with custom text
https://placehold.co/300x200/1a1a2e/eee/webp?text=Card+Thumbnail

# Avatar with initials, retina
https://placehold.co/80@2x.png?text=GG
```

And for Next.js, Claude will also output the `remotePatterns` config:

```ts
// next.config.ts
const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "placehold.co",
        port: "",
        pathname: "/**",
      },
    ],
  },
};
```
