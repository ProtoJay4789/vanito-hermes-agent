---
name: hermes-memory-and-restart
description: Use when memory fills, restarts lose context, or a session forgets.
---

# Hermes memory, and surviving a restart

Every rule here is a failure that actually happened, not a precaution.

## Memory is frozen when the session starts

Your memory files are read **once**, at session start, and injected into every turn from that
snapshot. **Editing them mid-session does not change what you are currently seeing.**

**The consequence:** at 2pm you may be honouring an entry you deleted at 11am. The injected text is a
photograph, not a live feed.

**Only `/new` refreshes it — and only the user can send it.** You cannot refresh your own memory by
writing to the file and continuing.

**So:** when a memory fact is wrong and it matters, **say it is corrected on disk and will take effect
next session.** Do not claim the correction is live.

## The character budget is hard

Memory has a fixed budget, checked **on the final state of a whole batch**.

- **Batch your changes.** Add + remove in ONE call — the limit is evaluated after the batch, so a
  batch that frees space and adds a fact succeeds where an add alone is rejected.
- **When it fills, cut — don't skip.** Archive cut lines to a file rather than deleting them. Memory is
  a buffer; the archive is the record.

**What belongs in memory:** facts that apply to *every* session — who the user is, environment facts,
standing conventions.
**What does not:** procedures (skills), task progress (session history), anything re-discoverable.

## What survives a restart

| thing | survives? |
|---|---|
| memory files on disk | ✅ re-read at start |
| config.yaml | ✅ — **but see the ownership trap** |
| skills on disk | ✅ |
| an in-flight turn | ❌ gone |
| your current plan | ❌ gone — write it down first |

**⚠️ Write the plan down before a restart, even a small one.** An agent that restarts mid-task and
rebuilds its intent from memory usually rebuilds the *summary* of the work, not the *next step*.

## The config ownership trap

**A running process owns its config keys and will write its in-memory copy back over a hand edit.**

**Rules:**

1. **Never hand-edit a key a process manages.** Use the command that owns it (`hermes config set …`).
2. **`HERMES_HOME` selects the profile — `HERMES_PROFILE` does not.** A loop that sets
   `HERMES_PROFILE` and calls `hermes config set` writes to the **same** profile every time while
   reporting different ones. **Verify by reading the path the command prints**, not the variable you
   set.
3. **After any config change, re-read the file.** A success message is not a persisted value.

## Restarting safely

**Before**
- write down what you were doing and what the next step is
- back up the files you changed, with a timestamp in the name
- check nothing is mid-turn

**After — measure, don't assume**
- is the process actually new? (uptime / PID start time)
- does a real turn work? (not just "the port is open")
- does the thing that used to **fail silently** work?
- re-read each config value you changed, from disk

**⚠️ The most informative signal after a restart is something that *should* have changed and didn't.**
If a config edit appears to have taken but behaviour is identical, the process didn't load it.

## Silent failure is the default failure mode

The pattern: **a plausible-looking value that resolves to nothing.** It does not throw. It does not
log. The feature simply never works — and looks identical to a feature nobody used.

An invalid speech-to-text provider silenced every voice note for weeks. An image provider that didn't
exist left image generation dead. Neither produced an error.

**So: validate against the schema's own list, not against intuition. And test the credential — a
commented-out key matches a naive search exactly like a live one.**
