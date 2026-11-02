# Vanito's Hermes agent — install package

**Built 2026-11-01.** Everything an agent needs to be *his* agent, in one folder: the soul, the
skills, and the memories.

```
Vanito-Hermes-Agent/
  SOUL.md              who the agent is, how it talks, what it refuses
  skills/              13 skills — his 7, plus 6 that fit his work
  memories/
    MEMORY.md          his world: characters, catalog, gaming, travel, media lanes
    USER.md            who he is and how he wants to be worked with
  manifest.json        provenance + hashes
  README.md            this file
```

---

## Install — three ways, pick one

### A · A fresh Hermes profile on a machine you control

```bash
# 1. create the profile
hermes profile create vanito

# 2. copy the soul and the memories in
cp SOUL.md            ~/.hermes/profiles/vanito/SOUL.md
cp memories/MEMORY.md ~/.hermes/profiles/vanito/MEMORY.md
cp memories/USER.md   ~/.hermes/profiles/vanito/USER.md

# 3. drop the skills in
cp -r skills/* ~/.hermes/profiles/vanito/skills/

# 4. run it
hermes --profile vanito
```

### B · Add to an existing Hermes install

```bash
cp -r skills/*     ~/.hermes/skills/
cp SOUL.md         ~/.hermes/SOUL.md          # ⚠️ overwrites — back up first
cp memories/*.md   ~/.hermes/memories/        # ⚠️ same
```

**⚠️ Check before overwriting.** If that install already has a soul or memories, merge instead of
replacing — the only files here that are *his* are the memories, and a replaced soul loses a working
agent's behaviour.

### C · Read it into any agent by hand

Every file is plain markdown. **`SOUL.md` → the system prompt. `memories/*` → the memory store.
`skills/*/SKILL.md` → the skill library.** No tooling required.

---

## What's in the skills folder

**His seven** — carried from the vault, unchanged:

| skill | what it does |
|---|---|
| `lyrics-writing` | song structure, chorus craft, genre conventions, his evolving preferences |
| `content/donna-ai-lyrics` | formats lyrics for Donna AI / Suno with the right bracket tags |
| `film-production-workflow` | end-to-end Seedance 2.0 music video — keyframes, clips, concat, review |
| `creative-production/character-driven-music-video` | consistent AI-generated characters across a video |
| `gaming/heroscape-rules` | Heroscape rules and army building |
| `gentech-hub/hub-audit` | keeps his dashboards fresh and catches broken HTML |
| `gentech/gentech-family-hub-management` | managing family hubs with strict data isolation |

**Six added** from the shared library because they fit the same work:

| skill | why |
|---|---|
| `film-production/seedance-video-production` | the current Seedance production procedure |
| `creative/songwriting-and-ai-music` | craft + Suno prompting alongside the lyric formatter |
| `creative/image-generation-workflow` | reliable image generation and editing |
| `creative/brand-assets` | logos, banners, thumbnails for tracks and channels |
| `media/voice-content-production` | voice audio across personas — useful for HIKARI/KAGE |
| `creative/meme-generation` | real meme images from templates |

---

## ⚠️ Two things to confirm before this goes to a third party

**2 · The Girlfriend / family details are personal.** They're in `MEMORY.md` because they keep image
work faithful. **If this package is going anywhere you don't fully control, strip that section.** The
agent will still work.

---

## ⚠️ Identity — settled 2026-11-01

**Vanito is a separate person from Jordan.** Jordan's words: *"Me and Venito are not the same person.
He's somebody that I added to the group as a collaborator… He was in the entertainment group most of
the time, so most of everything he's done is in there."*

**The vault profile note previously said they were the same person. That was wrong, it has been
corrected, and nothing in this package should merge their identities, their work, or their folders.**

---

## What this package deliberately does NOT contain

- **No credentials, no keys, no tokens.** Nothing here authenticates to anything.
- **No vault.** The GenTech brain holds credentials and private handoffs and never travels.
- **No cron jobs, no agent peers.** A personal agent starts with an empty schedule on purpose —
  nothing fires while you're not watching.

## Provenance

`manifest.json` records, for every skill, where it came from and its size, so a later audit can tell
what was packaged and when.
