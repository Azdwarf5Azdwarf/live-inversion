# Next steps (checkpoint)

Session note — where this stopped, so it's pickable back up later.

## Do first, no AI

- MVP: webcam + one button, live `filter: invert(1)` in the browser. No model.
- Wear it / look at it for a week before anything else. If it doesn't help
  focus or light-sensitivity, the rest of the spec doesn't matter.

## Testable today, still no training

- Invert 5-10 ordinary photos (screenshot through the invert filter, or the
  two buttons in `example.html`).
- Paste one into a general-purpose VLM (e.g. this chat) and ask about it.
- This checks whether an off-the-shelf model is already usable on inverted
  input, degraded or not, before any training work is justified.

## If training is ever pursued

- Data: don't shoot new images — take an existing box/point-annotated
  dataset (e.g. COCO) and invert the RGB. Coordinates don't move under
  invert, so labels stay valid; only recaption colour-dependent text.
- Compute: cloud only (rented A100/H100), not local hardware. Days-to-weeks
  per stage.
- Cheaper real path: skip training EVE from scratch — fine-tune an existing
  small open VLM (encoder-free if one exists cheaply) on inverted images
  instead. Same hypothesis, far less cost.
- Full recipe (EVE ingest + DeepSeek pointing) is in `docs/live-only.md`.

## Open question parked

- `docs/n5vqxk.md` — how the wearer learns a real-world colour without
  breaking the shared-inverted-space idea. Unresolved.

## State at checkpoint

Repo has no live/training code yet — everything above is still spec + two
stills (`example.html`, `examples/`). Next actual action, when picked back
up, is the invert-5-photos test above, not a build.
