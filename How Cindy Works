# How Cindy Works: A Learning Guide

A personal study guide to the Cindy client monorepo: what it is, how a message flows through it, and how **Memory** and **Skills** work.

All paths are relative to the repo root. Most of the internal docs (`docs/dev-rules/`, `docs/product-rules/`) are written in Chinese.

---

## Table of contents

1. [The big picture](#1-the-big-picture)
2. [Repo layout](#2-repo-layout)
3. [Topics to learn, in order](#3-topics-to-learn-in-order)
4. [The agent engine: maker-core](#4-the-agent-engine-maker-core)
5. [Desktop app: one message, start to finish](#5-desktop-app-one-message-start-to-finish)
6. [Model routing](#6-model-routing)
7. [Tools and extensions](#7-tools-and-extensions)
8. [Mobile, cross-device and cloud](#8-mobile-cross-device-and-cloud)
9. [Memory: correct it once and it remembers](#9-memory-correct-it-once-and-it-remembers)
10. [Skills: teach a workflow once and reuse it everywhere](#10-skills-teach-a-workflow-once-and-reuse-it-everywhere)
11. [Memory vs Skills](#11-memory-vs-skills)
12. [Where to start reading the code](#12-where-to-start-reading-the-code)

---

## 1. The big picture

Cindy doesn't have its own AI agent. It is a **host (wrapper) around existing agent CLIs**: **Claude Code**, **Codex** and **Pi**. It gives them one shared UI, task history, set of tools, memory and model routing.

This repo is the **client** only (desktop, mobile and shared packages). The backend server lives in a separate repo.

```
 Renderer (React)  ──IPC──▶  Electron main (host)  ──▶  maker-core  ──▶  claude / codex / pi CLI
      ▲                          │   │   │                  │                 │
      └──── 'maker:event' ◀──────┘   │   │                  └── events ◀──────┘
                                     │   └─ MCP tools (browser, scheduler, memory, plugins…)
   Phone ◀── device-link relay ──────┘
```

Key points:
- Agents, files and task history live **locally** on your desktop.
- The **cloud** handles sign-in, the device relay, the model gateway, billing, SkillHub and the plugin market.
- The **phone** is a remote control for the desktop; it has no agent of its own.

---

## 2. Repo layout

| Path | What it is |
|---|---|
| `apps/desktop` | Electron desktop app (`main/`, `preload/`, `renderer/`, `shared/`) |
| `apps/mobile` | Expo / React Native phone app |
| `apps/cindy-headless` | Command-line / container runner that reuses maker-core without the desktop UI |
| `packages/maker-core` | The agent engine: one interface over the three CLIs |
| `packages/maker-shared` | Display-layer types shared by desktop and mobile |
| `packages/lizi-mcps` | Built-in MCP tool servers |
| `packages/cindy-tools` | Plugin gateway (`ghost_list`, `ghost_info`, `ghost_call`) |
| `packages/device-link*` | Cross-device protocol (phone ↔ desktop) |
| `packages/model-providers` | Model and provider catalog, routing |
| `packages/*-bridge`, `anthropic-compat-proxy`, `model-compat` | API format translation |
| `packages/maker-scheduler` | Cron engine |
| `packages/orca-workflow` | Multi-agent lead/worker orchestration |
| `packages/maker-remote-ssh`, `maker-cc-manager`, `maker-pi-manager`, `remote-file-service` | Running agents on remote machines over SSH |
| `config/` | Endpoint lists for the `cn` and `global` regions |
| `tools/` | Pinned versions of the claude, codex, pi and ripgrep binaries |
| `docs/` | Rules: `dev-rules/`, `product-rules/`, `design-rules/` |

The best first map of the code is `docs/dev-rules/repo-map.md`.

---

## 3. Topics to learn, in order

About **40 topics in 9 groups**. Groups 1–3 (about 16 topics) are enough to understand how the app works; read 4–9 as you need them.

### Group 1: Foundations (5)
- [ ] What Cindy is: a host around the Claude Code, Codex and Pi CLIs
- [ ] Monorepo layout
- [ ] Architecture rules (packages never import desktop code; no dynamic `import()` in main)
- [ ] Electron process split: main, preload, renderer, shared
- [ ] Desktop startup sequence (`bootstrap-electron.ts`)

### Group 2: Agent engine, maker-core (6)
- [ ] The `BaseAgent` interface and session handles
- [ ] `Maker` and `Session` (turns, watchdogs, event fan-out)
- [ ] The common `AgentEvent` format
- [ ] Claude Code adapter (Agent SDK and translator)
- [ ] Codex adapter (`app-server` JSON-RPC)
- [ ] Pi adapter (RPC mode and the cindy-bridge extension)

### Group 3: The message lifecycle (5)
- [ ] Composer → `makerChatStore` → preload IPC call
- [ ] `AgentInputCoordinator` (queue, steer, interject, resume)
- [ ] The send transaction (`makerSendTransaction`)
- [ ] Event pipeline back to the UI (save to DB, broadcast `maker:event`)
- [ ] Rendering the chat (`MessageStream` and its cards)

### Group 4: Agent behaviour (5)
- [ ] How the system prompt is assembled (added after each CLI's own prompt)
- [ ] Permission modes and approval prompts (`InteractionResolver`)
- [ ] Usage tracking and pricing
- [ ] Switching models within one CLI
- [ ] Switching between CLIs mid-task (the handoff note built from history)

### Group 5: Model routing (3)
- [ ] `model-providers` (the catalog and `resolveRoute`)
- [ ] The Anthropic-compatible proxy
- [ ] API format bridges (Messages ↔ Responses ↔ Chat Completions) and `model-compat`

### Group 6: Persistence (3)
- [ ] SQLite database (drizzle, about 68 tables, runs in a worker thread)
- [ ] Migrations (`apps/desktop/drizzle/`)
- [ ] Multiple accounts (one database file per account)

### Group 7: Tools and extensions (7)
- [ ] Built-in MCP servers (`lizi-mcps`)
- [ ] How tools reach each CLI (directly, HTTP bridge, Pi bridge)
- [ ] `cindy_helper` and `cindy_scheduler`, which reveal their tools gradually
- [ ] User-added MCP servers and MCP Apps
- [ ] Plugins ("ghosts" / `.cindy` packages)
- [ ] SkillHub skills
- [ ] Browser, computer and Android control

### Group 8: Automation and collaboration (6)
- [ ] Scheduler (cron jobs that start tasks)
- [ ] IM channels (Telegram, Feishu, WeChat and others)
- [ ] Official hook channels (Slack protocol)
- [ ] Orca multi-agent (one lead, several workers)
- [ ] Bots/companions and bot group chat
- [ ] Memory and project context

### Group 9: Cross-device and cloud (6)
- [ ] device-link protocol (relay, WebRTC direct link, allowlist, reconnects)
- [ ] Mobile app (routes, mirroring tasks, creating tasks, over-the-air update limits)
- [ ] Sign-in, regions (`cn` / `global`), endpoints, Skip Sign-In mode
- [ ] What the cloud does vs what stays local
- [ ] Remote SSH (cc-manager, pi-manager, remote file service)
- [ ] Shared tasks, provider sharing, provider groups

**Optional extras:** the auto-updater, worktrees, voice input, log upload and redaction, the design system and i18n, `cindy-headless`.

---

## 4. The agent engine: maker-core

`packages/maker-core` turns three different CLIs into one model. It has **no Electron code**; the desktop app passes in everything it needs (binary paths, login details, tools, prompts).

### The main pieces
- **`BaseAgent`** (`packages/maker-core/src/agents/base-agent.ts:2690`) is the interface each CLI adapter implements. Its main method is `startSession()`, which returns a handle with:
  - `send`: start a new turn
  - `steer`: add to the turn that is running
  - `abort`
  - `setModel`, `setEffort`, `setPermissionMode`
  - `events()`: the stream of events
- **`Maker.createSession`** (`packages/maker-core/src/maker.ts:631`) wraps each handle in a **`Session`** (`src/session.ts`). The `Session` manages turns, watchdogs and how events are passed on.
- **`AgentEvent`** (`src/types/events.ts`) is the one event type every CLI's output is translated into:
  `text`, `thinking`, `tool_use`, `tool_result`, `interaction_request`, `status` (token usage), `done`, `error`, and a few more.

### How each CLI is driven

| CLI | How Cindy talks to it | Key files |
|---|---|---|
| **Claude Code** | Through the official Claude Agent SDK `query()`, pointed at the bundled `claude` binary | `agents/claude-code/index.ts`, `translator.ts` |
| **Codex** | Starts `codex app-server` and talks JSON-RPC over stdio (`thread/start`, `turn/start`, `turn/steer`) | `agents/codex/app-server/stdioTransport.ts`, `translator.ts` |
| **Pi** | Starts `pi --mode rpc` and talks JSONL over stdio; a `cindy-bridge` extension in Pi handles approvals and tools | `agents/pi/index.ts`, `translator.ts` |

Each adapter has a **translator** that converts the CLI's raw output into `AgentEvent`s.

### System prompt
Cindy's instructions (memory rules, plugin list, host instructions) are **added after** each CLI's default system prompt; they never replace it.
- Claude Code: `systemPrompt: { type: 'preset', preset: 'claude_code', append }`
- Codex: `developerInstructions`
- Pi: `--append-system-prompt`

### Switching CLI mid-task
Handled by the desktop host (`apps/desktop/src/main/maker-ipc/sessionAgentSwitchHandler.ts`):
1. Pause the old CLI's session.
2. Build a handoff note **from the saved history, with no model call** (`agentHandoff.ts` → `buildHandoffText`):
   - the last few turns, word for word
   - one-line summaries of older turns
   - the files and commands that were touched
3. Put that note in front of the next user message sent to the new CLI. The message you see in the UI stays as you typed it.
4. The new CLI can fetch older detail with the `cindy_helper` tools `get_chat_history` / `search_chat_history`.

---

## 5. Desktop app: one message, start to finish

### Process layout
- **`main/`**: trusted. Does the real work: processes, database, IPC, permissions.
- **`preload/`**: a thin bridge (`contextBridge.exposeInMainWorld('electronAPI', …)`).
- **`renderer/`**: untrusted React UI. No Node or Electron access.
- **`shared/`**: types and constants used by both sides.

### Startup (`apps/desktop/src/main/bootstrap-electron.ts`)
Endpoints → custom protocols → IPC handlers → per-account SQLite database → window → splash screen checks and downloads the CLI binaries → Maker engine → scheduler, IM channels, device-link, updater.

### The path of one message
1. **Composer**: `ChatInput.tsx` passes the message to `handleSend` in `CCAgentSessionView.tsx`.
2. **Renderer store**: `makerChatStore.sendMessage` calls `electronAPI.maker.input.enqueue`.
3. **Preload**: sends IPC `maker:input:enqueue`.
4. **Main handler**: `maker-ipc/register.ts` checks the sender and permissions, then calls `AgentInputCoordinator.enqueue`.
5. **Queue**: `AgentInputCoordinator` keeps a **per-task queue saved to disk**. This is what makes queueing, interjecting and resuming after a crash work.
6. **Send**: the queue drains into `makerSendTransaction`, which calls `session.send()` in maker-core.
7. **CLI runs**: maker-core drives Claude Code, Codex or Pi.
8. **Events back**: `sessionEventPipeline` saves each event to SQLite, then broadcasts it as `maker:event` to every window.
9. **Render**: `makerChatStore` updates the task, and `MessageStream.tsx` draws message, tool and thinking cards.

Messages from IM, the scheduler and bots enter the **same queue** through `sendToSessionInternal`.

### Storage
- SQLite through better-sqlite3 and drizzle-orm, running in a worker thread.
- About 68 tables: `sessions`, `messages`, `agent_input_queue_snapshots`, `schedules`, `orca_teams`, `bot_profiles`, usage tables and more.
- **One database file per account**: `<userData>/<prefix>-<userId>.db`.
- Migrations are in `apps/desktop/drizzle/`. They are append-only and never edited once merged.

### Renderer
- No Redux. Module-level stores (`lib/makerChatStore.ts`, `lib/sessionsStore.ts`) are read with `useSyncExternalStore`.
- The layout is a saved split/pane tree (`shared/layoutTree.ts`). There is exactly one `chat-main` panel.

---

## 6. Model routing

How Cindy lets any CLI use (almost) any model:

| Package | Role |
|---|---|
| `model-providers` | Model catalog; `resolveRoute` decides where a request goes and in what format |
| `anthropic-compat-proxy` | Local proxy that Claude Code's `ANTHROPIC_BASE_URL` points to. Claude models pass through unchanged; for other models it strips Anthropic-only fields. |
| `anthropic-responses-bridge` | Anthropic Messages → OpenAI Responses, so **Claude Code can run GPT or xAI models** |
| `responses-anthropic-bridge` | Responses → Anthropic, so **Codex can run Claude-style models** |
| `responses-chat-bridge` | Lets **Codex** use providers that only support Chat Completions |
| `model-compat` | Per-provider fixes (tool schemas, reasoning fields and so on) |

Pi needs none of this: it talks to each provider's protocol natively.

---

## 7. Tools and extensions

- **Built-in tools are MCP servers running inside the app** (`packages/lizi-mcps/src/providers.ts`): `cindy_browser`, `cindy_computer`, `cindy_android`, `cindy_scheduler`, `cindy_helper`, `cindy_memory`, `cindy_orca`, `cindy_ssh`, IM bot tools and more.
  - Claude Code uses them directly.
  - Codex reaches them through a local HTTP bridge (`codexHttpBridge.ts`).
  - Pi reaches them through its `cindy-bridge` extension.
- **Progressive discovery**: `cindy_helper` and `cindy_scheduler` expose only `list_tools` / `call_tool`, so the prompt stays small.
- **Plugins** (`.cindy` zip files, called "ghosts" in code): each runs in its own sandboxed Electron process. The agent finds them through `ghost_list`, `ghost_info` and `ghost_call`.
- **Scheduler** (`packages/maker-scheduler`): ticks every second. When a job fires, it creates or reuses a task and sends the scheduled message.
- **IM channels** (Telegram, Feishu, WeChat, DingTalk, Discord, WeCom, Slack): an incoming chat message is routed to a task, run as a normal turn, and the final reply is sent back.
- **Orca** (multi-agent): one lead task controls several worker tasks. Each worker can use any CLI and model. Workers report back automatically and the lead reviews their results.
- **Bots/companions**: long-lived agents, each with its own personality file (`SOUL.md`), memory, skills and model fallback chain.

---

## 8. Mobile, cross-device and cloud

- **device-link**: phone and desktop both connect to a cloud relay using the same account. When possible, the link upgrades to a direct WebRTC connection on the local network.
  - The phone calls desktop IPC channels through a **deny-by-default allowlist** (`packages/device-link/src/allowlist.ts`).
  - It subscribes to `sessions` and `session:<id>` to mirror tasks live.
  - **The desktop is always the source of truth.**
- **Mobile** (`apps/mobile`): an Expo Router app. It finds desktops through the relay; it has no agent of its own.
- **Regions**: `cn` (`config/endpoint.json`) and `global` (`config/endpoint.global.json`).
- **Skip Sign-In**: runs entirely locally; cloud features are unavailable.
- **Remote SSH**: Cindy installs small helper programs on the remote machine so agents run there while the UI stays local.

---

## 9. Memory: correct it once and it remembers

### What a memory is
Each memory is a **small Markdown file** with a short header. Example file `feedback_response_style.md`:

```markdown
---
title: Response style
description: One-line hook shown in the index
type: feedback
updatedAt: 2026-05-10T12:34:56Z
---
Keep answers short.
**Why:** user said long answers waste time.
**How to apply:** every reply in this project.
```

Four types (`packages/maker-core/src/memory/types.ts`):

| Type | Holds | Example |
|---|---|---|
| `user` | Who you are, your preferences | "prefers concise answers" |
| `feedback` | Corrections and "do this, not that" | must include **Why** and **How to apply** |
| `project` | Decisions, constraints, deadlines for this folder | "release freeze until Nov 1" |
| `reference` | Links to outside systems | dashboard URLs, docs |

A hidden fifth type, `digest`, is used when Pi compacts its context. It stores summaries of the dropped content so they stay searchable, but never puts them in the prompt.

### Where it's stored
- Under `userData/owners/<account-hash>/…`, with **one store per working directory** (`apps/desktop/src/main/maker-host/maker-memory-host.ts`).
- Git worktrees map back to the main repo, so a task in a worktree sees the same memories (`scope-resolver.ts`).
- `MEMORY.md`: an index rebuilt automatically, one line per memory.
- `fts.db`: a SQLite full-text search index (FTS5) built from the files.
- **The Markdown files are the source of truth**; the index can always be rebuilt.
- Each bot/companion has its own separate memory folder.

### How "correct once" works
1. **When a task starts**, Cindy adds the memory rules (`packages/maker-core/src/memory/system-prompt.md`) and the **current `MEMORY.md` index** to the system prompt. This happens for Claude Code, Codex and Pi alike.
2. The rules tell the model **when to save**: phrases like "I prefer X", "don't X", "always", "never", "remember", or a correction that would apply again.
3. The rules also say **what not to save**: code structure, git history, anything already in `CLAUDE.md` / `AGENTS.md`, temporary progress, raw tool output.
4. The model saves through the **`cindy_memory` MCP tool**.
5. **In the next task, with any CLI**, the index is in the prompt again, so the correction applies automatically.

### The `cindy_memory` tools

| Tool | What it does |
|---|---|
| `memory_write` | Create, update or append a memory |
| `memory_read` / `memory_list` | Read one memory / list all |
| `memory_search` | Full-text search |
| `memory_delete` | Remove a memory |
| `memory_consolidate` | Merge several memories into one |
| `memory_review` | A cheap model call that suggests cleanups (suggestions only) |

**Manual trigger:** start a message with `Save in memory:` and the rest is saved.

### Why it's "shared across harnesses"
- Each CLI has its own built-in memory: Claude Code writes to `~/.claude/projects/.../memory/`, Codex to `<CODEX_HOME>/memories/`.
- When Cindy memory is on, **those built-in memories are switched off** (`manager.ts` calls `setMemory(false)` on each agent).
- The prompt also tells the model to ignore any other memory path.
- Result: there is only **one** memory, and every CLI reads and writes it.

### Safety limits
| Limit | Value |
|---|---|
| Memory file soft limit (warning) | 2 KB |
| Memory file hard limit (rejected) | 8 KB |
| `MEMORY.md` index soft limit | 4 KB |
| Description length | 200 characters |

If the account isn't ready or is switching, memory **refuses to read or write** rather than writing to the wrong place.

### What happens when a memory would hit 9 KB?
**Cindy has no algorithm that automatically shrinks or summarizes memories in code.** It uses a simple byte check plus a "the model fixes it" loop.

**Step 1: hard check before writing** (`packages/maker-core/src/memory/storage.ts`, in `write()`):
```ts
// For mode 'append', nextBody = old body + "\n\n" + new text
if (Buffer.byteLength(nextBody, 'utf8') > this.config.hardShardBytes) {   // 8192
  throw new MemoryError('shard-too-large', `body ${bytes} bytes > 硬上限 8192`);
}
```
- The size is measured in **UTF-8 bytes** of the body only (the header isn't counted). A Chinese character is 3 bytes, so Chinese text hits the limit sooner.
- The check happens **before** anything is written. The write is rejected and **the old file is left unchanged**. Nothing is cut off.

**Step 2: the error goes back to the model.** `packages/lizi-mcps/src/memory/errors.ts` turns `shard-too-large` into a tool error `INVALID_PARAMS` with the message `body 9216 bytes > 硬上限 8192` ("硬上限" means "hard limit").

**Step 3: the model fixes it**, following the memory rules in its prompt. Its options:
1. **Shorten**: rewrite the memory more tightly and write again with `mode: "update"`.
2. **Split**: move part of it into a second memory with a different `name`.
3. **Merge**: call `memory_consolidate({ sources, target })`, where the model supplies the merged, shorter text.

**How `memory_consolidate` works** (`store.ts → consolidate()`); it is a simple sequence, not a compression algorithm:
1. Write the target memory (update it if it already exists, otherwise create it). The target **must also be ≤ 8 KB**.
2. Delete each source memory (skipping the target itself and any that are already gone).
3. Rebuild the search index and `MEMORY.md` once.
4. Re-check the sizes and return any warning.

If deleting a source fails partway, the deletions already done are not undone; the problem is logged.

**Soft warnings (above 2 KB, below 8 KB)** don't block anything. The write succeeds and returns, for example:
```json
{ "warning": "shard-size-exceeded",
  "warningDetail": { "sizeBytes": 3100, "softLimitBytes": 2048, "hardLimitBytes": 8192 } }
```
The prompt tells the model to leave it if it's slightly over, and to trim or consolidate if it's well over.

**`memory_review`** (`manager.ts → runReview()`) sends all memories to a cheap model with the instruction "classify each as contradiction / outdated / duplicate / fine; plan ≤ 200 words". It only returns **suggestions**; the agent still has to call delete or consolidate itself.

**Pre-compaction flush** (`flush-controller.ts`): this watches context usage at 70%, 85% and 92%. Right now it **only logs**; it does not yet make the model save memory.

| Situation | Code behaviour | Who fixes it |
|---|---|---|
| Body ≤ 2 KB | Written | — |
| 2 KB to 8 KB | Written, plus a `shard-size-exceeded` warning | The model, optionally (trim or consolidate) |
| Body > 8 KB (for example 9 KB) | **Rejected**, old file unchanged, `INVALID_PARAMS` | The model must shorten, split or consolidate |
| `MEMORY.md` > 4 KB | Written, plus an `index-size-exceeded` warning (no hard limit) | The model, optionally merges memories |

### Key files
- `packages/maker-core/src/memory/` (`manager.ts`, `store.ts`, `storage.ts`, `fts.ts`, `scope-resolver.ts`, `system-prompt.md`, `types.ts`)
- `packages/lizi-mcps/src/cindy_memoryMcpServer.ts`, `cindy_memoryToolRegistry.ts`
- `apps/desktop/src/main/maker-host/maker-memory-host.ts`

---

## 10. Skills: teach a workflow once and reuse it everywhere

### What a skill is
A **folder containing a `SKILL.md`** (instructions plus a description), optionally with scripts or reference files. It is the same format Claude Code uses natively.

```
my-release-skill/
├── SKILL.md        ← name, description, step-by-step instructions
└── scripts/        ← optional helpers
```

### Where skills are stored (one copy, linked into each CLI)
- Default install location: **`~/.agents/skills/<name>/`**, shared by all CLIs.
- A link at `~/.claude/skills/<name>` lets Claude Code find it natively (`apps/desktop/src/main/skillhub/installService.ts`).
- Skills for one project only: `<project>/.agents/skills/<name>`.

### How each CLI gets the skills

| CLI | How skills are loaded | Key file |
|---|---|---|
| Claude Code | Before each task, Cindy builds a temporary **local plugin** containing only the skills allowed for that task | `packages/maker-core/src/agents/claude-code/managed-skill-plugins.ts` |
| Codex | Cindy creates `cindy-*` links inside the Codex home folder | `apps/desktop/src/main/maker-host/managed-skills.ts`, `codex-global-skills.ts` |
| Pi | Pi's own skill discovery, with disabled skills filtered out | `packages/maker-core/src/agents/pi/skill-activation.ts` |

You can turn skills on or off globally or per task (`apps/desktop/src/main/skillhub/activationPreferences.ts`).

### How "teach once" works: the `/learn` flow
Cindy ships three built-in skills (`apps/desktop/src/shared/cindyBuiltInSkills.ts`):
- **`learn`**: turns a finished task into a reusable skill
- **`cindy-skill-creator`**: helps write a skill from scratch
- **`cindy-plugin-creator`**: helps write a plugin

The `/learn` flow:
1. You type **`/learn`** after finishing a task. You can also describe a workflow in text, or point at a SkillHub skill.
2. The agent calls the `start_skill_learning` tool (`packages/lizi-mcps/src/xdt-helper/start_skill_learning.ts`).
3. Cindy:
   - gathers evidence from the task;
   - turns it into a skill in a **separate task**;
   - stages the proposed skill;
   - **asks you to review it** before installing.
4. Once installed in `~/.agents/skills`, the skill is available to all three CLIs.

### SkillHub (sharing skills)
- **Install:** download a signed zip → check SHA-256 → unpack to a staging folder → swap into place → record in the registry.
- **Uninstall:** the skill goes to the system Trash.
- **Agent tools:** `search_skills`, `publish_skill`, `list_my_published_skills`, `get_skill_publish_status` (`packages/lizi-mcps/src/xdt-helper/skillhub.ts`).
- **Teams:** an organization can install skills for everyone automatically (`organizationAutoInstall.ts`).

### Key files
- `apps/desktop/src/main/skillhub/` (install, publish, scan, activation, sync)
- `apps/desktop/src/main/maker-host/built-in-skills.ts`, `managed-skills.ts`, `codex-global-skills.ts`
- `packages/maker-core/src/agents/claude-code/managed-skill-plugins.ts`
- `packages/maker-core/src/agents/pi/skill-activation.ts`

---

## 11. Memory vs Skills

| | **Memory** | **Skills** |
|---|---|---|
| Holds | Facts and preferences | Procedures and workflows |
| Example | "Keep answers short" | "How we do a release" |
| Size | Short notes (≤ 8 KB) | Full instructions plus scripts |
| Loaded | Index injected into **every** task | Loaded **only when relevant** |
| Scope | Per working directory (or per bot) | Global (`~/.agents/skills`) or per project |
| Created by | The model, when you correct it or say "remember" | `/learn`, the skill creator, SkillHub, or by hand |
| Shared across CLIs by | Turning off each CLI's built-in memory and using `cindy_memory` | One folder, linked or mounted into each CLI |

---

## 12. Where to start reading the code

1. `docs/dev-rules/repo-map.md`: a code map organized by user action.
2. `apps/desktop/src/main/maker-ipc/register.ts`: search for `INPUT_ENQUEUE`.
3. `apps/desktop/src/main/maker-ipc/agent-input-coordinator.ts`: the input queue.
4. `packages/maker-core/src/session.ts`, then `maker.ts`.
5. One CLI adapter end to end: `packages/maker-core/src/agents/claude-code/index.ts` with `translator.ts`.
6. Memory: `packages/maker-core/src/memory/system-prompt.md`, then `manager.ts`.
7. Skills: `apps/desktop/src/main/skillhub/installService.ts`, then `managed-skill-plugins.ts`.
