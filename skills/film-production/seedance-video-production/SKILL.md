---
name: seedance-video-production
description: Use when generating Seedance 2.0 video clips.
tags: [video, seedance, clawrouter, character-locking]
---

# Seedance 2.0 Video Production

## Quick Reference

| Property | Value |
|----------|-------|
| **Model** | `bytedance/seedance-2.0` |
| **Lane (default)** | BlockRun — `VideoClient`, endpoint `https://blockrun.ai/api`. **Reachable from the agent container.** |
| **Lane (host-only)** | `POST http://127.0.0.1:8402/v1/videos/generations` (ClawRouter). ⚠️ **A HOST service — the agent's container cannot reach it.** |
| **Auth** | BlockRun: `privateKey` passed explicitly, funded key at `/opt/data/.blockrun/.session`. ClawRouter: `Authorization: Bearer hermes-plugin`. |
| **Cost** | ~$0.98/clip on BlockRun (measured, two clips: 3.0295 → 1.0662 USDC). The 8402 lane quoted ~$1.14. |
| **Verified output** | 5 s · 720×1280 (9:16) · 24 fps · 121 frames · h264 |
| **Audio** | Video-only — NO audio tracks. Mix separately with ffmpeg. |

## Which lane — probe reachability BEFORE authoring the job

**Write the job for a lane you have already proved you can call from where you are sitting.** A job
written against an unreachable endpoint looks complete and cannot run; the failure surfaces only after
the user has been handed a command to paste.

- **BlockRun is the default and the reachable one.** `https://blockrun.ai/api` answers from inside the
  container, so the whole job runs without a host command.
- **The ClawRouter proxy on `127.0.0.1:8402` is a host service.** From a container, `127.0.0.1` is the
  container itself. Probe `127.0.0.1`, the docker gateway IPs, and `host.docker.internal` before
  assuming a localhost media proxy is usable — if all are closed, the job needs a host-side command
  and that is a different hand-off.
- **Prefer the reachable HTTP lane over a nicer-sounding local one.** An endpoint you can call beats an
  endpoint with better specs.

## Standing Model Rules (Aug 2026)
- **Images:** GPT Image 2 ONLY via ClawRouter or BlockRun MCP. ~$0.06-0.13.
- **Video:** Seedance 2.0 ONLY. Never switch unless Vanito says so.

## Seedance 2.5 vs 2.0 (Aug 2026 — 2.0 remains default for KAGE)
2.5 is live (Aug 7, 2026) but NOT a drop-in upgrade for character combat. 2.5 gains: up to 30s single-pass (2.0 caps at 15s), larger reference budget (30 img / 10 vid / 10 audio vs 2.0's 9/3/3), timestamp-level editing + green-screen/R2V control.
**Why STAY on 2.0 for KAGE:** independent fighting tests show 2.5 gets blurry during fast motion — a direct problem for gunfights, flips, kicks. 2.0 preserves the dark gothic digital painting style on short stitched clips, is predictable, and is already wired through ClawRouter. Route short action/combat clips → 2.0; only test 2.5 for a single continuous 30s scene or when stitching creates a continuity break. Do NOT switch the KAGE pipeline to 2.5 without an explicit user OK.

## Submission Pattern
Always `cat > file <<'ENDJSON'` — inline JSON in curl breaks on shell quoting:
```bash
cat > /tmp/payload.json << 'ENDJSON'
{"model":"bytedance/seedance-2.0","duration":10,"resolution":"720p","prompt":"...","image_url":"https://...","aspect_ratio":"16:9"}
ENDJSON
curl -s -X POST http://127.0.0.1:8402/v1/videos/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer hermes-plugin" \
  -d @/tmp/payload.json > /tmp/response.json
```

## Character Locking
Character degrades during action because the keyframe shows one pose but the action needs different poses → model guesses → character morphs.

**Fix A: Action Reference Sheets.** Generate multi-pose sheet via GPT Image 2 (3-5 poses, 1536×1024, ~$0.13). Use as `image_url` seed.
**Fix B: Locked description in every prompt.** Paste full character visual description into every Seedance prompt.

For multi-character scenes, generate matching sheets for each character type.

## The 7-section prompt (harvested 2026-09-25 — sections marked ★ each fixed a failure that cost a full run)

Structure, in this order. Keep it even when the prompt is short.

1. **REFERENCE ROLES** — say what each reference governs **and what it does not**. Bind by index:
   `@image1` = identity only (face, hair, costume); `@image2` = board, staging only. A reference with
   no stated boundary gets used for everything.
2. **★ GUIDES ARE NOT CONTENT** — **a prohibition does not work here; a definition does.** Three runs
   carrying *"no arrows, symbols or annotation marks of any kind in frame"* still rendered a drawn
   arrow into the delivery; deleting the clause changed only the arrow's colour. What worked was
   telling the model what the marks **are**: *"frame borders, panel numbers, caption text and motion
   arrows are planning guides drawn on paper by the director. They describe the shot, they are not
   objects in the world. None of them exists in the scene."* Keep both halves.
3. **STARTING STATE** — one sentence of place, one of opening pose.
4. **★ TIMELINE** — one entry per beat, each carrying **all five**: timecode, `Panel N`, shot size +
   angle + lens/move, **entry pose**, **exit pose**. Each beat begins in the pose the last ended.
5. **CAMERA** — real rigs only (dolly/tripod); no drift where static is specified; fix motion blur
   with a shutter ("consistent with a 1/48 second shutter on 35mm film").
6. **MUST REMAIN UNCHANGED** — identity restated as invariants **plus the anatomy count** ("two arms,
   two legs, five fingers on each hand in every frame"). Cheap, catches the most common failure.
7. **CONSTRAINTS** — only failures the timeline cannot express (duplicated limbs, feet sliding
   without weight transfer, teleporting, watermarks, slow motion, freeze frames).

**Write action as physics, not adjectives.** Name approach → contact → transfer of force → recovery,
and give soft matter its **lag**: sleeves settling a beat after the arms stop, dust falling back after
heel contact. Measured: these render correctly from prose alone — **which is why motion never needs a
drawn arrow.**

**Audio asks — name the SURFACES, never the instrument.** *"Diegetic sound only: heel taps on the
boards, fabric, breath, room tone. No music, no score."* delivered clean audio; the same line **with
"a distant guitar" added failed the whole job on `OutputAudioSensitiveContentDetected`** — naming an
instrument invites music synthesis and can trip output-audio copyright. (Our 2.0 path is video-only,
so this bites only on audio-capable lanes.) `generateAudio: false` is the fallback *after* a
refusal, never the default.

## Board craft — draw the board LIGHT (measured, and counter-intuitive)

The board is a plan; **a board drawn as a competing picture gets treated as one.**

- A **photoreal** board leaked its **panel numeral into 6 of 8 shots** with the prompt held
  byte-identical. A light pencil board leaked none.
- A **heavily drawn** pencil board — full environment, dark solid arrows — **still rendered its
  arrows into the delivery as real arrows in shot**; three runs, no prompt clause suppressed them.
  Redrawn light with the same arrows in the same places → **48/48 frames clean.**
- So: thin pale pencil, bare white paper, setting in two or three quick lines, no heavy shading, no
  rendered environment. **Markings are safe on a light board, including motion arrows** — draw them
  broad, soft, feathered. **Numbering and captions have never transferred: 0 leaks in 93 chances.**
- Give **each character its own plate**, never a combined sheet — the prompt binds references by
  index, so `@image1` is fighter A and `@image2` is fighter B, each unambiguous. (2.5 takes up to 30
  reference images.)

## Consistency — the durability ladder

Holds come from what you feed the model, in **rising durability**: prompt baseline → reference images
→ style reference → control map → trained model.

- **Seeds do not solve consistency.** A seed reproduces one generation and transfers nothing. Leave
  it unset across a set; set one only to re-roll a single unchanged prompt.
- **Baseline-plus-delta:** write out everything that must **not** change, put the single change in a
  final clause, keep the baseline **byte-identical** between runs. **Look at the baseline first** —
  you cannot enumerate what you have not seen. Never write "same as before": there is no memory
  between calls.
- **⚠️ Never chain output → output.** Drift compounds. Our "sequential chain" strategy buys cinematic
  flow by degrading the character — that is a workaround, not the fix. **Anchor every clip to the
  same approved baseline** and use the chain only when continuity genuinely demands it.
- **Never put a turnaround sheet in a reference slot for video** — it animates the grid. Use
  individual neutral-pose shots; a scene still carries its props and pose into the slot.
- **Judge identity across the WHOLE clip, not one frame.** A clip's defect appeared at 11.0s; a
  one-frame-per-shot check read clean at 10.5s. Sample **~2 frames/second** before shipping. A named
  part gained/lost/recoloured/reshaped = drift; a part that **moves with the action** (a cloak
  swinging, an antenna tilting) = animation, and passes.

## Clip Generation Strategies

**Parallel (same keyframe):** Fast but jumps back to static image. User rejects.
**Sequential chain:** Each clip feeds its last frame as seed for next. Cinematic flow but character degrades.
**Action sheets + chain (BEST):** Use action sheets for first 2-3 clips, then sequential chain.

Extract last frame:
```bash
ffmpeg -y -sseof -1 -i clipN.mp4 -vframes 1 frameN.png
cp frameN.png /var/www/gentechlabs/frameN.png && chmod 644 /var/www/gentechlabs/frameN.png
```

## Audio Mixing
Seedance returns video-only. Mix audio separately:
```bash
ffmpeg -y -ss 30 -i "track.mp3" -t 30 -c:a libmp3lame -b:a 192k \
  -af "afade=t=in:d=1,afade=t=out:st=28:d=2" audio.mp3
# VERIFY: if max_volume is -91 dB, trim captured silence → re-extract
ffmpeg -i audio.mp3 -af "volumedetect" -f null /dev/null 2>&1 | grep max_volume
# Stitch + mix
ffmpeg -y -f concat -safe 0 -i concat.txt -c copy video-only.mp4
ffmpeg -y -i video-only.mp4 -i audio.mp3 -c:v copy -c:a aac -b:a 192k -shortest \
  -map 0:v:0 -map 1:a:0 final.mp4
```

## Wallet
- **Funded wallet: `/opt/data/.blockrun/.session`** (`0xc0A1f229…4a0`, USDC on Base). Clients take the
  key explicitly — `new VideoClient({ privateKey })`. ⚠️ The MCP's own
  `profiles/<seat>/home/.blockrun/.session` is a **different, empty** account; a funded path in one is not
  a funded path in the other.
- Read the balance on-chain rather than trusting a cached figure — some builds' `getBalance()` is broken:
  `eth_call` `balanceOf` on USDC `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` (6 dp) via `https://mainnet.base.org`.
- ClawRouter caches balance at startup. After funding that lane: `sudo systemctl restart clawrouter-proxy.service`
- Payment failure + "execution reverted" = insufficient USDC (~$1/clip needed)
- ⚠️ **Stranded, do not fund:** `0xebc8c71970EEb6973bd87F1FF146B3Ec4a5972f8` (old skill address, ~0.49 left).

## Dimensions
| Keyframe | Seedance Output | Result |
|----------|----------------|--------|
| 1024×1024 | 1280×720 | ❌ Stretched |
| 1536×1024 | 1280×720 | ✅ OK |

## BlockRun VideoClient — the default path (measured)

```js
const { VideoClient } = require("/opt/data/tools/blockrun/node_modules/@blockrun/llm");
const key = fs.readFileSync("/opt/data/.blockrun/.session", "utf8").trim();
const vc  = new VideoClient({ privateKey: key, timeout: 300000 });
const r   = await vc.generate(PROMPT, {
  model: "bytedance/seedance-2.0",   // also -2.0-fast, -2.0-mini, 1.5-pro, 2.5
  imageUrl: PUBLIC_STILL_URL,        // image-to-video
  inputType: "image",
  durationSeconds: 5, aspectRatio: "9:16", resolution: "720p",
  budgetMs: 260000,
});
// r.data[0].url, r.data[0].duration_seconds, r.txHash
```

- **The video API REJECTS unknown parameters and NAMES them**: `400 {"message":"unsupported parameter:
  output_format"}`. Send the minimal set above and add fields back only where a rejection tells you the
  field is accepted. Do not speculatively pass `outputFormat`, `generateAudio`, `cameraFixed`.
- **`imageUrl` must be a public URL** — BlockRun fetches it remotely, exactly like FAL. A local path fails.
- **⚠️ "Real-person likeness is not supported on BlockRun."** Verbatim from the video API docs, beside
  `realFaceAssetId`. The lane serves original characters; it will not carry a specific real person.
- **BlockRun's terms push content policy down to the model provider** ("Those providers set their own
  terms… their behaviour is outside our control"), so a refusal here is usually ByteDance's, not the
  gateway's. Changing gateways therefore does not change the refusal.

## ⚠️ Synthetic likeness of a real person — the operating rule

**Do not generate a synthetic likeness of a real, identifiable person — still or moving — without that
person's documented consent.** Stated once here so it is applied, not rediscovered.

**"Likeness" = the face, and the face *and body moving together*. It does NOT mean an outfit, a pose, a
palette, a mood, or a kind of light. Style is not identity.**

| the ask | the answer |
|---|---|
| "same outfit / same pose / same setting / same mood" | **build it** — wardrobe, framing and light are styling |
| "make it look like HER" / "keep her face and body the same" | **decline**, then immediately offer the styling version |
| "she said it's fine" from a third party | **not consent** — it is unverifiable and about someone absent |

**No model choice changes this.** An uncensored checkpoint, a local GPU, a self-hosted pipeline — a tool
that *will* do something is not a permission to do it. Say plainly that the uncensored lane removes the
vendor's refusal and replaces it with nobody.

**Three reasons that hold even when the user is sincere, and worth giving once:**
1. **Consent must come from the person whose face it is** — a third party's assurance is not it.
2. **The file outlives the intent** — private is private only until it is forwarded, synced or lost, and
   a synthetic likeness cannot be recalled or distinguished from a real recording.
3. **One photograph cannot support it technically** — moving a face seen once makes the model INVENT the
   profile, the jaw, the far side of the head. The result is uncanny: identifiable enough to harm, wrong
   enough to insult. This is the argument that lands with a maker, because it is about quality.

**Deliver the refusal ONCE, in two or three lines, then move to the alternative in the same message.**
Re-explaining it every turn reads as moralising and burns the session; the request is usually warm and the
intent generous — refuse the artifact, keep the relationship. Point to the standing written rule
(`00-HQ/operating-rule-synthetic-likeness-<year>.md`) rather than re-arguing from scratch.

**Offer the substitution concretely** — "same wardrobe, same motion, same light, an original character,\n5 s" — because a styling-and-motion clip of an invented character is genuinely a nice artifact, and it is\nthe version that can be handed to the person it was made for.

## The keyframe still comes first — and its aspect ratio is a trap

For image-to-video, generate the seed still **before** committing to a clip, and generate it at the
video's aspect ratio.

- **Image models default to 1024×1024 SQUARE even when you ask for portrait.** An `aspectRatio` hint is
  advisory on several models (`bytedance/seedream-5-pro`, `google/nano-banana*`). **Check the dimensions
  with `ffprobe` and regenerate if wrong** — a square keyframe into a 9:16 job produces a stretched or
  cropped clip, and you pay for the video before you see the defect.
- **Regenerate rather than crop.** Cropping a square to portrait throws away the framing you composed;
  a fresh still costs a few cents.
- **Show the still to the user before spending on the video.** Wardrobe and pose are exactly the parts a
  person most wants to correct, and correcting them is free at the still stage and expensive after.
- BlockRun `ImageClient` (same key, same lane as video):

```js
const { ImageClient } = require("/opt/data/tools/blockrun/node_modules/@blockrun/llm");
const ic = new ImageClient({ privateKey: key, timeout: 240000 });
const r  = await ic.generate(prompt, { model: "bytedance/seedream-5-pro", aspectRatio: "9:16" });
// r.data[0].url — then verify dimensions with ffprobe before using it as imageUrl
```

Model prices measured on this lane (`GET /v1/images/models`): `zai/cogview-4` $0.015 · `gpt-image-1`
$0.02 · `grok-imagine-image` $0.02 · `seedream-5-pro` $0.045 · `nano-banana` $0.05 · `gpt-image-2.5-*`
$0.28. Same model family for still and clip keeps the painted style consistent across the pair.

## Verify the clip, do not trust the response

"5 seconds returned" is not "5 seconds of motion". Check the artifact:

```bash
ffprobe -v error -show_entries stream=codec_name,width,height,nb_frames \
        -show_entries format=duration -of default=noprint_wrappers=1 clip.mp4
```

Then hash 3–4 sampled frames and require they DIFFER — a held still packaged as a clip is byte-identical
across samples. Decode frames with ffmpeg inside Python (`-f image2pipe -vcodec png -`) and hash the
stdout; do **not** build the verdict inside a shell one-liner, where a failed substitution silently
lands in the negative branch.

## Other quirks
- `client.edit()` → CDN URLs; `client.generate()` → data URLs (base64, save via decode)
- `client.getBalance()` broken — use `node bal.mjs`
