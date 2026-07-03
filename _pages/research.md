---
layout: archive
title: "Research"
permalink: /research/
author_profile: true
---

My research asks a simple question: **how do we make generative video models that respect physics — and how do we check that they actually do?** Most video models produce footage that looks plausible frame-by-frame but drifts physically over time: motion slides, contacts break, occlusions flicker, and errors accumulate over long horizons. I work on giving these models explicit physical structure to condition on, and on evaluation that separates genuine physical grounding from surface-level mimicry.

## Directions

### Physics-enhanced video generation
Turning physical structure — optical flow, motion trajectories, simulated point clouds, and reference video — into signals a generative model can directly consume, rather than relying on text prompts and appearance priors alone. The goal is video whose motion and contact behaviour stay consistent with the physical conditions it was given.

### Multimodal world models
Interactive, controllable world models that fuse visual, geometric, and language conditioning to predict how a scene evolves under actions. I am interested in models that are not just realistic but *steerable* and *inspectable*.

### Generative modeling &amp; distillation
Diffusion and flow-based models, and few-step distillation in particular. A recurring theme is the error accumulation that shows up when few-step samplers are reused across many steps, and how to keep low-step generation stable.

### Trustworthy evaluation
Evaluation protocols built around controlled conditions — zero / random / mismatched / real — to test whether a model truly uses the physical or acoustic evidence it is given, instead of guessing from priors. Good evaluation is, to me, as much a research contribution as a new model.

## Manuscripts

Several manuscripts are currently **under review**. To respect the anonymity requirements of double-blind venues, titles and authorship are not listed publicly during review. Details are available on request by [email](mailto:gzwsjtu98@sjtu.edu.cn), and accepted work will be listed here and on my [Google Scholar profile](https://scholar.google.com/citations?user=PS_CX0AAAAAJ).
