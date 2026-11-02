---
name: seedance-video-production
description: Use when generating video clips with Seedance via BlockRun.
---

# Seedance video production

Seedance turns a text prompt — or a still image plus a prompt — into a short clip. This is the
procedure that actually produces usable footage.

## Two modes

| mode | input | use for |
|---|---|---|
| **text-to-video** | a prompt | establishing shots, motion, atmosphere |
| **image-to-video (i2v)** | a still + prompt | keeping a character consistent across shots |

**⚠️ i2v is the one that matters for character work.** A text prompt will give you a different face
every time. A still locks the look, and the prompt describes *motion*, not appearance.

## The call

```bash
curl -s -X POST https://blockrun.ai/api/v1/video/generations \
  -H "Authorization: Bearer $BLOCKRUN_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "bytedance/seedance-2.0",
    "prompt": "<your prompt>",
    "duration": 5,
    "aspect_ratio": "9:16",
    "image_url": "<public URL of your still>"
  }'
```

**The response returns a job; the clip comes back as a URL.** Fetch it and save it locally — do not
rely on the hosted URL persisting.

## Measured baseline (so you know what "correct" looks like)

```
model       bytedance/seedance-2.0
resolution  720 × 1280
frame rate  24 fps
frames      121
duration    5.04 s
cost        ≈ $0.98 per clip
```

**If your clip comes back materially different from that**, something changed — check the model name
and the duration before assuming the render was fine.

## Writing the prompt

A single sentence gives you a single image with drift. Seven sections give you a shot:

```
1. SUBJECT      who/what, with the details that must stay constant
2. ACTION       what happens across the five seconds — one continuous motion, not three
3. CAMERA       push in / pull out / orbit / handheld / locked off
4. LIGHTING     source, direction, quality (hard sun, soft window, practical neon)
5. SETTING      where, and what is in the background
6. STYLE        film stock, animation style, colour grade
7. NEGATIVES    what must NOT appear
```

**⚠️ One motion per clip.** A five-second clip that tries to do three things does none of them.

## Hard constraints — plan around them

- **No tears**
- **No lip-sync** — do not plan a singing close-up; imply the performance with the body and cut away
- **No real bands or real people**
- **Real-person likeness is not supported at all.** Original stylized characters only.

**These are provider limits, not preferences.** A prompt that violates them doesn't fail loudly — it
returns something worse than you asked for.

## Verify every clip — four checks

```
1. resolution   is it the size you asked for
2. frames       did you get the full duration or a truncated clip
3. motion       does the subject move the way you described
4. content      watch it — is anything wrong in it
```

**⚠️ A returned URL is not a finished clip.** Look at it before you build on it, and before you spend
on the next one.

## Cost discipline

- **Write the prompt carefully the first time.** A re-roll is another ~$0.98.
- **Generate the still first** (cheap), judge it, then animate. Animating a bad still wastes a clip.
- **Batch nothing blind.** Check the first clip before queuing five.

## Pitfalls

- **`duration` is capped** — asking for 10 seconds may quietly return 5, or reject with a confusing
  parameter error. Ask for what the model supports.
- **A private image URL will fail.** The still must be publicly reachable — host it and verify the URL
  loads before passing it in.
- **Aspect ratio is not free** — asking for 9:16 and 16:9 in the same batch is two different renders.
