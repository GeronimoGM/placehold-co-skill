---
name: placehold-co
description: >
  Use this skill whenever you need to generate placeholder images using https://placehold.co/ — a free, fast, and zero-dependency image placeholder service. Trigger this skill when a user asks for placeholder images, dummy images, mock images, test images, or filler images in any project (Next.js, React, HTML, Vue, Angular, etc.). Also trigger when the user mentions needing fake images during development, building wireframes or mockups, or prototyping UIs. If the user is working with Next.js and needs placeholders, always include the remotePatterns configuration. Use this skill even when the user says something casual like "throw in some placeholder images" or "I need some dummy images for now."
---

# placehold.co

Generate placeholder images via `https://placehold.co/` with zero dependencies — just a URL. Covers all parameters, framework integration, and Next.js config.

## URL Structure

```
https://placehold.co/{width}x{height}/{bgColor}/{textColor}/{format}?text=...&font=...
```

All segments after `{width}x{height}` are optional and can be combined.

---

## Parameters

### Size (required)
```
https://placehold.co/600x400      # width × height
https://placehold.co/400          # square (400×400)
```
- Min: **10×10 px** | Max: **4000×4000 px**

### Format (optional — default: SVG)
```
https://placehold.co/600x400/png
https://placehold.co/600x400.png        # dot notation also works
```
Supported: `svg`, `png`, `jpeg`, `gif`, `webp`, `avif`

> **Next.js tip:** Use `png`, `webp`, or `avif` when using `<Image>` from `next/image` — SVG works too but requires `dangerouslyAllowSVG` on some configs.

### Colors (optional)
Both background and text colors must be provided together.
```
# Hex (3 or 6 chars)
https://placehold.co/600x400/000000/FFFFFF

# CSS color names
https://placehold.co/600x400/orange/white

# Transparent background
https://placehold.co/600x400/transparent/F00

# Colors + format
https://placehold.co/600x400/000000/FFFFFF/png
https://placehold.co/600x400/000000/FFFFFF.png
```

### Text (optional — default: image dimensions)
Use `?text=` query param. Spaces → `+`, newlines → `\n`.
```
https://placehold.co/600x400?text=Hello+World
https://placehold.co/600x400?text=Hello\nWorld
```

### Font (optional — default: Lato)
```
https://placehold.co/600x400?font=roboto
https://placehold.co/800?text=Hero+Image&font=montserrat
```
Available fonts:
| Font | Slug |
|---|---|
| Lato (default) | `lato` |
| Lora | `lora` |
| Montserrat | `montserrat` |
| Noto Sans | `noto-sans` |
| Open Sans | `open-sans` |
| Oswald | `oswald` |
| Playfair Display | `playfair-display` |
| Poppins | `poppins` |
| PT Sans | `pt-sans` |
| Raleway | `raleway` |
| Roboto | `roboto` |
| Source Sans Pro | `source-sans-pro` |

### Retina / HiDPI (optional)
Append `@2x` or `@3x` to the size. Supported for PNG, JPEG, GIF, WebP, AVIF only (not SVG).
```
https://placehold.co/600x400@2x.png
https://placehold.co/800@3x.webp
```

---

## Common URL Recipes

```
# Basic gray placeholder
https://placehold.co/600x400

# Transparent background, red text
https://placehold.co/600x400/transparent/F00

# Dark card thumbnail, WebP
https://placehold.co/300x200/1a1a2e/eee/webp

# Avatar square with initials
https://placehold.co/80/6366f1/ffffff/png?text=GG

# Hero banner, retina, custom font
https://placehold.co/1200x400@2x.webp?text=Hero+Banner&font=montserrat

# Product image placeholder
https://placehold.co/400x400/f3f4f6/9ca3af/png?text=Product
```

---

## Next.js Integration

### `next.config.ts` — remotePatterns

Add placehold.co to `remotePatterns` so `next/image` can optimize it.

```ts
// next.config.ts
import type { NextConfig } from "next";

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

export default nextConfig;
```

> If you already have other `remotePatterns` entries, add the placehold.co object to the existing array — don't replace it.

### Usage with `next/image`

```tsx
import Image from "next/image";

// Basic usage
<Image
  src="https://placehold.co/600x400/png"
  alt="Placeholder"
  width={600}
  height={400}
/>

// With custom text and colors
<Image
  src="https://placehold.co/300x200/6366f1/ffffff/png?text=Card+Thumbnail"
  alt="Card thumbnail placeholder"
  width={300}
  height={200}
/>

// Fill mode (parent must be position: relative with defined size)
<div style={{ position: "relative", width: "100%", height: "400px" }}>
  <Image
    src="https://placehold.co/1200x400/png"
    alt="Hero placeholder"
    fill
    style={{ objectFit: "cover" }}
  />
</div>

// Retina-ready avatar
<Image
  src="https://placehold.co/80@2x.png?text=GG"
  alt="Avatar"
  width={80}
  height={80}
/>
```

### Using a helper function (recommended for large projects)

```ts
// lib/placeholder.ts

type PlaceholderOptions = {
  width: number;
  height?: number;
  bg?: string;
  fg?: string;
  text?: string;
  font?: string;
  format?: "png" | "webp" | "avif" | "jpeg" | "gif" | "svg";
  retina?: "@2x" | "@3x";
};

export function placeholder({
  width,
  height,
  bg,
  fg,
  text,
  font,
  format = "png",
  retina,
}: PlaceholderOptions): string {
  const size = height ? `${width}x${height}` : `${width}`;
  const retinaStr = retina ?? "";
  const colorPath = bg && fg ? `/${bg}/${fg}` : "";
  const params = new URLSearchParams();
  if (text) params.set("text", text);
  if (font) params.set("font", font);
  const query = params.size > 0 ? `?${params.toString()}` : "";
  return `https://placehold.co/${size}${retinaStr}${colorPath}/${format}${query}`;
}
```

Usage:
```ts
placeholder({ width: 600, height: 400 })
// → https://placehold.co/600x400/png

placeholder({ width: 300, height: 200, bg: "6366f1", fg: "fff", text: "Card" })
// → https://placehold.co/300x200/6366f1/fff/png?text=Card

placeholder({ width: 80, retina: "@2x", text: "GG" })
// → https://placehold.co/80@2x/png?text=GG
```

---

## Other Frameworks

### React (plain)
```tsx
<img
  src="https://placehold.co/600x400/png"
  alt="placeholder"
  width={600}
  height={400}
/>
```

### HTML
```html
<img src="https://placehold.co/600x400" alt="placeholder" />
```

### CSS background
```css
.hero {
  background-image: url("https://placehold.co/1200x400/png");
  background-size: cover;
}
```

### Angular
```html
<img
  src="https://placehold.co/300x200/6366f1/ffffff/webp?text=Card"
  alt="placeholder"
  width="300"
  height="200"
/>
```

---

## Gotchas

- **SVG + Next.js `<Image>`**: SVG works but Next.js won't apply size optimization to it. Prefer `png`, `webp`, or `avif` when performance matters.
- **Colors are always paired**: you can't set background without text color and vice versa.
- **Retina not supported for SVG**: only raster formats (`png`, `jpeg`, `gif`, `webp`, `avif`) support `@2x` / `@3x`.
- **Text encoding**: spaces must be `+` (not `%20`) and newlines must be `\n` in the query string.
- **Max size**: 4000×4000 px — requesting anything larger will return an error.
