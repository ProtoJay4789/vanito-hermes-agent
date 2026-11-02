# Vanito's Hermes agent

**A complete handoff package.** Everything an agent needs to be *his* agent: the soul, the skills, and
the memories — built from the GenTech fleet's real experience, so he starts where we are instead of
where we were a year ago.

> **Private repo.** Read it, copy what you need, then set up your own brain. Nothing here connects to
> anything; it is text and instructions.

---

## Read in this order

| # | file | why |
|---|---|---|
| 1 | `README.md` | this — what's here and how to install it |
| 2 | `SOUL.md` | who your agent is, how it talks, what it refuses |
| 3 | `memories/USER.md` | who you are, and how you want to be worked with |
| 4 | `memories/MEMORY.md` | your world: characters, catalog, gaming, travel, media lanes |
| 5 | `skills/` | everything your agent knows how to do |

## Install — fastest path

```bash
git clone https://github.com/ProtoJay4789/vanito-hermes-agent.git
cd vanito-hermes-agent

mkdir -p ~/.hermes/memories
cp SOUL.md            ~/.hermes/SOUL.md
cp memories/USER.md   ~/.hermes/memories/USER.md
cp memories/MEMORY.md ~/.hermes/memories/MEMORY.md
cp -r skills/*        ~/.hermes/skills/

hermes
```

Or, on a dedicated profile:

```bash
hermes profile create vanito
cp SOUL.md            ~/.hermes/profiles/vanito/SOUL.md
cp memories/*.md      ~/.hermes/profiles/vanito/memories/
cp -r skills/*        ~/.hermes/profiles/vanito/skills/
hermes --profile vanito
```

**⚠️ Back up first if you already have a `SOUL.md` or memories** — those are the files that make an
agent feel like *yours*, and overwriting one loses a working personality.

---

## The skills, by what they're for

### Your creative work

| skill | what it does |
|---|---|
| `lyrics-writing` | song structure, chorus craft, genre conventions |
| `content/donna-ai-lyrics` | formats lyrics for Donna AI / Suno with the right bracket tags |
| `creative/songwriting-and-ai-music` | songwriting craft + Suno prompting |
| `film-production-workflow` | end-to-end Seedance 2.0 music video |
| `film-production/seedance-video-production` | the current Seedance production procedure |
| `creative-production/character-driven-music-video` | consistent AI characters across a video |
| `creative/image-generation-workflow` | reliable image generation and editing |
| `creative/brand-assets` | logos, banners, thumbnails |
| `creative/meme-generation` | real meme images from templates |
| `media/voice-content-production` | voice audio across personas |

### Your world

| skill | what it does |
|---|---|
| `gaming/heroscape-rules` | Heroscape rules and army building |
| `gentech-hub/hub-audit` | keeps dashboards fresh, catches broken HTML |
| `gentech/gentech-family-hub-management` | managing family hubs with strict data isolation |

### ⚠️ The ones that will save you the most time

**These exist because we lost work to each of them. Read these before you build anything that runs on
a schedule, or hands off to another machine.**

| skill | the pitfall it prevents |
|---|---|
| `agent-kit/hermes-memory-and-restart` | **memory freezes at session start** (an edit you make mid-session is not live until `/new`); the memory budget is hard and checked on the whole batch; **a running process owns its config keys and will overwrite your hand edit**; `HERMES_HOME` — not `HERMES_PROFILE` — selects the profile; and restarting mid-task without writing the plan down means the agent rebuilds the *summary*, not the *next step*. |
| `agent-kit/fleet-self-reflection` | how to audit your own setup and find the faults no monitor catches — including the silent-failure class below. |
| `agent-kit/use-what-we-have` | check what's already installed before building or claiming it's missing. |
| `devops/safe-update-restart` | never blind-restart a running system. |
| `development/develop-and-verify` | verify the thing you built actually does the thing, and treat a roadblock as a lead. |

### The silent-failure rule, stated once

**A plausible-looking config value that resolves to nothing does not throw. It does not log. The
feature simply never works — and looks exactly like a feature nobody used.**

We ran with `stt: provider: <invalid>` for weeks: every voice note on every agent failed silently.
Same for image generation. Neither produced an error.

**So:** validate provider names against the schema's own list, and **test credentials rather than
reading them** — a commented-out key matches a naive search exactly like a live one.

---

## What this repo deliberately does NOT contain

- **No credentials, keys, or tokens.** Nothing here authenticates to anything.
- **No brain/vault.** A shared brain holds credentials and private notes; it never travels.
- **No cron jobs and no agent peers.** A personal agent should start with an empty schedule — nothing
  fires while nobody is watching.

## A note on the memory files

`memories/USER.md` and `memories/MEMORY.md` describe one person's world — characters, a catalog, family
details used to keep image work faithful. **Edit them to be true.** They are a starting point written
from what we knew, not a record that should be preserved unchanged.
