- preview part = `(preview: PreviewAPI) => Dispose | void`, registers `preview.renderHooks(factory)`
- factory runs once per render, state across nodes (numbering, ids) → factory closure

| hook key in `nodes` | renders |
|---|---|
| node type | `heading`, `link`, … |
| extension name | the extension's `extension` nodes |
| `definition:<name>` | the extension's definitions |
| `code-block:<language>` | fenced code by first word of the info string |

- nodes after the document → `finish(context)` beside `nodes`
- hook: `(node, context) => RenderNode | RenderNode[] | undefined`, `undefined` keeps the default
- wrap built-in rendering → `context.default()`
- output render nodes from `element(tag, attributes, children)`, no HTML strings
- block element → keep `data-source-line` from `context.default()`, scroll sync reads it
- change the parsed HTML of every render → `preview.transform(fragment => …)`
- after a render changed the DOM → `preview.on('render', …)`; DOM changes there are undone by the next render
- untrusted documents → `sanitize`: https://github.com/danieldietrich/bonsai/blob/main/examples/security/sanitize.ts
- interactive output → write-widget skill
- reference: https://github.com/danieldietrich/bonsai/blob/main/examples/references/preview.ts
