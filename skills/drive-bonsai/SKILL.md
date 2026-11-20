---
name: drive-bonsai
description: Act as an agent inside a running bonsai.md page: read and edit the document, call commands, install extensions and widgets at runtime. Triggers: code running in bonsai.md, augment bonsai at runtime.
---

## Entry
- `window.bonsai`: `document` (`BonsaiDocument`), `editor` (`EditorAPI`), `preview` (`PreviewAPI`)
- capabilities → `bonsai.document.commands.list()`: ids, descriptions, argument schemas
- syntax → `bonsai.document.markdown.extensions`, widget kinds → `bonsai.document.widgets.kinds()`
- skills: `https://bonsai.md/skills/<name>/SKILL.md` for `write-extension`, `write-widget`, `embed-bonsai`

## Scope
- first → `const me = bonsai.document.scope('<agent-id>')`
- edit → `me.edit(changes, options)`, origin = agent id
- install → `me.add(…)` around every `use`, `on`, `widgets.use`, `commands.register`
- done, failed or user asks → `me.dispose()`
- other owners' registrations → never dispose unless the user asks
- spawned agent → own scope, own id

## Act
| need | API |
|---|---|
| user context | `document.text`, `document.ast`, `editor.selection.get()`, `view.visibleRange()` |
| show the user a place | `view.reveal(offset)` |
| existing capability | `document.commands.run(id, args)`, never simulate keys |
| new capability | command in own scope, key via `editor.keys.bind` inside an editor part |
| change behavior or look | write-extension skill |
| UI for the task | write-widget skill |
| long computation | `new Worker(url)`, `me.add(() => worker.terminate())` |
| persistence | `localStorage` keys prefixed `bonsai:<agent-id>:` |

- react to user edits → `me.add(document.on('change', …))`, skip `origin` = own id
- no guardrails: documents run their HTML, keep secrets out of documents and storage
