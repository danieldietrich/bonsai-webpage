# embed-bonsai

- The document is the kernel and the views are adapters on it, so wiring is three calls and any number of views stay in sync without app code.
- The example stays because the three calls are the whole contract and a rule list alone would leave the argument order ambiguous.
- Missing CSS custom properties is the most likely silent failure for an embedder: the package CSS has no fallbacks by design.
- Security is opt-in since bonsai.md runs without guardrails; embedders showing untrusted text add it themselves.
