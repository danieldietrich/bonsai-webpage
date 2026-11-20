# drive-bonsai

- Served from bonsai.md so an agent running in the page fetches its instructions from the same origin, next to the consumer skills.
- `window.bonsai` names editor and preview: the document's `views` list is in attach order, which an agent would have to guess.
- Everything goes through a scope because the page has no guardrails: the scope is the only way to take back what an agent did.
- Commands instead of synthetic key events: keys are user configuration, command ids are a stable contract.
