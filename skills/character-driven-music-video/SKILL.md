---
name: character-driven-music-video
description: Use when making a music video with AI-generated characters across shots.
---

# Character-driven music video

The hard part is not one good frame — it's the **same character** in frame twelve as in frame one. This
is the workflow that holds identity across a whole video.

## The rule that governs everything

**Never generate a character from a text prompt alone, twice.** Each generation invents a new face. The
whole method is: **lock the look once, then animate the locked look.**

## The pipeline

```
1  CHARACTER SHEET     one still, full description, approved by ear before you build on it
2  SHOT LIST           what happens, in order, one motion per clip
3  STILLS              one still per shot, all from the character sheet as reference
4  REVIEW              he looks at the stills BEFORE any video is generated
5  CLIPS               i2v: still + motion prompt → 5-second clip
6  CONCAT              clips joined in order with the audio
7  CHECK               watch the whole thing once, end to end, before calling it done
```

**⚠️ Step 4 is the one people skip and regret.** Stills cost pennies; clips cost ~$0.98 each. Fixing a
wrong look on a still is free. Fixing it across six clips is six re-rolls.

## Step 1 — the character sheet

Write the description once, in full, and reuse it **verbatim** in every later prompt. It should pin:

- **age range and build**
- **hair** — length, colour, how it falls, what covers it
- **eyes** — colour, and what surrounds them
- **outfit** — garment by garment, with colours and materials
- **silhouette** — what the figure looks like as a shape
- **distinguishing marks** — a specific colour rim, a piece of jewellery, a tattoo

**Keep it in a file.** A character sheet that exists only in a chat message gets lost, and the next
session reinvents the character.

## Step 2 — shot list

For each shot, one line:

```
SHOT 03 · close on hands · locked-off camera · practical neon from the left · 5s
```

**One motion per clip.** "She turns, walks, and sits" is three clips, not one.

## Step 3 — stills

Generate each still **from the character sheet**, using the approved sheet image as a reference where
the model supports it.

**Face handling for real-person likeness — always, without exception:**

```
bangs over the eyes → chin over the shoulder → from behind → silhouette → pure scenery
```

**This is not a stylistic choice.** Providers refuse real likeness, and a close-up of a real face is
either refused or comes back subtly wrong. Design the shot so the question never arises.

## Step 5 — clips

```
model        bytedance/seedance-2.0
duration     5
aspect       9:16 for vertical, 16:9 for horizontal — never mixed in one video
image_url    the approved still for this shot
prompt       motion only: camera move + subject action. Appearance is already in the still.
```

**⚠️ Do not re-describe the character in the motion prompt.** Two descriptions fight; the model splits
the difference and the face drifts.

## Step 6 — concat

Join in shot order, keep the audio track intact, and **match the cut to the beat**. A clip that runs
two frames long reads as a mistake, because it is one.

## Constraints you plan around, not discover

- **no tears · no lip-sync · no real bands · no real-person likeness**
- **No singing close-up.** Imply performance through the body — shoulders, hands, the back of the
  head — and cut away before the mouth matters.
- **Text in frame renders badly.** Put titles on in post, not in the prompt.

## Verification — watch the finished video once

```
① does the character look the same in shot 1 and shot 6
② does each clip contain the motion you asked for
③ are the cuts on the beat
④ is the audio in sync
⑤ does anything in frame contradict the character sheet
```

**A list of successful render URLs is not a finished video.** Watch it.
