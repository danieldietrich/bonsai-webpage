---
author: daniel
---

# Bonsai

_Bonsai doesn't write—it listens._

Writing is like growing a Bonsai tree. It takes time. You shape it gently, following the grain of the wood rather than forcing it into a predetermined form.

[Bonsai](https://bonsai.md)  is a Markdown editor that removes everything that doesn't serve the act of writing. There are no word counts, no save indicators, no formatting buttons. The mouse cursor hides itself after a moment of stillness. What remains is a warm, paper-like surface and a blinking caret.

## Documents without servers

Bonsai has no backend. There are no accounts, no databases, no cloud storage. Your document lives in exactly two places: your browser and—when you choose to share it—in a URL.

Pressing ⌘S does not save to disk. It copies a shareable link to your clipboard. The link contains your entire document, compressed and encoded in the URL fragment—the part after the `#`. Anyone who opens that link sees your document in their own Bonsai editor. The link _is_ the document.

When you type, every keystroke auto-saves to local storage—silently, immediately, without confirmation. When you reload the page, your document is still there. When you clear it, you start fresh. There is no concept of "files" or "projects"—just the current document on the paper.

## The document graph

Every Bonsai document carries a reference to its origin. When you open a shared link and begin editing, the new document records _where it came from_—not by name or timestamp, but by the cryptographic hash of the content it was derived from. This creates a structure that emerges naturally from writing and sharing: a directed graph of documents.

### The model

A document is a pair `(content, parent)` where:

- _content_ is the markdown text—the full, plain-text string
- _parent_ is the SHA-256 hash of the content this document was derived from

The **root** is the empty document. Its content is the empty string, and its parent is the hash of the empty string—making it the only document whose parent hash equals the hash of its own content:

```
root = ("", hash(""))
```

Since the parent of any document is the hash of the _content_ it was derived from, and the empty document was derived from nothing, the only coherent choice is: its parent is the hash of its own content. The root is its own origin.

When you share a document, the URL encodes both the content and the parent hash. The recipient's editor loads the content and records the parent. If they edit and share again, their new document's parent becomes the hash of what they received—creating a new link in the chain.

### Branches

Suppose Alice writes "Hello world" starting from an empty page, and shares the link. Both Bob and Carol open Alice's link independently.

Bob changes it to "Hello World!" and shares his version. Carol changes it to "Greetings world" and shares hers. The graph now looks like this:

```
hash("Hello World!")   hash("Greetings world")
             │          │
             ▼          ▼
          hash("Hello world")
                   │
                   ▼
               hash("")
```

Two documents—Bob's and Carol's—both point to the same parent. A branch has formed. Not because anyone chose to "create a branch," but because two people independently continued from the same starting point. Branching is a natural consequence of sharing.

### Content collisions

The graph identifies nodes by the hash of their content. Two documents with identical text produce the same hash—regardless of how they came to exist.

Consider this editing sequence:

1. Start at root ("")
2. Type "a"—parent is hash("")
3. Change to "A"—parent is hash("a")
4. Change back to "a"—parent is hash("A")

Steps 2 and 4 both produce content "a", but with different parents. The node hash("a") now has _two_ parent edges, and the graph contains a cycle:

```
hash("") ◀︎──── hash("a") ◀︎──── hash("A")
                  │               ▲
                  └───────────────┘
```

The same content was reached via two different paths, and the graph records both derivations. In distributed scenarios, if two people independently type identical text from the same starting point, they produce the same node with the same edge. The graph cares about _what was written_, not _who wrote it_ or _when_.

### Traversal and its limits

The parent chain defines a theoretical path from any document back to root. But following this path requires having the actual intermediate documents, not just their hashes. A hash identifies content but does not contain it. From a single shared link, you hold one document and know its parent's hash, but that is as far as you can see.

**The hash captures derivation, not complete history.** To reconstruct the full graph, you would need to collect every document that was ever shared. In a serverless system, there is no central place where all documents accumulate. The graph is emergent, decentralized, and always partial from any single observer's perspective.

When a content collision creates a cycle, the shared link resolves the ambiguity: it encodes a specific _(content, parent)_ pair, so the parent recorded in _your_ link determines which edge you follow.

### A formal summary

Let _H_ be a cryptographic hash function (SHA-256).

A **document** is a pair _(c, p)_ where _c_ is a string (the content) and _p = H(c')_ for some predecessor content _c'_.

The **root** is _(e, H(e))_ where _e_ is the empty string. It is the unique document satisfying _p = H(c)_.

The **document graph** _G = (V, E)_ is defined as:

- _V_ = the set of content hashes _H(c)_ for all documents _(c, p)_ that have been created
- _E_ = the set of directed edges _(H(c), p)_ for all documents _(c, p)_ that have been created

Properties of the graph:

- _Content-addressed._ Nodes are identified by hash(content), not by any external identifier.
- _Directed._ Each edge points from a document to its parent—from derivation to origin.
- _Rooted._ The root is the unique node with a self-loop: _(H(e), H(e))_.
- _Acyclic when content is never revisited._ Cycles emerge when the same content is reached via different derivation paths.
- _Partially observable._ A single document reveals exactly one parent edge. The full graph requires collecting all documents.
- _Time-agnostic._ Edges encode "was derived from," not temporal order.

## Memory for machines

The document graph emerged from human needs: writing, sharing, revising. But the structure itself—text that evolves, branching derivations, content-addressed history—describes something more general.

Large language models, autonomous agents, agentic workflows—the vocabulary is new, but the underlying need is familiar: a way to pass context from one step to the next. Humans use documents. Agents use context—and context, stripped to its essence, is text.

### Context is a document

An agent's context—system prompt, conversation history, plan, observations—is text that shapes behavior. As the agent works, the context evolves. Each snapshot maps directly to Bonsai's document model: the context is the content, the hash of the previous context is the parent. Each step produces a new document in the graph.

### Agents branch

When an agent delegates a subtask, it refines its current context for the subtask and passes it as a new document. The sub-agent works from this derived context independently. In the document graph, delegation is branching:

```
              sub-sub-agent
              context
                 │
                 ▼
sub-agent A   sub-agent B
context       context
       │         │
       ▼         ▼
      agent context
```

The structure is identical to humans sharing documents. Alice shares with Bob and Carol; a parent agent delegates to sub-agents A and B. Context diverges when work is parallelized.

### Applications

**State checkpointing.** An agent saves its context as a Bonsai link at key decision points. If a line of reasoning leads nowhere, it can return to a checkpoint and branch in a different direction.

**Audit trails.** The chain of parent hashes is a verifiable record of how context evolved. The diff between consecutive documents shows the agent's action at each step.

**Parallel exploration.** An agent facing multiple options forks its context into branches, explores each independently, and compares results. Dead ends are visible as branches that were never continued.

**Cross-agent coordination.** Agent A's output context becomes Agent B's input. The connection is a link. No shared database, no message queue, no API contract— just a URL containing text and a parent hash.

**Human-in-the-loop.** A human opens an agent's context in the editor, reads the reasoning, makes corrections, and shares the modified link back. The graph captures the intervention as a branch point.

**Reproducibility.** Given a Bonsai link, you know exactly what context an agent had. The hash proves it—any modification, however small, produces a different hash.

### Ephemeral by default, persistent by choice

Agent working memory does not need permanent storage. A context exists for the duration of a task. When the task is complete, the intermediate states can be discarded—or preserved as links for later inspection.

A sub-agent is spawned with a context (a link). It works, evolves its context, and returns a result (a new link). The sub-agent's intermediate states exist only if they were explicitly saved. No growing archive of stale state, no garbage collection, no permission model for historical context. The graph exists only as the union of links that someone—human or machine—has chosen to retain.

### The interface dissolves

For humans, Bonsai removes the interface between the writer and the text. For agents, the same principle applies. There is no database API, no authentication flow, no file system abstraction. The protocol is: read text from a URL, do work, write text to a URL.

Human documents and agent contexts interleave in the same graph, branching from one another. A person writes a prompt; an agent branches from it. The agent produces a result; a person branches from that. What remains is what was always there: _content, and where it came from._

## Growing slowly

Bonsai is a small thing. A single static page. A markdown editor. A document model with a text and a hash.

The documents written in Bonsai—by humans and machines alike—form a graph that grows across the network. No central authority. Just content, linked by derivation, spreading through the act of sharing a link.

This is version one. The paper is ready. The caret is blinking.
