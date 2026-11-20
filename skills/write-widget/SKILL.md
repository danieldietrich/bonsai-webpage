---
name: write-widget
description: Build interactive UI from markdown in a bonsai document: charts, forms, tables, controls, UI generated for a task. Triggers: widget, interactive block, render a fenced code block as UI.
---

## Source
- widget state = markdown in the document, no state elsewhere
- widget = fenced code block at the top level, info string's first word = kind: ```` ```chart ````
- fence content = data the widget parses, JSON unless the user names a format

## Register
- `document.widgets.use({ kind, mount })` returns `Dispose`, kind `[a-z][a-z0-9-]*`, new in the document
- `mount(root, context)` returns `{ update?(source), dispose() }`
- `root`: fresh element in the host's shadow root, owned by bonsai: fill it, put styles inside it, never `customElements.define`, no global CSS
- `context`: `view` (`'editor' | 'preview'`), `source`, `document`, `write(source)`
- no `update` → a source change disposes and mounts again

## Views
| view | widget appears |
|---|---|
| preview | in place of the fence |
| editor | floating at the top right of the fence, the fence stays editable text |

- one instance per view: state lives in the fence, never in the instance

## Write back
- user changes the widget → `context.write(source)`: replaces the fence content, one undo step, origin `widget:<kind>`
- every view's instance then gets `update(source)`
- fence inside a list or quote → `write` throws

## Lifecycle
- `dispose` → remove listeners, timers, observers, workers
- heavy computation → Web Worker, terminate in `dispose`

## Test
- `const doc = createDocument({ text })`, `createPreview(element, doc)`, `doc.widgets.use(widget)`
- assert `element.querySelector('bonsai-widget').shadowRoot`, `doc.text` after an interaction
