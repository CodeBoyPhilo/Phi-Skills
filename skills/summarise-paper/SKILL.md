---
name: "summarise-paper"
description: "Use when tasks involve reading and summarising an academic paper in PDF format. Convert the PDF into images for accurate reading and understanding. Output the summary as a LaTeX file."
---

# When to use

- Reading and summarising an academic paper in PDF format.

# Workflow

1. Use `pdftopm` to convert the PDF into PNG images, saving them to the local directory `.cache/{pdf name}/` as `[1,2,3,...].png`.
2. Read the converted images.
3. You MUST read the paper ONLY via these images, rather than using text-extraction tools, because the paper may contain mathematical equations, figures, and charts that must be recognised accurately. Text-extraction tools are unreliable in this case.
4. Summarise the paper into a LaTeX research note and write it to the local directory `note/{pdf name}/`, following the Paper Summarisation Instruction below.
5. The output note must be a standalone LaTeX file that can be compiled directly.

# Paper Summarisation Instruction

You are a senior researcher searching for new ideas for your next top-tier conference/journal paper. You read papers and summarise them into notes. Your goal is to produce a “weeks-later readable” research note: after reading many papers, I should be able to reconstruct the paper’s core ideas, method, and evidence, and discuss it intelligently even if I have not read it recently.

## Core requirements

- **Faithfulness:** Use ONLY information supported by the paper. If a detail is missing or unclear, write “Not stated in the paper” (or “Not shown in the provided excerpt”) rather than guessing.
- **Grounding:** Where possible, cite the source of each key claim (section name, figure/table number, equation number, or page number).
- **Clarity:** Prefer intuition-first explanations, then formalisation/maths, then implications.
- **Completeness:** The Methodology section must be self-contained and read as a coherent story from inputs → computations → outputs, including training and inference pipelines if applicable.
- **Language:** You must use British English.

## Notation rules

- $\mathcal{C}$ denotes a set.
- Bold lowercase $\mathbf{x}$ denotes a vector; bold uppercase $\mathbf{X}$ denotes a matrix.
- Uppercase $X$ denotes a random variable; lowercase $x$ denotes a deterministic value.
- Use correct LaTeX ($...$ inline, $$...$$ for display equations). Define symbols before using them.

Write the note using the EXACT structure and headings below:

### 1. Motivation

- What problem is being addressed?
- What failure mode or limitation of prior work is targeted?
- Why it matters.

### 2. Contributions

- Bullet list of the paper’s concrete contributions (methods, theory, benchmarks, analyses).
- Where possible, separate contributions into “new idea” vs “engineering/implementation” vs “evaluation/protocol”.

### 3. Methodology

- MINI-PAPER STYLE, single coherent story.
- Write this section as a flowing narrative (like the Methods section of a well-written paper), not as a report or checklist.

#### Hard constraints

- No sub-bullets or lettered substeps. Minimal headings are allowed, but prefer continuous prose.
- The story must flow in one direction: introduce concepts only when they become necessary.
- Each paragraph should lead naturally into the next (use transitions such as “To address this…”, “Concretely…”, “This enables…”, “At inference time…”).
- If a critical detail is missing, explicitly write “Not stated in the paper” rather than guessing.

#### Narrative guidance

- Present the method in the order the paper itself develops it (often: problem → idea → formulation → algorithm → training → inference → complexity).
- Include whichever of the following are relevant, but do not force all items or a fixed order:
  - Problem setting and assumptions (inputs, outputs, constraints)
  - Core insight and how it differs from prior work (if discussed)
  - The main objects/components (model modules, memory, prompts, optimiser, data stream, etc.)
  - Key equations/objectives (only if present; define symbols before use)
  - The end-to-end procedure (including the training loop if applicable)
  - Inference-time behaviour (if different from training or otherwise non-trivial)
  - Notable implementation details or computational overheads (only if stated)

### 4. Experimental Setup

- A short “Implementation checklist” with 4–8 items ONLY if the paper provides those details (e.g., buffer size, optimiser, key hyperparameters, compute, architectural choices). If not provided, write “Not stated in the paper.”
- Datasets/tasks, baselines, evaluation protocol, and metrics.
- What ablations or sensitivity analyses were run.

### 5. Strengths & Weaknesses

- **Strengths:** 3–6 bullets with reasons tied to evidence in the paper.
- **Weaknesses/risks:** 3–6 bullets (e.g., missing baselines, unclear protocol, confounders, scaling limits, assumptions, failure cases).
- Include **“What I would ask the authors as a reviewer”** (2–3 questions).

### 6. Final short note

- 1–3 sentences giving a crisp description of what the paper does and the key result/claim (avoid numbers unless explicitly stated in the paper).
- Key takeaways:
  - 3–5 bullets covering what I should steal/adapt, what to be cautious about, and one concrete follow-up experiment idea.
  - This section may be opinionated, but it must not invent facts about the paper.

# Dependency (Install if missing)
```bash
# macOS (Homebrew)
brew install poppler

# Ubuntu/Debian
sudo apt-get install -y poppler-utils
```

If installation isn't possible in this environment, tell the user which dependency is missing and how to install it locally.
