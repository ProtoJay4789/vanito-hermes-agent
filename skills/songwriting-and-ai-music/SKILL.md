---
name: songwriting-and-ai-music
description: Use alongside lyrics for song structure and Suno-style prompting.
---

# Songwriting and AI music prompting

Lyrics are the words. This is the **song** — arrangement, production, and how to ask a music model for
what you actually hear in your head.

## Structure that works

```
Intro      4–8 bars, sets the world. Often just one instrument.
Verse      8–16 bars, low energy, one idea
Pre-Chorus 4–8 bars, builds — the question the chorus answers
Chorus     8 bars, the emotional thesis, repeated
Verse 2    8–16, deepens rather than restates
Chorus     same words
Bridge     4–8, the turn: new angle, key change, or a confession
Final      8+ (chorus, often with a changed line)
```

**Most amateur songs fail at the pre-chorus** — they go verse → chorus with nothing turning the screw.
Add the lift.

## Energy, not just structure

A song holds attention by **contrast**. If every section is loud, nothing is loud.

```
quiet  →  build  →  loud  →  pull back  →  peak
```

**Map the energy before you write a line.** The arrangement follows the map.

## Prompting a music model (Suno and similar)

**Split the ask into two fields, and never mix them:**

**Style / genre field** — the sound:

```
industrial metal with J-rock vocals, distorted guitars, 808 sub, half-time drums,
female and male duet, screamed sections over clean melodic chorus, 140 BPM
```

**Lyrics field** — the words with structure tags (see `donna-ai-lyrics`).

**⚠️ Do not put mood instructions in the lyrics block.** The model will sing them.

### Style prompt checklist

```
GENRE          primary, plus one modifier (post-hardcore J-rock, not just "rock")
VOCALS         male / female / duet; clean / screamed / whispered
INSTRUMENTS    the two or three that define the sound
TEMPO          BPM, or a feel ("half-time", "driving")
PRODUCTION     era and texture ("wall of guitars", "lo-fi tape", "modern brick-wall master")
REFERENCE      a description, not a real artist name — some models reject real names
```

## Mixing languages

- **Note the language per section** in the lyrics field.
- **The emotional peak usually carries the language it was written in.** Don't translate a hook that
  works.
- **A model handed mixed-language lyrics will drift toward one language** unless each section is
  labelled.

## Judging a generation

```
① does the vocal delivery match the lyric's intent
② does the chorus land harder than the verse
③ does the arrangement have contrast, or is it one level throughout
④ is anything wrong — mispronunciation, a dropped section, a truncated ending
```

**Re-roll on a specific complaint.** "Make it better" produces a different song; "the chorus needs a
bigger lift" produces a corrected one.

## Pitfalls

- **Too many style words is the same as none** — the model averages them out.
- **A reference to a real artist may be refused.** Describe the sound instead.
- **Two different genres in the style field usually gives you a confused blend**, not a blend you chose.
- **Generated audio length is capped.** A four-minute song usually needs an arrangement strategy, not a
  longer prompt.
