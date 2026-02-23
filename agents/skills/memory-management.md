# Memory Management Skill — Configuration and Patterns

> Prevents the three memory failure modes that kill agent effectiveness.
> Source: Kevin Simback's Lobsternaut memory guide (x-links/7).
> Every agent must follow these protocols.

## The Three Failure Modes

Memory fails in three specific ways. Each requires a different fix.

### Failure 1: Memory Is Never Saved

The agent decides what to save — it's a judgment call by the model in real-time. Important context slips through because the model deems it not worth storing. Like an employee who decides which meeting notes to keep and which to throw away.

**Fix**: Memory flush configuration (see R-001 below).

### Failure 2: Memory Is Saved But Never Retrieved

Facts make it to disk, but the agent answers from its context window instead of searching memory. From your perspective it "forgot" — in reality it never looked.

**Fix**: Hybrid search configuration + explicit search-before-answer rules (R-002, R-003).

### Failure 3: Context Compaction Destroys Knowledge

To avoid hitting token limits, older messages get summarized or removed. Any information only in the active conversation (not yet saved to disk) is destroyed. Even MEMORY.md content loaded at session start can get summarized away mid-conversation.

**Fix**: Early flush triggers + external memory storage (R-001, R-004).

---

## Core Rules

### R-001: Memory Flush Configuration (Critical)

Enable memory flush to trigger a silent turn before compaction that writes durable memories to disk. This is the single most impactful configuration change.

```json
{
  "compaction": {
    "memoryFlush": {
      "enabled": true,
      "softThresholdTokens": 40000,
      "prompt": "Distill this session to memory/YYYY-MM-DD.md. Focus on decisions, state changes, lessons, blockers. If nothing: NO_FLUSH",
      "systemPrompt": "Extract only what is worth remembering. No fluff."
    }
  }
}
```

Key points:
- **Customize the prompt** — the default is too generic. Tell it exactly what to capture: decisions, state changes, lessons, blockers
- **Raise softThresholdTokens to 40000** — triggers flushes earlier, before valuable context gets compacted
- **"If nothing: NO_FLUSH"** — prevents empty memory files from cluttering the system

### R-002: Context Pruning Configuration

Control how old messages are removed before full compaction. Use cache-TTL mode.

```json
{
  "contextPruning": {
    "mode": "cache-ttl",
    "ttl": "6h",
    "keepLastAssistants": 3
  }
}
```

- Keeps messages from last 6 hours
- Always preserves 3 most recent assistant responses
- Eliminates the situation where you repeat recent context after a flush

### R-003: Hybrid Search Configuration

Combine vector similarity (conceptual matching) with BM25 keyword search (exact tokens). Both are needed.

```json
{
  "memorySearch": {
    "enabled": true,
    "sources": ["memory", "sessions"],
    "query": {
      "hybrid": {
        "enabled": true,
        "vectorWeight": 0.7,
        "textWeight": 0.3
      }
    }
  }
}
```

- **Vector search** catches conceptual matches ("collision probability" finds "Pc calculation")
- **BM25** catches exact matches (error codes, project names, wallet addresses)
- Without hybrid, exact-match queries miss constantly

### R-004: Session Indexing

Index past session transcripts so conversations from weeks ago are searchable.

```json
{
  "memorySearch": {
    "sources": ["memory", "sessions"]
  },
  "experimental": {
    "sessionMemory": true
  }
}
```

Makes "what did we decide about X last Tuesday?" answerable.

### R-005: Search Before Answering

When asked about past decisions, preferences, or project state, agents MUST search memory before answering from context. The rule:

1. Question involves recall? → Call `memory_search` first
2. Found relevant result? → Use it as the authoritative answer
3. No result? → Answer from context, but flag it as "not found in memory — may be inaccurate"
4. After answering from memory, if the info is important, verify it's still saved to disk

### R-006: Daily Memory File Structure

Memory files follow this naming and structure:

```
agents/memory/
├── MEMORY.md                      # High-level cross-agent learnings
├── 2026-02-18.md                  # Daily memory flush
├── 2026-02-19.md
└── ...
```

Daily files capture:
- **Decisions made** — what was decided and why
- **State changes** — what changed in the codebase, config, or infrastructure
- **Lessons learned** — what worked, what didn't, what to do differently
- **Blockers** — what's stuck and what's needed to unblock
- **Key context** — anything that would be expensive to rediscover

### R-007: What to Always Save to Memory

These categories must ALWAYS be written to memory, never left to the model's judgment:

1. **User preferences** — communication style, engineering values, tool preferences
2. **Architectural decisions** — why we chose X over Y
3. **API keys and service configurations** (references, not the actual keys)
4. **Deployment outcomes** — what was deployed where, success/failure
5. **Bug root causes** — the actual cause, not just the symptom
6. **Performance baselines** — build times, bundle sizes, response times
7. **Token holder/subscriber metrics** — weekly snapshots

### R-008: What NOT to Save

Don't pollute memory with:
- Routine status updates ("build passed")
- Temporary debugging output
- Full code listings (reference the file path instead)
- Speculative ideas not yet validated
- Duplicate information already in skill files

---

## Advanced Memory Tools (Evaluate When Needed)

### QMD — Superior Retrieval Backend

Developed by Tobi (CEO of Shopify). Replaces built-in SQLite indexer with BM25 + vectors + reranking. Better retrieval quality.

**Key feature**: Index external document collections (Obsidian vault, project docs) alongside agent memory.

**When to adopt**: When basic hybrid search isn't finding relevant results, or when you need to search across external knowledge bases.

### Mem0 — Compaction-Proof External Memory

YC-backed. Stores memories outside the context window entirely.

Two automatic processes per turn:
- **Auto-Capture**: Detects and stores info without depending on LLM judgment
- **Auto-Recall**: Searches and injects relevant memories before agent responds

**Solves**: Failure Modes 1 and 3 completely. Memory is captured automatically and survives any compaction.

**When to adopt**: When memory flush alone isn't catching enough context, or when running multi-day complex projects.

### Cognee — Knowledge Graphs

Builds a knowledge graph from data. Queries relationships between entities.

**Example**: "Alice owns the auth module" creates nodes for Alice with an "owns" edge to auth module.

**When to adopt**: Enterprise multi-agent teams, complex relationship queries. Overkill for basic setups.

### Obsidian Integration

Two approaches:

**Symlink** (simple):
```bash
ln -s ~/workspace/memory ~/Obsidian/AgentMemory
```
Agent memory appears in Obsidian on all devices for review and annotation.

**QMD index** (powerful):
Everything in Obsidian vault becomes searchable by agents. Obsidian 1.12 CLI allows metadata queries (cheaper than reading full files).

---

## Multi-Agent Memory Architecture

For Lobsternaut's 5-agent system:

| Layer | What | How |
| --- | --- | --- |
| **Private memory** | Each agent's workspace, daily notes | `agents/memory/` per-agent subdirectories |
| **Shared reference** | User profile, agent roster, conventions | `agents/memory/MEMORY.md` + `AGENTS.md` |
| **Shared search** | All agents search same reference docs | QMD with shared paths (when adopted) |
| **Coordination** | DocumentationAgent reads core files at session start | Nightly review protocol (documentation.md R-010) |

Key principle: Treat agent memory like a human team's documentation. Some things are shared (handbook, architecture, conventions). Some things are private (work in progress, agent-specific patterns).

---

## Failure Log

_No memory failures logged yet. When memory issues are encountered, document them here with root cause and fix._

## Configuration Changelog

```
### [2026-02-18] Initial configuration
- Memory flush enabled (softThresholdTokens: 40000)
- Hybrid search enabled (vector 0.7, BM25 0.3)
- Session indexing enabled
- Context pruning: cache-ttl 6h, keep 3 assistant messages
```
