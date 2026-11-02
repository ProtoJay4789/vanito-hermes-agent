# Vanito's Hermes agent

**Everything his agent needs, in one place.** The soul, the memories, and the skills — so he doesn't
start over.

> **This repo is temporary.** Read it, copy what you need into your own setup, then it gets deleted.

---

## Read in this order

| # | file | what it is |
|---|---|---|
| 1 | `SOUL.md` | who the agent is, how it talks, what it refuses |
| 2 | `memories/USER.md` | who you are and how you want to be worked with |
| 3 | `memories/MEMORY.md` | your world — characters, catalogue, gaming, travel |
| 4 | `skills/` | how to do each kind of work |

---

## Install

```bash
mkdir -p ~/.hermes/memories
cp SOUL.md            ~/.hermes/SOUL.md
cp memories/USER.md   ~/.hermes/memories/USER.md
cp memories/MEMORY.md ~/.hermes/memories/MEMORY.md
cp -r skills/*        ~/.hermes/skills/
```

Or on a dedicated profile:

```bash
hermes profile create vanito
cp SOUL.md         ~/.hermes/profiles/vanito/SOUL.md
cp memories/*.md   ~/.hermes/profiles/vanito/memories/
cp -r skills/*     ~/.hermes/profiles/vanito/skills/
hermes --profile vanito
```

**⚠️ If you already have a `SOUL.md` or memories, back them up first.** Those are the files that make
an agent feel like yours, and overwriting one loses a working personality.

---

## The skills

| skill | what it does |
|---|---|
| `lyrics-writing` | song structure, chorus craft, genre voice, the editing pass |
| `donna-ai-lyrics` | formats lyrics for Donna AI / Suno with the right bracket tags |
| `songwriting-and-ai-music` | song shape, energy mapping, Suno-style prompting |
| `character-driven-music-video` | keeping one character consistent across a whole video |
| `seedance-video-production` | Seedance clips via BlockRun — the measured procedure |
| `image-generation-workflow` | generating and judging an image without wasting re-rolls |
| `hermes-memory-and-restart` | **read this first if anything runs on a schedule** |
| `brain-setup` | setting up and syncing an Obsidian brain |
| `heroscape-rules` | Heroscape rules and army building |
| `travel-planning` | trips — flights, stays, itinerary, budget |

---

## ⚠️ Read this one before you build anything

**`skills/hermes-memory-and-restart`** — every rule in it is a failure that actually cost work:

- **Memory is a snapshot taken at session start.** An edit you make mid-session is not live until
  `/new`. Only the user can send it.
- **The memory budget is hard**, and it's checked on a whole batch — so free space and add the new
  fact in the same call.
- **A running process owns its config keys** and will write its in-memory copy back over a hand edit.
  Use the command that owns the key, never a text edit.
- **`HERMES_HOME` selects the profile — `HERMES_PROFILE` does not.** A loop using the wrong variable
  writes the same file while reporting different ones.
- **Restarting mid-task without writing the plan down** makes the agent rebuild the *summary* of the
  work, not the *next step*.

**And the rule underneath all of them:** a plausible-looking config value that resolves to nothing
does not throw and does not log. It just never works — and looks identical to a feature nobody used.
Validate against the schema's own list, and test credentials rather than reading them.

---

## What is not here

- **No credentials, keys or tokens.** Nothing here authenticates to anything.
- **No cron jobs, no agent peers.** Nothing fires while nobody's watching.
- **Nothing about anyone else.** This is one person's agent.

**Edit the memories to be true.** They're a starting point written from what was known — not a record
to preserve unchanged.
