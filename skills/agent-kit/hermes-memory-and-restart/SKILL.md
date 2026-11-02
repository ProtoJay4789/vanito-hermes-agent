---
name: hermes-memory-and-restart
description: Use when memory fills, restarts lose context, or a session forgets.
---

# Hermes memory, and surviving a restart

**Written from a fleet that lost work to each of these.** Every rule below is a failure that actually
happened, not a precaution.

---

## Memory is frozen when the session starts

**Your memory files (`MEMORY.md`, `USER.md`) are read once, at session start, and injected into every
turn from a snapshot.** Editing them mid-session does **not** change what you are currently seeing.

**⚠️ The practical consequence:** at 2pm you may be honouring a memory entry that you deleted at 11am.
The injected text is a photograph, not a live feed.

**`/new` is the only thing that refreshes it — and only the user can send it.** You cannot refresh your
own memory by writing to the file and continuing.

**So:** when a memory fact is wrong and the user cares, **say it is corrected on disk and will take
effect next session.** Do not claim the correction is live.

## The character budget is hard, and it counts

Memory has a fixed character budget, checked **only on the final state** of a batch. Two consequences:

- **Batch your changes.** Add + remove in ONE call — the limit is evaluated after the whole batch, so a
  batch that frees space and adds a fact succeeds where an add alone would be rejected.
- **When it fills, cut — don't skip.** Write cut lines to a vault archive, not to `/dev/null`. The
  archive is the point: *"memory is the buffer, the vault is the system of record."*

**What belongs in memory:** facts that apply to *every* session — who the user is, environment facts,
standing conventions with no task home.
**What does not:** procedures (skills), task progress (session history), anything re-discoverable.

## A restart is not a memory event — know what actually reloads

| what | survives a restart? |
|---|---|
| memory files on disk | ✅ yes, and they are re-read |
| config.yaml | ✅ yes — **but see the ownership trap below** |
| skills on disk | ✅ yes |
| an in-flight turn | ❌ gone |
| your "current plan" | ❌ gone — write it down BEFORE restarting |

**⚠️ Write the plan down before a restart, even a small one.** An agent that restarts mid-task and
reconstructs its intent from memory usually reconstructs it wrong — it rebuilds the *summary* of the
work, not the *next step*.

## ⚠️ The config ownership trap

**A running process owns its config keys and will write its in-memory copy back over a hand edit.**
This is not theory — a fleet edited `fallback_providers` out of five config files, and four minutes
later all five were rewritten with the key restored plus an extra entry appended.

**Rules:**

1. **Never hand-edit a key a process manages.** Use the command that owns it (`hermes config set …`).
2. **`HERMES_HOME` selects the profile — `HERMES_PROFILE` does not.** A loop that sets `HERMES_PROFILE`
   and calls `hermes config set` writes to the SAME profile every time while reporting different ones.
   **Verify by reading the path the command prints, not by trusting the variable you set.**
3. **After any config change, re-read the file.** A successful write message is not a persisted value.

## Restarting safely

**Before:**
- write down what you were doing and what the next step is
- back up the files you changed, with a timestamp in the name
- check nothing is mid-turn

**After — measure, don't assume:**
- is the process actually new? (uptime / PID start time)
- does a real turn work? (not just "the port is open")
- does the **silently-broken** thing work? (the one that failed without an error before)
- re-read each config value you changed, from disk

**⚠️ The most informative signal after a restart is a thing that *should* have changed and didn't.** If
a config edit appears to have taken but the behaviour is identical, the process did not load it.

## Silent failure is the default failure mode

The pattern to watch for: **a plausible-looking value that resolves to nothing.** It does not throw. It
does not log. The feature just never works, and looks identical to a feature nobody used.

`stt: provider: nous` silenced every voice note on every seat for weeks. `image_gen: provider: nous`
left image generation with no provider at all. Neither produced a single error.

**So: validate against the schema (the source list), not against intuition. And test the credential —
a commented-out key matches a naive search exactly like a live one.**
