---
name: image-generation-workflow
description: Use when generating or editing an image with an AI image model.
---

# Image generation

Most failed image jobs fail before the model runs — a vague prompt, a private reference URL, or a
request the provider refuses. This is the order that avoids all three.

## Before you spend

```
1  WHAT IS IT FOR         thumbnail, album art, character sheet, meme — the answer changes the prompt
2  ASPECT RATIO FIRST     decide 1:1, 9:16, 16:9, 3:2. You cannot fix it later for free.
3  THE REFERENCE          if the image must match something, host it publicly and VERIFY the URL loads
4  THE PROMPT             7 parts, below
5  GENERATE ONE           judge it. Then decide whether to re-roll.
```

## The seven-part prompt

```
SUBJECT     the thing itself, with the details that must hold
ACTION      what it is doing, or its pose
FRAMING     close-up / medium / wide; what is cut off
LIGHTING    source, direction, quality
SETTING     where, and what is behind it
STYLE       medium, era, colour grade — "painterly", "photographic", "ink", "3D render"
NEGATIVES   what must not appear
```

**Fill every part.** A short prompt is not a safe prompt — it hands the model a free choice on
everything you left out, including the things you cared about.

## Real-person likeness — the hard line

**Do not generate a real person's likeness.** Providers refuse it, and the models that don't refuse
produce something worse than a failure.

**When the subject is a real person, change the frame:**

```
bangs over the eyes · chin over the shoulder · from behind · silhouette only · hands/boots in frame
```

**Only original, stylized characters for anything that shows a face.**

## Verification — describe what you actually see

**⚠️ A returned URL is not a finished image.** Open it and check:

```
① is the subject right
② is the framing what you asked for
③ is the aspect ratio correct
④ is anything wrong — extra fingers, garbled text, a second subject
⑤ would he accept this, or would he ask for a re-roll
```

**Say what's wrong plainly.** "It rendered" and "it's right" are different sentences, and only one of
them is useful.

## Cost discipline

- **Stills are cheap, video is not.** Iterate on stills; only animate an approved one.
- **One change per re-roll.** If you change the lighting *and* the pose *and* the framing, you learn
  nothing about which change helped.
- **Keep the prompt that worked.** A character sheet is useless if the prompt that produced it is lost.

## Pitfalls

- **Private reference URLs fail.** The model can't see localhost or a signed link that expires.
- **Text in image renders badly.** Add titles and captions in post.
- **"Photorealistic" and "a real person" are the same category** to a provider's safety check. If a
  prompt keeps getting refused, that's usually why — not a bad word.
- **Aspect ratio is not a crop.** Asking for a different ratio re-renders the whole composition.
