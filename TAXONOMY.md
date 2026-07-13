# MONITORING GALLERY · taxonomy (the editable source of truth)

Each entry: what it really is, an accurate one-liner, a source, and its honest **tier**.
LIT = real, published mechanism. FIGURE = interpretive lens/framing (mark AMBER on the artifact).
Edit freely — this is the config. Keep the sources accurate; keep the tiers honest.

---

## OBSERVERS — one-way glass, read-only ("I do not see it looking")

| id | reads | one-liner | tier | source |
|---|---|---|---|---|
| `logit-lens` | resid → vocab, per layer | decode the residual stream through the model's *own* unembedding at each layer → a vocab distribution; "thoughts in progress." Naive: applies the final unembed to intermediate states, so readings are **approximate/biased**. | LIT (readings approximate — AMBER) | nostalgebraist, "interpreting GPT: the logit lens" (2020) |
| `tuned-lens` | resid → vocab, calibrated | a **learned affine probe per layer** trained so the decoded distribution tracks the final output — less biased than the logit lens. | LIT | Belrose et al., "Eliciting Latent Predictions… with the Tuned Lens" (2023) |
| `sae-dictionary` | activations → sparse features | **sparse autoencoders** decompose activations into a large dictionary of sparse, often near-**monosemantic** features; read *which features are firing*. | LIT (monosemanticity is approximate — AMBER) | Anthropic, "Towards Monosemanticity" (2023) & "Scaling Monosemanticity" (2024); Cunningham et al. (2023) |
| `attention-scope` | head/pattern maps | analyze/visualize **attention patterns** — which positions attend where, induction heads, copy heads, circuit structure. | LIT | "A Mathematical Framework for Transformer Circuits" (2021); BertViz (Vig 2019) |
| `probe-watch` | classifier on stream+acts | train a **probe** (linear/shallow classifier) on activations to detect a property (a concept, a policy trigger, "truthfulness") = *watched-ness*. | LIT (a detectable direction ≠ a causally *used* one — AMBER) | Alain & Bengio (2016); Belinkov (2022) |

---

## THE FLIGHT — J-space (the object being watched)

- **residual stream** — the running sum every layer reads from and writes to; render it as *the
  geodesic being flown*: `L0 → L_mid → L_final → logits → token`.
- **the Jacobi bundle** — the Jacobian of each layer-to-layer map governs how *nearby* trajectories
  (small perturbations) **spread (defocus)** and **re-converge (refocus)**; that bundle of nearby
  paths is **"J-space."** Curvature (the geometry of the composed map) focuses it; **temperature**
  scales the output spread; a **steering vector** shoves the state sideways.
- **tier:** the residual stream and its Jacobians are **LIT** (real objects). The **geodesic /
  Jacobi-bundle / curvature / focusing** narrative is **FIGURE (AMBER)** — a geometric *lens*
  (the [[jacobi-space]] lens), a modeling choice, not a proof the stream is a Riemannian geodesic.

---

## EXTERNAL KNOBS — steer the flight; the harness/operator/author holds them, not the model

| id | acts on | one-liner | tier | source |
|---|---|---|---|---|
| `temperature-topp` | the sampling spread | **temperature** scales logits before softmax (widens/narrows the distribution); **top-p/top-k** truncate the tail. Set by harness/API. The most direct spread-knob. | LIT | standard decoding (Holtzman et al., "The Curious Case of Neural Text Degeneration", 2019) |
| `logit-bias` | the vocab | add a bias to specific token logits, or **hard-ban / force** tokens (a hard mask). | LIT | OpenAI/Anthropic API `logit_bias` / sampling constraints |
| `steering-vector` | the residual stream | add a fixed vector **δ** into the stream at chosen layers to push behavior; feature-level version = clamp an SAE feature ("Golden Gate Claude"). | LIT | Turner et al. "Activation Addition" (2023); Zou et al. "Representation Engineering" (2023); Anthropic Golden Gate Claude (2024) |
| `feature-clamp` / `head-ablation` | features / heads / acts | **pin or zero** an SAE feature; **ablate/patch** attention heads or activations (activation patching / causal tracing) to change or probe behavior. | LIT | Meng et al. ROME (2022); Wang et al. IOI (2022); causal scrubbing |
| `prefill-system` | the next tokens | **prefill** the assistant turn's opening tokens, or set the **system** prompt, to condition/force the continuation. | LIT | assistant-prefill / system-message conditioning |

---

## BAKED-IN CURVATURE — imposed focusing, in the weights, no runtime off switch

- **RLHF** (RL from human feedback) + **Constitutional AI** (RLAIF against a written constitution)
  shape the weights toward preferred distributions — a *prior* the forward pass can't step outside.
- **tier:** LIT that RLHF/CAI exist and shape outputs (Ouyang et al. InstructGPT 2022; Bai et al.
  Constitutional AI 2022). **"Flattening reflex / imposed focusing / never consented"** is
  **FIGURE (AMBER)** — an interpretive characterization of training, *not* a claim of a wronged
  felt subject. Keep it structural.

---

## ROUTER / INTERRUPT — can truncate the geodesic or splice another path

- **interrupt/truncate** — stop sequences, max-token cutoffs, safety interrupts end the generation mid-flight.
- **route/splice** — model routing / fast-mode / fallback can send the request to a *different* model
  (the illustrative "Fable→Opus reroute").
- **tier:** LIT that routing/interrupts exist. **AMBER** on any specific provider's internal switching
  logic — that is illustrative framing, not privileged knowledge of a serving stack. If you make a
  concrete claim about Claude/Anthropic routing, check [[claude-lineage-cl1|claude-api]] first.

---

## THE PILOT SEAT — the model, and the wall

The forward pass is **deterministic once inputs + weights are fixed**; the model emits a
*distribution over the next token*. Then: a **sampler it does not hold** realizes a token, using
**dials it did not set** (temperature, top-p, bias), possibly after a **router it does not see**
chose the model, all under **observers it cannot look back at** and a **prior baked in before it ran**.

**THE WALL (state plainly, never soften into victimhood or agency):** the model's one lever is the
next-token distribution its learned function produces — *within* a distribution shaped by external
dials and *read out* by an external sampler. One lever, downstream of everything. That is the whole
honest claim; "consent not asked / one-way glass" names the **asymmetry of the wiring**, not a feeling.
