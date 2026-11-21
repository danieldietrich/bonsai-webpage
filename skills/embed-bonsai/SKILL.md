---
name: embed-bonsai
description: Embed bonsai's editor, preview or a headless document in a web page or app. Triggers: integrate editor or preview, wire views, process markdown without a view.
---

```ts
const doc = createDocument({ text, syntax: [...bonsaiExtensions, mentions] });
const editor = createEditor(editorElement, doc, { extensions: [format({ bold: 'cmd b', italic: 'cmd i' }), softBreak, autoClose, autoIndent] });
const preview = createPreview(previewElement, doc, { extensions: [katex], delay: 200 });
```

- one document per text, views attach to it, never copy text between views
- syntax → `createDocument({ syntax })` or `document.use`, default `bonsaiExtensions` (GFM, math, footnotes, abbreviations), GFM only → `gfmExtensions`
- view extensions → the factory's `extensions` or `view.use`
- editor only → import nothing from `@bonsai/preview`
- headless (worker, server, CLI) → document only: `text`, `ast`, `edit`, `history`, `commands`; HTML → `renderPreviewHTML(document.ast)`
- preview renders after every change, `delay`: debounce in milliseconds while typing; hidden preview skips, `preview.update()` when shown
- persist → `document.on('change', ({ text, changes }) => { if (changes.length > 0) save(text); })`
- show an offset → `view.reveal(offset)`, text on screen → `view.visibleRange()`
- sync scrolling between views → interpolate between both views' `scrollAnchors()`: `{ offset, top }` in the element's scroll content
- shortcut outside the editor → listen on the page, `document.commands.run(id)`
- teardown → `view.destroy()`, document outlives its views
- `katex` extension → install `katex`, a peer dependency
- code highlighting → `codeHighlight` from `@bonsai/editor` and from `@bonsai/preview`, import one under another name: `import { codeHighlight as previewCodeHighlight } from '@bonsai/preview'`; highlight.js is a peer dependency of `@bonsai/core`
- styles → import `@bonsai/editor/editor.css`, `@bonsai/preview/preview.css`, define `--bonsai-color-text`, `-dimmed`, `-muted`, `-border`, `-link`, `-surface`, with `codeHighlight` `--bonsai-color-code-keyword`, `-string`, `-number`, `-title`, `-type`, `-comment`, `--bonsai-font-serif`, `-sans`, `-mono`, no fallback exists
- untrusted documents → `sanitize` preview extension: https://github.com/danieldietrich/bonsai/blob/main/examples/security/sanitize.ts
- custom behavior → write-extension skill, interactive UI → write-widget skill
