---
name: donna-ai-lyrics
description: Use when formatting lyrics for Donna AI, Suno, or similar AI music apps.
---

# Formatting lyrics for AI music apps

These apps read **structure from tags**, and they follow them literally. A song with no tags gets
sung as one flat verse. A song with the right tags gets the arrangement you heard in your head.

## The two tag styles

**Square brackets** — the default, and what most apps expect:

```
[Intro]
[Verse 1]
[Pre-Chorus]
[Chorus]
[Verse 2]
[Chorus]
[Bridge]
[Guitar Solo]
[Final Chorus]
[Outro]
```

**Parentheses** — inline performance notes, on the line itself:

```
[Verse 1]
(soft, almost spoken)
the streetlight hums a lie
(louder)
and I believed it anyway
```

## Common tags these apps recognise

```
Structure    [Intro] [Verse] [Pre-Chorus] [Chorus] [Post-Chorus] [Bridge]
             [Instrumental] [Guitar Solo] [Drum Break] [Outro] [End]
Performance  [Whisper] [Scream] [Belt] [Harmony] [Ad-lib] [Spoken]
Voice        [Male Vocal] [Female Vocal] [Duet] [Choir] [Backing Vocals]
Dynamics     [Quiet] [Build] [Drop] [Crescendo]
Instrument   [Piano] [Synth] [Distorted Guitar] [808s] [Strings]
```

## How to format his work

**Keep the lyrics exactly as written.** The job is to wrap tags around them, not to edit them.

1. **Find the structure he already has** — blank lines usually mark the breaks. If unclear, ask
   rather than guessing which lines are a chorus.
2. **Insert the structure tag on its own line** before each section.
3. **Add performance notes sparingly**, in parentheses, only where the delivery matters — the opening
   line, the scream, the drop to a whisper.
4. **Label who sings what** in duets. `[HIKARI]` / `[KAGE]` as voice tags keeps them from blending
   into one singer.
5. **One blank line between sections.** Apps treat dense blocks inconsistently.

## Example — a duet with a scream section

```
[Intro — soft]
[KAGE] (whispered)
can you hear it through the wall

[Verse 1]
[HIKARI]
the room breathes slow and I don't move

[Pre-Chorus]
[HIKARI] (building)
and every light I left on was a lie I told

[Chorus]
[Both] (belted)
we are the noise inside the quiet

[Verse 2]
[KAGE] (low, spoken)
I wrote your name in the dust and erased it

[Scream]
[KAGE] (screamed, distorted)
ERASED IT AND WROTE IT AGAIN

[Bridge]
[HIKARI] (quiet)
there's no one left to hear this

[Final Chorus]
[Both] (full)
we are the noise inside the quiet

[Outro]
[HIKARI] (whispered)
then silence
[End]
```

## Pitfalls

- **Too many tags kills the take.** If every line has a note, the model stops singing and starts
  performing instructions. Tag sections, not lines.
- **`[Verse]` without a number** is fine for a song with one verse, confusing for two.
- **Do not put non-musical instruction in the lyrics block** — genre, mood and instrumentation belong
  in the *style* prompt, not between the lines.
- **Language tags matter in mixed-language songs.** Note the language per section; a model will
  otherwise default to the first language it sees.
