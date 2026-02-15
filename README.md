# Phi's Skills

A small collection of my customised skills for coding and research agents.

> [!NOTE]
> These skills are opinionated and optimised for my own workflow. Feel free to adapt it to your own research area!

## 📦 Installation

Recommended

```bash
npx skills add https://github.com/CodeBoyPhilo/Phi-Skills.git
```

## Included skills

<details>
<summary> 📝 <code>summarise-paper</code>  </summary>

- Summarises an academic paper into a standalone LaTeX research note.
- Supports both PDF files and arXiv URLs.
- For PDFs, it renders pages to images before reading (more reliable for equations/figures than text extraction).
- For arXiv, it downloads and reads the LaTeX source.
- Writes the output note under `note/<paper name>/`.
- Built on top of OpenAI's [pdf](https://github.com/openai/skills/blob/main/skills/.curated/pdf/SKILL.md) skill and nanochat's [read-arxiv-paper](https://github.com/karpathy/nanochat/blob/master/.claude/skills/read-arxiv-paper/SKILL.md).
- I typically run this with `GPT-5.2 high`.

**Usage**

Example prompts:

```text
Use the summarise-paper skill to summarise @./paper.pdf.
```

```text
Use the summarise-paper skill to summarise https://arxiv.org/abs/2601.07372.
```

</details>

<details>
<summary> 🔎 <code>search-conference</code>  </summary>

- Finds papers in a specific OpenReview venue that match your research idea (or example papers).
- Uses [`embed-papers`](https://github.com/CodeBoyPhilo/Embed-Papers) to crawl venue metadata, warm an embeddings cache, and run cosine-similarity search.
- Produces a short, grouped Markdown reading list with brief rationales.
- Requires `embed-papers` (in `PATH`) and `OPENAI_API_KEY`.
- I typically run this with `GPT-5.2 high`.

**Usage**

Example prompts:

```text
Use search-conference: find me ICLR 2026 papers exploring the memory and long context problem, e.g. external memory with write policies learned end-to-end. Don't include papers that leveraged reinforcement learning.
```

</details>
