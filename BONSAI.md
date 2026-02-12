---
author: daniel
---

# Bonsai

_Bonsai doesn't write — it listens._

Writing is like growing a Bonsai tree. It takes time and love. You cannot rush it. You shape it gently, over days and months, following the grain of the wood rather than forcing it into a predetermined form.

Bonsai is a markdown editor at [bonsai.md](https://bonsai.md). Its goal is singular: provide the best writing experience possible — the way Kindle provides the best reading experience. Not by adding features, but by removing everything that doesn't serve the act of writing.

There are no toolbars. No word counts. No save indicators. No formatting buttons. No settings panels. The mouse cursor hides itself after a moment of stillness. What remains is a warm, paper-like surface and a patiently blinking caret — an invitation: _tell me what you are thinking._

Finding the perfect words, optimizing reading flow, checking spelling and grammar — these are optimization tasks that constantly judge our writing and pull us onto a structural layer, away from deeply thinking about ideas. Bonsai protects the space where ideas form. When in doubt, leave it out.

The markdown syntax _is_ the interface. Delimiters are visible but dimmed — you always know the structure of your document, but the visual noise stays low. There are no hidden panels to discover, no modes to switch between. What you see is what there is.

Bonsai is defined as much by what it _does not_ have as by what it does. No cloud. No analytics. No login. No third-party scripts. Zero runtime dependencies. A calm environment. Focus. Warm colors that give you room to breathe. A slow pulse that says: _take your time._

## Documents without servers

Bonsai has no backend. There are no accounts, no databases, no cloud storage. Your document lives in exactly two places: your browser's local storage and — when you choose to share it — in a URL.

Pressing ⌘ S does not save to disk. It copies a shareable link to your clipboard. The link contains your entire document, compressed and encoded in the URL fragment — the part after the `#`. Anyone who opens that link sees your document in their own Bonsai editor. No server was involved. No data was transmitted to any third party. The link _is_ the document.

This is stateless by design. Bonsai does not know who you are. It does not track what you write. It does not phone home. The editor is a static page served once; everything else happens in your browser.

When you type, every keystroke auto-saves to local storage — silently, immediately, without confirmation. When you reload the page, your document is still there. When you clear it, you start fresh. There is no concept of "files" or "projects" — just the current document on the paper.

Sharing is spreading links. You paste a Bonsai link in a chat, an email, a note. The recipient opens it, and now they have their own copy. If they edit it and share again, a new link is born. Documents propagate through the network of people who share them, with no central authority keeping track.

This is how documents have always wanted to work.

## The document graph

Every Bonsai document carries a hidden thread connecting it to its origin. When you open a shared link and begin editing, the new document remembers _where it came from_ — not by name or timestamp, but by the cryptographic hash of the content it was derived from. This creates a structure that emerges naturally from the act of writing and sharing: a directed graph of documents.

### The model

A document is a pair `(content, parent)` where:

- _content_ is the markdown text — the full, plain-text string
- _parent_ is the SHA-256 hash of the content this document was derived from

The **root** is the empty document. Its content is the empty string, and its parent is the hash of the empty string — making it the only document whose parent hash equals the hash of its own content:

```
root = ("", hash(""))
```

This is not an arbitrary convention. It is the only self-consistent definition. Since the parent of any document is the hash of the _content_ it was derived from, and the empty document was derived from nothing, the only honest answer is: its parent is the hash of itself. The root is its own origin.

This also produces a clean invariant: clearing all text in the editor always returns you to root. There is no special "new document" action. Deleting everything _is_ starting over.

When you share a document, the URL encodes both the content and the parent hash. The recipient's editor loads the content and records the parent. If they edit and share again, their new document's parent becomes the hash of what they received — creating a new link in the chain.

### Branches

Suppose Alice writes "Hello world" starting from an empty page, and shares the link. Both Bob and Carol open Alice's link independently.

Bob changes it to "Hello World!" and shares his version. Carol changes it to "Greetings world" and shares hers. The graph now looks like this:

```
         hash("")
            ▲
            │
    hash("Hello world")
        ▲       ▲
        │       │
hash("Hello   hash("Greetings
 World!")       world")
```

Two documents — Bob's and Carol's — both point to the same parent: the hash of Alice's text. A branch has formed. Not because anyone chose to "create a branch," but because two people independently continued from the same starting point. Branching is a natural consequence of sharing.

This is true regardless of _when_ the edits happened. Bob might have edited minutes after Alice shared; Carol might have edited days later. There is no timestamp in the model. There is no universal clock to rely on in a distributed system where documents travel as links through chats, emails, and bookmarks. The only thing that is recorded is _what_ was written and _what it was derived from_.

### Content collisions

The graph identifies documents by the hash of their content. Two documents with identical text produce the same hash — regardless of how they came to exist. This leads to an interesting property: content collisions.

Consider this editing sequence:

1. Start at root ("")
2. Type "a" — parent is hash("")
3. Change to "A" — parent is hash("a")
4. Change back to "a" — parent is hash("A")

Steps 2 and 4 both produce documents with content "a", so they share the same content hash. But they have different parents: step 2's parent is hash(""), step 4's parent is hash("A"). In the graph, the node hash("a") now has _two_ parent edges:

```
hash("") ◀︎──── hash("a") ◀︎──── hash("A")                       
                  │               ▲
                  └───────────────┘
```

The node hash("a") points to both hash("") and hash("A") as parents. The graph contains a cycle.

This is not a defect — it is an accurate reflection of what happened. The same content was reached via two different paths. The graph does not pretend there was a single linear history; it honestly records the full branching structure.

Now consider a distributed scenario. Alice and Bob both independently start from an empty page and each type "hello". They have never communicated. Both produce a document with content "hello" and parent hash(""). In the graph, these are the _same node_ connected by the _same edge_. Two people, separated by distance and time, unknowingly created the same document. The graph treats them as one — because the graph cares about _what was written_, not _who wrote it_ or _when_.

But if Alice then changes her text to "Hello" while Bob changes his to "hello world", the graph branches from a node that two people contributed to independently:

```
         hash("")
            ▲
            │
      hash("hello")
        ▲       ▲
        │       │
hash("Hello") hash("hello world")
```

The graph captures the structure of derivation faithfully, even when the paths that created it were entirely independent.

### Traversal and its limits

From any document, you can follow the parent chain backward:

```
doc ──▶︎ parent ──▶︎ parent's parent ──▶︎ ... ──▶︎ root
```

This gives you one path — one branch of the history. If you hold only a single shared link, you can trace the lineage of _that specific document_ back toward the root, but you cannot see the branches that diverged along the way. You see the trunk beneath your feet, not the canopy around you.

This is a fundamental property: **the hash captures derivation, not complete history.** To reconstruct the full graph — all branches, all convergences, all collisions — you would need to collect every document that was ever shared. In a serverless system, there is no central place where all documents accumulate. The graph is emergent, decentralized, and always partial from any single observer's perspective.

When a content collision creates a cycle — as in the "a" / "A" / "a" example above — traversal from the colliding node encounters ambiguity: which parent edge to follow? The answer depends on which specific document you hold. The shared link encodes a specific _(content, parent)_ pair, not just a content hash. The traversal follows the parent recorded in _your_ link, which traces _your_ branch of the graph unambiguously back to root. Other branches through the same node exist but are invisible to you unless someone shares them.

Each shared link is a leaf. Each chain of parents is a branch. The full graph is the union of every chain that was ever created — a tree that grows silently, distributed across the browsers and clipboards of everyone who has ever used Bonsai.

### A formal summary

Let _H_ be a cryptographic hash function (SHA-256).

A **document** is a pair _(c, p)_ where _c_ is a string (the content) and _p = H(c')_ for some predecessor content _c'_.

The **root** is _(e, H(e))_ where _e_ is the empty string. It is the unique document satisfying _p = H(c)_.

The **document graph** _G = (V, E)_ is defined as:

- _V_ = the set of content hashes _H(c)_ for all documents _(c, p)_ that have been created
- _E_ = the set of directed edges _(H(c), p)_ for all documents _(c, p)_ that have been created

Properties of the graph:

- _Content-addressed._ Nodes are identified by hash(content), not by any external identifier. There are no document IDs, no filenames, no timestamps.
- _Directed._ Each edge points from a document to its parent — from derivation to origin.
- _Rooted._ The root is the unique node with a self-loop: _(H(e), H(e))_.
- _Acyclic under linear editing,_ but cycles emerge naturally from content oscillation (reaching the same content via different paths).
- _Partially observable._ Any single traversal reveals one branch. The full graph requires collecting all documents.
- _Time-agnostic._ Edges encode "was derived from," not temporal order. There is no before or after — only structure.

## Memory for machines

We live in an era where machines write too.

Large language models, autonomous agents, agentic workflows — the vocabulary is new, but the underlying need is ancient: a place to think, a surface to work on, a way to pass context from one moment to the next. Humans use paper, notebooks, documents. Agents use _context_ — and context, stripped to its essence, is text.

### Context is a document

An agent's context is the text that defines what it knows and what it should do. A system prompt is a document. A skill description is a document. A tool specification is a document. A conversation history is a document. A plan, a reflection, a summary of observations — all documents. Each one is a piece of markdown text that shapes the agent's behavior.

The terminology varies, but the pattern is universal:

- A **system context** is a markdown document that defines the agent — its role, its personality, its constraints. It is the root from which all reasoning grows.
- **Skills** are markdown documents that describe capabilities the agent can invoke — how to search, how to write code, how to communicate with external systems.
- **Tool descriptions** are markdown documents that specify the interfaces available to the agent — the parameters, the expected behavior, the boundaries.
- An **evolving context** is what happens when the agent works. It observes, reasons, acts, and its accumulated understanding is captured as text — an ever-growing document that represents the agent's current state of mind.

When an agent begins its work, it starts with an initial context: a markdown document assembled from system prompt, skill definitions, and tool descriptions. As it works — observing, reasoning, acting — its context evolves. New information is appended or integrated. The context at any given moment is a snapshot of the agent's working memory.

This is exactly the model Bonsai already provides. A document. A parent. A hash.

### Agents branch

When an agent delegates a subtask to a sub-agent, what happens? The parent agent takes its current context, refines it for the subtask — adding specific instructions, narrowing the scope, injecting relevant observations — and passes this refined context to the child. The sub-agent begins working from this derived context. It evolves independently: reading, thinking, acting, accumulating its own observations. When it finishes, it returns a result that the parent agent integrates back into its own context.

In Bonsai's document graph, this is a branch. The parent agent's context at the moment of delegation is the parent node. The sub-agent's initial context is a new document derived from it — a new node with a parent edge pointing back. If the sub-agent delegates further, the chain extends. Multiple sub-agents working in parallel create multiple branches from the same parent.

```
      agent context
       ▲         ▲
       │         │
sub-agent A   sub-agent B
context       context
                 ▲
                 │
              sub-sub-agent
              context
```

The structure is identical to humans sharing documents. Alice shares with Bob and Carol; a parent agent delegates to sub-agent A and sub-agent B. The graph does not distinguish between human branching and machine branching. It captures the same fundamental pattern: context diverges when work is parallelized.

And just like human collaboration, there is no universal clock. Sub-agent A might finish before sub-agent B. They might run on different machines, in different time zones, at different speeds. The graph does not encode when — only what was derived from what.

### Identity through content

In Bonsai's model, a document is identified by the hash of its content. For agents, this is a powerful feature. An agent's working state at any moment _is_ its context — the text it holds. The hash of that text becomes a natural identifier for that state.

Think of it as: **agent context = node in the graph.** The parent edge records where this context came from. The content hash is the address.

The agent's identity is not a separate field — it is embedded in the content itself. The system prompt, the role description, the skills, the accumulated observations — all of this _is_ the context, and the hash of the context _is_ the identity of the agent in that state.

Two agents with identical context are, for all practical purposes, the same agent in the same state — just as two documents with identical content are the same node in the graph. If they diverge (different observations, different actions), their contexts diverge and the graph branches. If they converge independently (arrive at the same conclusions through different paths), the graph converges — same content, same hash, same node. A content collision, as natural for machines as it is for humans.

### Ephemeral memory

Agents do not need permanent storage the way humans traditionally imagine it. An agent's working memory exists for the duration of a task. When the task is complete, the memory can be discarded — or preserved as a link, a reference, a hash pointing back to a specific state.

Bonsai is not a database. It is a runtime memory surface. Documents exist in local storage while the agent is working and in URLs when context needs to be shared or preserved. There is no growing mountain of stale state. No garbage collection problem. No permission model for historical context. The graph exists only as the union of links that someone — human or machine — has chosen to retain.

A sub-agent is spawned with a context (a Bonsai link). It works, evolves its context through a chain of parent relationships, and returns a result (a new link). The parent agent integrates the result into its own context, creating yet another node. The sub-agent's intermediate states exist only if they were explicitly shared — ephemeral by default, persistent by choice.

This ephemerality mirrors the nature of thought itself. We do not remember every intermediate step of our reasoning. We remember the conclusions, the insights, the turning points. The rest fades — and that is not a loss, it is clarity.

### The interface dissolves

For humans, Bonsai removes the interface between the writer and the text. No toolbars, no buttons, no menus — just the paper and the caret.

For agents, the same principle applies at a different level. There is no database API, no authentication flow, no file system abstraction. There is only text in, text out. Context is created by writing markdown. Context is shared by passing a URL. Context is branched by deriving a new document. The "interface" is the document model itself — and it is so simple that it barely qualifies as an interface at all.

This matters because the best tool for thought — whether human or machine — is one that disappears. For humans, that means a calm writing surface. For machines, that means a minimal, uniform protocol: text, hashes, and parent links. Nothing more.

The graph that emerges is the same graph. Human documents and agent contexts, interleaved, branching from one another. A person writes a prompt; an agent branches from it. The agent produces a result; a person branches from that. The distinction between human and machine nodes dissolves. What remains is what was always there: _content, and where it came from._

## Growing slowly

Bonsai is a small thing. A single static page. A markdown editor with no features to speak of. A document model with a text and a hash.

But small things, tended carefully, become something more. A Bonsai tree is not impressive because of its size. It is impressive because every branch, every curve, every leaf was placed with intention.

The documents written in Bonsai — by humans and machines alike — form a graph that grows silently across the network. No central authority, no master branch. Just content, linked by the invisible threads of derivation, spreading outward through the simple act of sharing a link.

This is version one. The paper is ready. The caret is blinking.

_Tell me what you are thinking._
