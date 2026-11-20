# write-extension

- One skill for three parts because writing a feature usually touches all three and shares one `name`. The part files load only when the agent writes that part.
- Parts stay separate modules so an editor-only app carries no preview code.
- Lives in `@bonsai/core` because every editor or preview install pulls core in, so the skill is always present.
- Registrations made while a part runs belong to the part: an agent never has to collect its own disposers, and a scope takes back everything an owner installed.
- The removal test catches the most common runtime bug: a part that leaves listeners, decorations or DOM behind.
- Signatures stay in the `.d.ts` files; the skill holds only what the types cannot say. References point to GitHub because `examples/` is not published.
