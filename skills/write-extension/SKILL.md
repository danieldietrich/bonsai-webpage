---
name: write-extension
description: Write a bonsai extension: inline markdown syntax, editor behavior, preview rendering, or a feature across them. Triggers: new inline syntax, editor shortcut or behavior, custom preview rendering.
---

## Parts
| part | install | read |
|---|---|---|
| syntax | `document.use(syntax)` | `syntax.md` |
| editor | `editor.use(part)` or `createEditor` option `extensions` | `editor.md` |
| preview | `preview.use(part)` or `createPreview` option `extensions` | `preview.md` |

- feature = parts sharing one `name`, one module per part, each part optional
- editor part never imports preview part, and the reverse
- `use` returns `Dispose`, install and remove any time, install order = hook order
- view part = `(view) => Dispose | void`: registrations made while it runs are removed with it, return a `Dispose` for the rest (document listeners, timers, page listeners)
- install for an owner (agent, plugin) → `const scope = document.scope(owner)`, `scope.add(view.use(part))`, `scope.dispose()` removes everything
- user-triggerable action → `document.commands.register({ id, description, args, run })`, key → `editor.keys.bind('cmd b', id)`; acts on one view → `view: editor`, runs in the view focused last
- react to text change → `document.on('change', ({ changes, origin }) => …)`, skip own `origin`
- markdown structure → syntax part only, view parts read `document.ast`, `document.positionAt(offset)`, `document.isVerbatim(offset)`
- block-level UI → write-widget skill, extensions add no block syntax
- import `@bonsai/core`, `@bonsai/editor`, `@bonsai/preview`, never `@bonsai/core/internal`

## Test
| part | assert |
|---|---|
| syntax | `extension` nodes in `createDocument({ text, syntax }).ast` |
| editor | `document.text`, `selection.get()`, `decorations.get(name)` after `document.edit` or `document.commands.run(id)` |
| preview | `preview.element` after `document.edit` |
| removal | state before `use` equals state after `Dispose` |
