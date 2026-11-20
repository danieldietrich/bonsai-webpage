# write-widget

- Markdown is the widget's state, so a document with widgets stays shareable, diffable and editable as text, and agents generate UI and data together.
- A bonsai-owned host instead of `customElements.define`: custom element definitions are global and can never be removed or redefined, which breaks removal and iteration.
- Bonsai attaches the host's shadow root and hands the widget a fresh element inside it: the preview's DOM patching never touches it, and a remount replaces only that element.
- The editor shows widgets as overlays because every editor block's `textContent` must equal the source; replacing the fence would break caret mapping.
- A widget writes only its own fence so several widgets and agents edit one document without clobbering each other.
