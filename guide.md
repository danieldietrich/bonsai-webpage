# Bonsai user guide

Bonsai is a markdown editor that runs in your browser at <https://bonsai.md>. It has no toolbars, menus, accounts, or settings screens. You open the page and start typing.

## Getting started

1. Open <https://bonsai.md>.
2. Type. The logo fades away once you start.
3. Close the tab whenever you like. Bonsai saves as you type, and your text, caret position, and view are back the next time you open the page.

Clicking the logo on an empty page opens this guide. When you're done reading, press ⇧⌘⌫ to clear the page and start writing.

Your document is stored only in this browser. There is no server copy and no sync between devices. To move a document to another device or person, share a link (see [Sharing](#sharing)).

Bonsai keeps **one document per browser**. Two open tabs save to the same place, and the last change wins, so keep one tab open.

The mouse pointer hides after two seconds without movement and comes back when you move the mouse.

## Keyboard shortcuts

On Windows and Linux, use Ctrl wherever this guide says ⌘.

### Writing

| Shortcut | What it does |
|---|---|
| ⌘B | Bold the selection (`**text**`), or remove bold |
| ⌘I | Italicize the selection (`_text_`), or remove italics |
| ⌘Z / ⇧⌘Z | Undo / redo |
| ↩ in a list or quote | Start the next item or quote line. On an empty item, step out one level |
| ⇥ / ⇧⇥ | Indent / outdent by two spaces. On a list item, nest it under the item above / move it back up to the parent list |
| ⇧↩ | Line break inside a paragraph (inserts `<br>`) |

Bonsai also completes some typing for you:

- Typing `` ` ``, `(`, `[`, or `{` inserts the closing character and puts the caret between them. If you then type the closing character, Bonsai steps over the one already there.
- Pressing ↩ after a fence that opens a block (```` ``` ````, `~~~`, or `$$`) adds the closing fence below.
- Typing `>` to finish an HTML tag such as `<mark>` adds the closing `</mark>` after the caret.

### Views and document

| Shortcut | What it does |
|---|---|
| ⇧⌘P | Switch views: editor → split → preview → editor |
| ⇧⌘0 | Turn scroll sync on or off in split view |
| ⌘S | Copy a share link for the document |
| ⇧⌘⌫ | Clear the document |

## Views

Bonsai has three views. Press ⇧⌘P to go to the next one.

- **Editor**: the text alone. Markdown is styled as you type, but the symbols stay visible.
- **Split**: the editor and the formatted preview together. If the window is at least as wide as it is tall, the preview is on the right. Otherwise it is below the editor. Resizing the window or rotating a tablet switches the layout right away.
- **Preview**: the formatted document alone. When you go back to the editor, your caret is where you left it.

In split view, the editor and preview scroll together so the same passage stays in view in both. Press ⇧⌘0 to turn this off or back on. Bonsai remembers the setting.

## Sharing

Press **⌘S**, or tap the share button that appears when you touch the screen on a phone or tablet. "Link copied!" confirms that a link is on your clipboard.

The **whole document is inside the link**, compressed. Nothing is uploaded. This means:

- Anyone who has the link can read the document. Share it like you would share the text.
- The link is a snapshot. Later edits don't change it, so press ⌘S again to share a newer version.
- Longer documents make longer links.

A shared link opens in preview. Press ⇧⌘P to edit.

> **Opening any document link replaces the document in your browser.** Share or copy your current text first if you want to keep it.

## Opening markdown from the web

Put `#` and a web address after `https://bonsai.md/?` to open a markdown file in Bonsai:

```text
https://bonsai.md/?#https://raw.githubusercontent.com/user/repo/main/README.md
```

GitHub Gists have a shorter form:

```text
https://bonsai.md/?#gist:<user>/<gist-id>
https://bonsai.md/?#gist:<user>/<gist-id>/<file-name>
```

If you leave out the file name, you get the gist's first file. You always get its latest version.

Things to know:

- The file opens in preview and replaces your current document.
- Only `http` and `https` addresses work. The server must allow other websites to load the file. GitHub raw files and Gists do.
- Bonsai waits at most 10 seconds and loads at most 1 MB.
- If loading fails, Bonsai starts with an empty document.

## Linking to a heading

Every heading in the preview gets an address made from its text. `## Getting Started` becomes `#getting-started`. If two headings have the same text, the second gets `-1` added, the third `-2`, and so on.

- Inside a document, `[see below](#getting-started)` jumps to that heading when clicked in the preview.
- `https://bonsai.md/#getting-started` opens your saved document and scrolls to the heading if the preview is showing. In the editor-only view it does nothing.

## Link options

Add these to the address after `?` to choose how Bonsai opens. They also become your saved settings.

| Option | Values | Effect |
|---|---|---|
| `previewMode` | `0` editor, `1` preview, `2` split | Opening view |
| `scrollSync` | `0` off, `1` on | Scroll sync in split view |
| `caretPosition` | a number, `0` or more | Where the caret starts, counted in characters |

Examples:

```text
https://bonsai.md/?previewMode=2
https://bonsai.md/?previewMode=2&scrollSync=0
https://bonsai.md/?previewMode=2#gist:<user>/<gist-id>
```

Invalid values are ignored.

## Clearing the document

**⇧⌘⌫** empties the page. "Fresh mind" confirms that it worked. Whenever the document becomes empty in the preview-only view, Bonsai switches back to the editor.

> Clearing also erases undo history. **You can't undo it.** Press ⌘S first if you might want the text back.

## Printing and PDF

Use your browser's print command (⌘P). Bonsai prints only the formatted document, whatever view you are in. To save a PDF, choose "Save as PDF" in the print dialog.

## Markdown you can use

Bonsai supports standard markdown (CommonMark) and the GitHub additions:

| You type | You get |
|---|---|
| `# Title`, `## Section` | Headings |
| `**bold**`, `_italic_`, `~~struck~~` | **bold**, _italic_, ~~struck~~ |
| `` `code` `` | Inline code |
| ```` ``` ```` on its own line | A code block |
| `- item`, `1. item` | Lists. Numbered lists inside numbered lists switch to 1. → i. → a. |
| `- [ ] task`, `- [x] done` | Task lists |
| `> quote` | Quotes |
| `[text](https://…)` | Links. Plain `https://…`, `www.…`, and email addresses become links on their own |
| `![alt text](image.png)` | Images |
| `\| a \| b \|` with a `\|---\|---\|` row below | Tables |
| `---` | A horizontal rule |

### Math

Bonsai displays math written in LaTeX:

```markdown
Euler: $e^{i\pi} + 1 = 0$

$$
\int_0^1 x^2 \, dx = \frac{1}{3}
$$
```

Inline math goes between single `$` signs on one line. Block math goes between `$$` lines. To write a plain dollar sign, type `\$`.

### Footnotes and abbreviations

```markdown
Bonsai is quiet.[^1]

[^1]: Mostly.

The HTML spec is long.

*[HTML]: HyperText Markup Language
```

Footnotes are numbered in the preview and link to their text. An abbreviation shows its full form when you hover over the word.

### HTML

Some HTML tags, such as `<mark>`, `<b>`, and `<sub>`, are styled in the editor too. All HTML appears in the preview, so you can make a fold-out section:

```markdown
<details>
<summary>Spoiler</summary>

Markdown works in here when blank lines surround it.

</details>
```

The preview shows HTML as written, including event handlers such as `onclick`, embedded frames, forms and style sheets (`<style>`); only `<script>` elements don't run. Open documents from people you trust: a shared document can run code in Bonsai.

### Front matter

A block between two `---` lines at the very top of a document counts as metadata. It is hidden in the preview.

### Line breaks

Ending a line with two spaces or a backslash does **not** start a new line. Press **⇧↩** to insert a line break.

## Privacy

- Your document stays in your browser and in any links you share. Bonsai has no server that stores text.
- Bonsai contacts another website only when you open a link that points to a remote file.
- Clearing your browser's site data for bonsai.md deletes your document.
