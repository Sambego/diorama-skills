# Diorama — React Slideshow Library

Diorama is a React 18 component library for building browser-based slide-deck presentations. It is designed to work inside Next.js (App Router) projects.

## Scaffolding a new presentation

```bash
npx create-next-app@latest my-talk --app --typescript --tailwind --no-src-dir --no-import-alias
cd my-talk
npm install @sambego/diorama
```

Replace `app/page.tsx` with:

```tsx
"use client";

import { Deck, Slide, Title, Text } from "@sambego/diorama";

export default function Home() {
  return (
    <Deck navigation>
      <Slide>
        <Title>Welcome</Title>
        <Text>Use arrow keys or swipe to navigate.</Text>
      </Slide>
      <Slide>
        <Title>Slide two</Title>
      </Slide>
    </Deck>
  );
}
```

Replace `app/globals.css` with an empty file or minimal reset — Diorama ships its own base styles (Montserrat font, border-box reset).

Remove any default padding/margin on `body`/`html` that would conflict with the full-viewport deck.

Add this to `app/layout.tsx` inside `<head>` if the font doesn't load:

```html
<link href="https://fonts.googleapis.com/css?family=Montserrat:400,700" rel="stylesheet" />
```

Run `npm run dev` and open `http://localhost:3000`.

---

## Component reference

### Deck (required, top-level)

Wraps all slides. Manages keyboard navigation, touch swipe, URL-based slide tracking, and optional presenter notes.

```jsx
<Deck
  navigation       // show Previous/Next buttons (default: false)
  swipeToChange     // enable touch-swipe navigation (default: true)
  presenterNotes    // open a popup with speaker notes + timer (default: false)
  footer={<Footer>© 2025</Footer>}  // sticky footer element
  className="my-deck"
>
  <Slide>…</Slide>
  <Slide>…</Slide>
</Deck>
```

Keyboard shortcuts: `←` / `→` or `Page Up` / `Page Down`.

Every `<Slide>` child automatically receives an `index` (number) and `navigate(slideIndex)` function as props — use `navigate` to jump to any slide programmatically.

### Slide

Container for one slide. Takes up the full viewport. Content is centered with flexbox.

```jsx
<Slide
  notes="Remember to explain the architecture diagram"
  style={{ backgroundColor: "#1a1a2e" }}
>
  <Title>Architecture</Title>
</Slide>
```

- `notes` (string) — speaker notes shown in the presenter-notes popup.
- `style` (object) — inline styles; use this for per-slide background colours/images.
- `className` (string) — extra CSS class.

### Title

Renders an `<h1>`.

```jsx
<Title>Hello World</Title>
```

### Subtitle

Renders an `<h2>`.

```jsx
<Subtitle>A secondary heading</Subtitle>
```

### Text

Renders a `<p>`.

```jsx
<Text>Some body text for the slide.</Text>
```

### Highlight

Inline `<span>` with a coloured background. Use it inside Title, Subtitle, or Text.

```jsx
<Title>
  This is <Highlight color="#e63946">important</Highlight>
</Title>
```

`color` accepts any CSS colour value (hex, rgb, named, or CSS custom property).

### Quote

Renders a `<blockquote>` with optional `<cite>`.

```jsx
<Quote quotee="Grace Hopper">
  The most dangerous phrase in the language is "we've always done it this way."
</Quote>
```

### List

Renders `<ul>` (default) or `<ol>` (when `ordered` is true). Pass `<li>` elements as children.

```jsx
<List>
  <li>First point</li>
  <li>Second point</li>
</List>

<List ordered>
  <li>Step one</li>
  <li>Step two</li>
</List>
```

### Columns

Splits children into equal-width columns.

```jsx
<Columns>
  <div>
    <Text>Left column</Text>
  </div>
  <div>
    <Text>Right column</Text>
  </div>
</Columns>
```

Each direct child gets `maxWidth: 100 / n %` where n is the number of children. Works with any number of columns.

### Image

Full or inline image with optional colour overlay.

```jsx
<Image src="/diagram.png" alt="System diagram" />

<Image src="/hero.jpg" alt="Hero" full />

<Image src="/photo.jpg" alt="Photo" full color="rgba(0,0,0,0.4)" />

<Image src="/icon.svg" alt="Icon" contain />
```

- `full` — fills the entire slide.
- `contain` — uses `object-fit: contain`.
- `color` — renders a coloured overlay on top of the image (useful for darkening background images so text remains readable).

### Video

Autoplaying muted video with optional overlay. Always renders muted.

```jsx
<Video src="/demo.mp4" autoplay loop full />

<Video src="/clip.mp4" full color="rgba(0,0,0,0.3)" />
```

- `autoplay` / `loop` / `full` — booleans.
- `color` — overlay colour.

### Code

Syntax-highlighted code block styled like a macOS terminal window (with traffic-light dots). Uses PrismJS.

```jsx
<Code
  code={`const greet = (name) => {
  console.log(\`Hello, \${name}!\`);
};`}
  lang="javascript"
/>
```

`lang` defaults to `"javascript"`. Supports any language PrismJS includes by default (javascript, css, markup/html, clike, etc.).

### Browser

Renders a macOS-style browser chrome around an `<iframe>`.

```jsx
<Browser url="https://example.com" />
```

### Footer

Renders a fixed element at the bottom of the deck (passed to `<Deck footer={…}>`).

```jsx
<Deck footer={<Footer>© 2025 My Name</Footer>}>
  …
</Deck>
```

---

## CSS custom properties

Diorama defines these CSS variables on `:root` that you can use in inline styles or your own CSS:

| Variable | Value |
|---|---|
| `--color-primary` | green |
| `--color-seconday` | teal |
| `--color-danger` | red |
| `--color-success` | green |
| `--color-info` | blue |
| `--color-warning` | yellow |

Gray scale: `--color-gray-0` (white) through `--color-gray-7` (near black), plus `--color-gray-dark`, `--color-gray-medium`, `--color-gray-light`.

Named colours: `--color-red`, `--color-orange`, `--color-yellow`, `--color-green`, `--color-teal`, `--color-blue`, `--color-pink`, `--color-brown`.

Every component renders a `diorama-*` class name (e.g. `diorama-slide`, `diorama-title`, `diorama-code`) so you can target them with global CSS.

---

## When to use built-in components vs. custom ones

**Use the built-in components when:**

- You need standard presentation elements — titles, body text, quotes, lists, images, videos, code snippets, or browser previews. These are ready to go with sensible defaults.
- You want consistent styling — all built-in components share the same Montserrat typography, spacing, and colour variables.
- You want the `diorama-*` class names for easy global CSS overrides.

**Create a custom component when:**

- You need interactive elements (forms, live demos, animations, charts) — wrap them in a div inside a `<Slide>` and add your own event handlers.
- You need a layout that `<Columns>` can't express (e.g. grid, asymmetric columns, overlapping elements) — use a plain `<div>` with CSS Grid or Flexbox inside a `<Slide>`.
- You need a specialised media embed (YouTube, CodePen, a canvas visualisation) — create your own component and place it inside a `<Slide>`.

Custom components work as direct children of `<Slide>`. The slide content area is a flex column centered both ways, so custom elements participate in that layout automatically. Override with your own styles if needed.

```jsx
// Example: custom interactive component inside a slide
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Clicked {count} times
    </button>
  );
}

// Usage
<Slide>
  <Title>Interactive Demo</Title>
  <Counter />
</Slide>
```

---

## Presenter notes

Enable with `<Deck presenterNotes>`. Each slide's `notes` prop appears in a popup window alongside the current and next slide preview and a running timer. Allow popups in your browser.

```jsx
<Deck presenterNotes navigation>
  <Slide notes="Introduce yourself and the topic.">
    <Title>Welcome</Title>
  </Slide>
  <Slide notes="Explain the key architecture decisions.">
    <Title>Architecture</Title>
  </Slide>
</Deck>
```

---

## Full example

```tsx
"use client";

import {
  Deck,
  Slide,
  Title,
  Subtitle,
  Text,
  Highlight,
  Code,
  Image,
  Quote,
  List,
  Columns,
  Footer,
} from "@sambego/diorama";

export default function Home() {
  return (
    <Deck navigation footer={<Footer>My Talk — 2025</Footer>}>
      <Slide notes="Welcome the audience.">
        <Title>
          Building with <Highlight color="#e63946">Diorama</Highlight>
        </Title>
        <Subtitle>A React slideshow library</Subtitle>
      </Slide>

      <Slide>
        <Title>Key Features</Title>
        <List>
          <li>Keyboard and swipe navigation</li>
          <li>Presenter notes with timer</li>
          <li>Syntax-highlighted code blocks</li>
          <li>Works with Next.js App Router</li>
        </List>
      </Slide>

      <Slide>
        <Title>Side by Side</Title>
        <Columns>
          <div>
            <Text>Left column content</Text>
          </div>
          <div>
            <Text>Right column content</Text>
          </div>
        </Columns>
      </Slide>

      <Slide>
        <Code
          code={`import { Deck, Slide, Title } from "@sambego/diorama";

export default function App() {
  return (
    <Deck>
      <Slide><Title>Hello</Title></Slide>
    </Deck>
  );
}`}
          lang="javascript"
        />
      </Slide>

      <Slide style={{ backgroundColor: "#1a1a2e", color: "#fff" }}>
        <Quote quotee="Grace Hopper">
          The most dangerous phrase is "we've always done it this way."
        </Quote>
      </Slide>

      <Slide>
        <Image src="/architecture.png" alt="Architecture diagram" />
      </Slide>

      <Slide>
        <Title>Thank you!</Title>
      </Slide>
    </Deck>
  );
}
```
