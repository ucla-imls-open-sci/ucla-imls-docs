---
title: AI Review
layout: default
parent: IMLS Tools
nav_order: 3
---

The mechanical checker (see [Checker](checker)) catches structural problems
deterministically and for free. The `--ai` flag adds a narrative review for
the part a deterministic checker can't do: whether a challenge is
pedagogically sound, whether the tone matches the
[Carpentries style guide](https://carpentries.github.io/sandpaper-docs/instructor/style.html),
whether something will confuse a learner encountering it fresh. It's given
the mechanical findings as context, so it doesn't just repeat the checklist.

It's off by default — it costs time, and for `claude`/`codex` it costs API
usage.

```
pixi run check ./my-lesson --episode 03-sharing.md --ai --backend ollama
pixi run check ./my-lesson --episode 03-sharing.md --ai --backend claude
pixi run check ./my-lesson --episode 03-sharing.md --ai --backend codex
```

All three backends use the same local Ollama embedding model
(`nomic-embed-text`) to retrieve relevant style-guide passages — that part
never leaves your machine or costs anything, regardless of which backend
answers the actual question.

## Backends

| Backend | What it needs | Notes |
|---|---|---|
| `ollama` | `pixi run pull-models`, then `ollama serve` running | Fully local and free; slower and less sharp than the API backends |
| `claude` | `ANTHROPIC_API_KEY` set, or `ant auth login` | Uses the Anthropic Python SDK directly. Default model `claude-opus-5`; pass `--model claude-sonnet-5` or `--model claude-haiku-4-5` for a cheaper/faster option |
| `codex` | The [OpenAI Codex CLI](https://developers.openai.com/codex) installed and logged in (`codex exec "hello"` should just print a reply) | Shells out to `codex exec`; pass `--model <name>` to override its configured default. On macOS, a fresh install can get flagged and deleted by an XProtect false positive — see [openai/codex#31377](https://github.com/openai/codex/issues/31377) if `codex exec` fails with a missing-binary error |

`--model` overrides the default for whichever `--backend` you picked.
`--embed-model` overrides the retrieval embedding model (default
`nomic-embed-text`) if you want to try a different one.

## Recommended local models (16GB Apple Silicon)

Pull them with pixi:

```
pixi run pull-models          # nomic-embed-text + qwen3.5:9b-q4_K_M (default, balanced)
pixi run pull-models-small    # nomic-embed-text + qwen3.5:4b (faster, lighter)
pixi run pull-models-coding   # qwen2.5-coder:7b (for code-heavy lessons)
```

| Model | Download | Use it for | Why |
|---|---|---|---|
| `qwen3.5:9b-q4_K_M` (default) | ~6.6 GB | General episode review | Best balance of quality and footprint at this size — 256K context, leaves real headroom on 16GB while your browser/editor are also open |
| `qwen3.5:4b` | ~3.4 GB | Quick iterative checks | Noticeably faster, still coherent; use while drafting, switch to the 9B for a final pass |
| `qwen2.5-coder:7b` | ~4.7 GB | Lessons with heavy code blocks (shell, Python, R episodes) | Coder-tuned variant reviews code samples more carefully than the general model |
| `gpt-oss:20b` | ~14 GB | A stretch option if you want the best local quality and can close everything else | Runs on 16GB via MXFP4 quantization, but leaves little headroom |
| `nomic-embed-text` | ~274 MB | Retrieval (used by every backend, not just `ollama`) | Small and fast, good enough for retrieving style-guide passages |

Don't run the Ollama backend alongside something else memory-hungry (another
large model, a heavy IDE) on 16GB — swap will tank throughput long before
you run out of RAM outright.
