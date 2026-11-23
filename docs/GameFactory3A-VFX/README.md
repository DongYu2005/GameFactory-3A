# Inverse VFX: Recovering Executable Effect Trees from Text and Video

<p align="center">
  <a href="#"><img src="https://img.shields.io/badge/arXiv-submitting-b31b1b?style=flat-square&logo=arxiv" alt="arXiv (submitting)" /></a>
</p>

<!-- TODO(before merge): upload the supplementary video in the GitHub web editor and paste the user-attachments URL below. -->
<table>
  <tr>
    <td width="100%"><video src="TODO_VIDEO_URL" width="100%" controls muted playsinline></video></td>
  </tr>
</table>

## Overview

Real-time visual effects (VFX) are everywhere in games and film, but generating them is still hard. Video generators output pixels that cannot be edited in an engine, and LLMs that write engine code face a vast interface in which errors often pass silently.

We cast effect generation as an inverse problem: given a text description and, optionally, frames sampled from a reference video, recover an **effect tree**. The tree is a closed-vocabulary program, and a deterministic executor compiles it into a native effect in Unreal Engine, Unity, or Godot.

This work has four parts:

- **Effect tree and executors.** The tree describes an effect as elements, each with a role, a temporal envelope, a spawn pattern, a motion and an appearance carrier, whose leaves are engine primitives and asset references. One executor per engine compiles the same tree file and reports unsupported nodes and missing assets instead of failing silently.
- **Data engine.** A render-in-the-loop sampler recombines 384 hand-reproduced tutorial effects into 14,064 paired trees, rendered videos and descriptions, with a quality gate on every render.
- **VFX-Bench.** 486 prompts in five categories (particles, meshes, simulated fluids, compositions, post-processing) with about 6,000 frozen checklist items. Every system delivers a 3 s video on the same stage, and the judges see only that video, so tree-writing models, coding agents, native agent products and video generators are compared on the same footing.
- **A learned tree writer.** An open LLM (Qwen3.5-9B + LoRA) is fine-tuned to write effect trees under a grammar constraint.

The same frontier agent, delivering the same prompts in three forms (VFX-Bench small set, 144 prompts, text condition):

| Agent · form | Checklist accuracy | Prompts delivered | Median minutes per prompt |
|---|---|---|---|
| Opus 5.5 · effect tree | 0.75 | **100%** | **3.5** |
| Opus 5.5 · UE code | 0.74 | 98% | 12.2 |
| Opus 5.5 · native agent | **0.77** | **100%** | 13.3 |
| gpt-6.1-sol · effect tree | **0.69** | **100%** | **2.4** |
| gpt-6.1-sol · UE code | 0.29 | 68% | 9.8 |
| gpt-6.1-sol · native agent | 0.57 | 90% | 10.0 |

Writing a tree matches the agent's own native result, or exceeds it, in about a quarter of the model time, and it delivers every prompt.

What fine-tuning adds to a 9B model:

| System | Element F1 on held-out effects | VFX-Bench checklist accuracy |
|---|---|---|
| Qwen3.5-9B without fine-tuning (3 examples, same grammar and assembly) | 0.15 | 0.03 |
| Nearest training tree (retrieval) | 0.50 | 0.08 |
| **Qwen3.5-9B + LoRA (ours)** | **0.67** | **0.17** |

Element F1 is measured on 315 rendered held-out trees; checklist accuracy on the 144-prompt small set. The tree structure is learnable by a small model, which still trails frontier agents (0.75) by a wide margin. The same 144 trees compiled by the UE, Unity and Godot executors score 0.19, 0.16 and 0.20 under one judge, with no significant difference between engines.

See the paper for the full tables, ablations, cross-engine details and the comparison with effect-video generation.

## What is in this repository

<!-- TODO(before merge): the Inverse VFX code is not in this repository yet. Fill in the locations once the release scope is decided. -->

| Part | Location |
|---|---|
| Effect-tree schema: elements, envelopes, spawn, motion, appearance carriers, closed asset vocabulary | TODO |
| Executors that compile a tree into a native effect and write a report: UE, Unity, Godot | TODO |
| Example effect trees with their renders | TODO |
| Tests | TODO |

The schema is what a tree writer reads. A tree lists elements; each element has a role, a temporal envelope (appearance, climax, sustain, dissipation), a spawn pattern, a motion and an appearance carrier, and its leaves are engine primitives and asset references such as `bank:glow_point{edge:soft}`. The executor resolves the references against the asset banks, compiles the tree, and records unsupported nodes and missing assets in its report.

## Quick start

<!-- TODO(before merge): replace the placeholders with the real entry point and paths. -->

1. Start from one of the example trees, or have an agent or the fine-tuned model write one for a text prompt.

2. Compile and render it with the UE executor (UE 5.7):

   ```bash
   TODO: executor command
   ```

   The output is the native effect, a 3 s render on the default stage, and the executor report.

3. Open the report. It should list no unsupported nodes or missing assets. Then look at the render; if something is wrong, edit the tree and run again. The same tree file compiles with the Unity and Godot executors.

## Benchmark and evaluation

<!-- TODO(before merge): add the dataset link if VFX-Bench is released. -->
VFX-Bench has 486 prompts in five main categories and 13 appearance groups, each with a frozen checklist of about twelve atomic items (about 6,000 in total). A system delivers a 3 s video under a shared stage and camera. Two judges from different vendors (qwen3.6-plus and MiniMax-M3) first describe the frames without the prompt, then mark each checklist item, and the two scores are averaged. Checklist accuracy counts undelivered prompts as zero, and executability is reported next to it. The main comparison uses a 144-prompt small set chosen by coverage rules. Held-out structure is measured by element F1 against ground-truth trees. The definitions are in the paper and its supplementary material.

Textures, meshes and effect packs obtained from third parties are used only for rendering and are not redistributed.

## Citation

```bibtex
@article{inversevfx2026,
  title   = {Inverse VFX: Recovering Executable Effect Trees from Text and Video},
  author  = {TODO},
  journal = {arXiv preprint arXiv:TODO},
  year    = {2026}
}
```

## License

Code: Apache 2.0, see the repository `LICENSE`. Third-party assets used for rendering are not covered and are not redistributed.
