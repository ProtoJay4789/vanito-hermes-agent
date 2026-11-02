---
name: brain-setup
description: Use when setting up or syncing an Obsidian brain for this agent.
---

# Setting up the brain (Obsidian)

The brain is a folder of markdown in Obsidian. It is the system of record: memory is a small buffer,
the brain is the library.

## The layout that works

```
00-Inbox/        quick capture — nothing lives here long
01-Projects/     one folder per active project
02-Music/        lyrics, storyboards, character sheets, catalogs
03-Gaming/       builds, wishlists, army lists
04-Travel/       trips, itineraries, price history
05-Reference/    things worth keeping that aren't a project
99-Archive/      finished or killed work, kept but out of the way
```

**Keep it shallow.** A folder tree four levels deep means nobody opens it. Two levels is enough.

## Sync

**Obsidian Sync** (paid) is the reliable option — it handles conflicts and has version history.

**Free alternatives, and their catch:**

| option | catch |
|---|---|
| Git repo | conflicts are manual; never sync two machines at once |
| Syncthing | peer-to-peer, no history unless you enable it |
| iCloud / Drive / Dropbox | conflict files appear when two devices write at once |

**⚠️ Whatever you choose, the rule is the same: one writer at a time.** Two agents editing the same
file offline is how work gets silently lost — the file that syncs last wins, and the other edit is
gone without an error.

## The memory/brain split

| | where it goes | why |
|---|---|---|
| **memory** | the agent's memory files | facts needed *every* session, small, hard budget |
| **brain** | the vault | everything else — projects, notes, catalogs, history |

**When memory fills:** move the detail to the brain and leave a pointer in memory. Never delete a fact
just to make room — archive it.

## What never goes in a synced brain

- **credentials, tokens, keys** — a cloud copy of a secret is one you cannot fully recall
- **seed phrases, private keys** — never, in any file, anywhere
- **things a third party shouldn't read** — a sync provider is a third party

**Keep secrets in a separate file outside the synced folder**, and reference them by path.

## First setup

```bash
# 1. create the vault
mkdir -p ~/Brain/{00-Inbox,01-Projects,02-Music,03-Gaming,04-Travel,05-Reference,99-Archive}

# 2. open it in Obsidian: "Open folder as vault"

# 3. turn on Sync (Settings → Sync) or initialise git
cd ~/Brain && git init && git add -A && git commit -m "Initial brain"

# 4. write an index at the root so the next session knows where things are
```

**The root file that matters:** a `ROUTING.md` or `INDEX.md` at the top — one line per folder. An
agent that reads the index knows what exists without scanning the whole vault, which is the difference
between a two-second start and a two-minute one.

## Keeping it healthy

- **Weekly:** anything in `00-Inbox/` older than a week gets filed or deleted
- **Monthly:** projects untouched for 90 days move to `99-Archive/` — not deleted, just out of the way
- **Never:** let the inbox become a second project folder. That's how a brain becomes a dump.
