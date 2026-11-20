- editor part = `(editor: EditorAPI) => Dispose | void`

| need | API |
|---|---|
| look of extension nodes | `editor.nodes.attributes(name, node => attributes)`: `class`, `title`, `aria-*`, `data-*` |
| highlight ranges | `editor.decorations.set(name, ranges)`, style with `::highlight(<name>)` |
| change text | `editor.document.edit(changes, { selection, undo, origin })` |
| read or move selection | `editor.selection.get()`, `set(range)`, `reveal()` |
| action on key | command + `editor.keys.bind('cmd b', id)` |
| intercept typing | `editor.on('input', (event, selection) => handled)`, check `event.inputType` |
| other keys | `editor.on('keydown', (event) => handled)` |
| UI anchored to text | widget → write-widget skill |

- editor DOM → read only, block `textContent` = markdown source
- listeners → on `editor.element`, never `document`
- edit the user did not type → own `origin`, no `selection`: the user's caret stays
- edits in quick succession as one undo step → same `undo` name
- `changes`: sorted, not overlapping, offsets into the text before the edit
- key binding: `cmd`, `ctrl`, `shift`, `alt` + key, never Alt/Option + letter
- reference: https://github.com/danieldietrich/bonsai/blob/main/examples/proofread.ts
