# Live-only spec (possible)

This is not a product plan. It is the live path this repo might become: cheap glasses that invert the world in real time, and an AI whose *entire visual context* is that inverted stream.

The stills in `examples/` and the invert/normal switch in `example.html` are the frozen analogue. Live is the same shader, on a camera, on a face.

## What “live-only” means

- Invert happens **on the way to the eye**, every frame, not as a saved photo.
- The model is fed **the same inverted frames the wearer sees**.
- Discussion with the AI is grounded in that inverted picture. Not “I know this is really an orange cat, we inverted it.” The cat in context is the cat on the glass.
- Normal-colour photos are a debugging mode, not the working domain.

If that last point is wrong, the rest of the spec still works — but the experiment is weaker. The claim to test is: *shared inverted space changes what we talk about, and maybe how the model thinks.*

## Why invert at all

Wearer: ADHD / autism, migraine light sensitivity, high-importance days. Invert is a low-latency colour filter. It is not a camera for keepsakes. It is a view.

Model: maybe it “likes” inverted images. Hypothesis, not proven.

- Colour invert `(r,g,b) → (1−r, 1−g, 1−b)` keeps **edges, topology, count, pose**. It wrecks **colour priors** (sky-is-blue, cat-is-orange) that VLMs overfit.
- Off-the-shelf vision models were trained almost entirely on un-inverted photos. Inverted input is either a hard out-of-domain hit, or a useful push onto structure. We will not know until we run both.
- If wearer and model share the inverted frame, there is **no translation step** between “what I see” and “what the AI sees.” Context-discussion is one picture, not two.

Smart glasses later (chat on the lens, bone conduction — already sketched in the Claudius-stone notes). First hardware is cheaper: a viewfinder that only inverts.

## Split that must stay split

| Path | Budget | Job |
|---|---|---|
| **Display invert** | sub-frame, on device, GPU/NPU shader | what the eye gets |
| **AI on inverted frames** | can lag; cloud or on-device | pointing, talking, memory |

Never wait for the model to invert. Invert is optics (a shader). The model consumes an already-inverted buffer. Same rule as `example.html`: CSS `filter: invert(1)` is the live product in one still.

## DeepSeek paper as the training trajectory

Paper: **Lu, Ma, Chen, et al. (2026). *Thinking with Visual Primitives*.** DeepSeek + Peking University + Tsinghua. Official GitHub was published then pulled; clones and write-ups remain.

- [clone README](https://github.com/mitkox/Thinking-with-Visual-Primitives)
- [PDF (clone)](https://raw.githubusercontent.com/mitkox/Thinking-with-Visual-Primitives/main/Thinking_with_Visual_Primitives.pdf)
- [36Kr walkthrough](https://eu.36kr.com/en/p/3789208597372165)

```
@article{lu2026think,
  title={Thinking with Visual Primitives},
  author={Lu, Ruijie and Ma, Yiyang and Chen, Xiaokang and Luo, Lingxiao
          and Wu, Zhiyu and Pan, Zizheng and Liu, Xingchao and Lin, Yutong
          and Li, Hao and Liu, Wen and Hao, Zhewen and Gao, Xi
          and Nie, Shaoheng and Wei, Yixuan and Xie, Zhenda
          and Chen, Ting and Zeng, Gang},
  year={2026}
}
```

### What they actually claim

Multimodal CoT is still language. Language cannot *point*. They call that the **Reference Gap** (not the Perception Gap — more pixels do not fix “the one on the left”).

Fix: elevate **points** and **bounding boxes** to *minimal units of thought*. Interleave them in the reasoning trace, as vocabulary, not as a tool call:

```
I see a <|ref|>cat<|/ref|><|box|>[[370,334,408,497]]<|/box|>.
```

Boxes (`<|box|>`) for objects with extent. Points (`<|point|>`) for trajectories (maze steps, curve tracing). The finger stays on the picture while the sentence continues.

Training is **specialize, then unify**:

1. Multimodal pretrain — learn to emit the primitive format.
2. Split SFT — **FTwG** (box/grounding expert) and **FTwP** (point expert). Do not mix early.
3. Split RL (GRPO) — three reward heads: **format**, **quality**, **accuracy**.
4. Unified RFT — merge experts into one model.
5. On-policy distillation — student copies expert trajectories (KL).

Cold-start tasks: coarse/fine counting, spatial VQA, maze navigation (~17 pt gap vs GPT-5.4 on their maze set), path tracing. Compression is extreme (pixels → tens of KV entries) so pointing-in-CoT is cheap enough to run.

Limitations they admit: fine-grain scenes still fail, primitive mode is not auto-triggered, point-topology does not generalize to every spatial task.

### Why this is the inversion trajectory

**Coordinates do not move when you invert colour.** The cat’s box is the same box. The street’s vanishing point is the same point. Invert is a colour involution; spatial primitives are invariant.

So the DeepSeek pipeline is the training recipe with one domain rule: **every visual input is inverted**, matching the glasses.

| DeepSeek stage | Training in inversion |
|---|---|
| Pretrain primitive format | Same tokens, inverted images |
| FTwG (boxes) | Count / ground on inverted frames. Walk-in-town: box the cat, the pots, the person up the street |
| FTwP (points) | Trace inverted streets, kerbs, flock paths, maze-like alleys |
| GRPO rewards | Format + quality unchanged. Spatial accuracy is invert-invariant. **Colour-attribute accuracy is not** — drop or relabel (“blue shirt” after invert is not blue) |
| Unified RFT + distill | One student whose default world is inverted |

Colour-dependent fine-grained counting (DeepSeek’s “how many wearing blue”) is the task that *should* break. That is useful: it forces the model off the colour prior and onto the finger.

Maze / path / count-without-colour are the tasks that should transfer. Those are also the glasses tasks: *where is the cat, where does the street go, how many people ahead.*

Do **not** start from a frontier VLM and hope invert is “just augmentation.” Domain default is inverted. Normal is the ablation.

## Shared context (the actual experiment)

```
camera ──► invert shader ──► glasses (eye)
                 │
                 └──► inverted frame ──► VLM that thinks with primitives
                                              │
                                              └──► CoT with <|ref|><|box|> / <|point|>
                                                   spoken / shown to wearer
```

Rules for talk:

- Grounding is inverted. “That cat” means the cat as both of us see it on the glass.
- The model points (box/point) instead of saying “the thing on the left.”
- Colour words, if used, describe **inverted appearance**, or they are omitted. World-knowledge colour (“it’s really orange”) is a separate, explicit mode — off by default.
- Stills for later discussion are stored inverted, or stored as a pair (raw + invert) with invert as the conversation copy.

This is the difference. Most vision agents see the un-inverted world and translate. Here the conversation *is* the inverted world.

## Hardware, possible, cheap first

1. **Now:** `example.html` + stills. Proof the shader is the product.
2. **Phone / laptop webcam:** same invert, live, one button. Latency target: one frame.
3. **Cheap glasses:** camera in, inverted view out. No AI required for the filter to work. AI is a sidecar.
4. **Smart glasses:** inverted view + overlay of boxes/points + bone conduction. Same sidecar, now on the lens.

If step 3 does not help light-sensitivity / focus, stop. The model does not rescue a filter that the eye rejects.

## What this spec is not

- Not a VR social app.
- Not a photo camera (it could dump frames; that is not the job).
- Not “train a 284B DeepSeek replica.” The paper is the *trajectory* (primitives in CoT, split experts, invert as domain). The first student can be a small VLM.
- Not GitHub Pages, not a name (cam.ius / poi.cam still open).

## First measurements (when live exists)

1. Eye: does invert on a walk reduce glare / help focus vs normal, same street.
2. Model: same prompt on `walk-in-town.png` vs `walk-in-town-inverted.png` — does pointing stay on the cat. Coordinates should. Colour words should flip or vanish.
3. Joint: wearer and model discussing the inverted still, then the live street. If the talk needs a constant “actually it’s orange,” the inverted-domain rule failed.

## Open

- Invert in pixel space (this spec) vs invert in latent space after a normal encoder. Pixel invert is what the eye gets; start there.
- Whether off-the-shelf VLMs already “like” invert, or need the inverted-domain train.
- Whether colour-word mode (inverted appearance vs world-knowledge) should be a switch on the glasses, like invert/normal in `example.html`.
