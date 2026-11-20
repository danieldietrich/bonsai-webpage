- syntax part = `MarkdownExtension`: `name`, rules; signatures in `@bonsai/core` types
- name: `[a-z][a-z0-9-]*`, not a node type, new in the document

| rule | matches | example |
|---|---|---|
| `text` | pattern in plain text | `@user`, `#123` |
| `trigger` | span at a start character, before emphasis and links | `{{var}}` |
| `definition` | single line at paragraph start | `*[HTML]: HyperText Markup Language` |

- match → `extension` node with `name` and typed `data`, views render it
- content not markdown → `verbatim: [name]`
- `document.use(syntax)` and its `Dispose` re-parse, views render again
- offsets absolute, ranges half-open
- reference: https://github.com/danieldietrich/bonsai/blob/main/examples/references/syntax.ts
