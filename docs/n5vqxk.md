# Open question: how does the wearer learn a real-world colour?

Follows from [`live-only.md`](live-only.md) — specifically the open item
"Whether colour-word mode (inverted appearance vs world-knowledge) should be
a switch."

## The gap

The wearer only ever sees the inverted stream. If they ask the model "what
colour is this cat?", there are three possible answers, and none of them are
free:

1. **Model says the inverted-appearance colour** ("cyan"). True to the shared
   picture, useless as world information — the wearer already sees cyan.
2. **Model says the real-world colour** ("orange"). Useful, but it silently
   reinjects the un-inverted world into the wearer's head through language,
   which is exactly the translation step `live-only.md` says the spec exists
   to avoid. Do this often enough and the wearer is back to running two
   colour models at once (what I see vs. what it "really" is) — the thing
   invert-only was supposed to remove.
3. **A third option: an agreed code**, not a colour word — e.g. the model
   returns a fixed token/index for "this hue family" (`hue-17`) rather than
   "orange." Wearer and model share a private mapping instead of English
   colour names. This avoids re-teaching the wearer the un-inverted colour
   language, but it only helps if the wearer builds fluency in a code that
   has no other use outside this system — a real cost, not a free lunch.

## Why this matters for the live spec

`live-only.md` treats colour-word mode as a binary switch (inverted
appearance / world-knowledge, off by default). Real use will need a *reason*
to flip that switch — e.g. matching clothes, reading a traffic light, food
freshness — and each flip is a small defection from the "shared inverted
space" hypothesis the whole experiment is testing. Option 3 (coded answer)
is a possible middle ground: gives the wearer something actionable without
handing back full English colour vocabulary.

## Not resolved here

- Whether a private hue-code is learnable/worth it, or just a worse version
  of asking for the real colour.
- Whether traffic-light / safety-critical colour cases need to bypass the
  switch entirely (always answer in real-world colour, no debate).
- How often "what colour is this really" gets asked in practice — this is
  speculative until step 2 of `live-only.md` (webcam + live invert) exists
  and can be tested on.
