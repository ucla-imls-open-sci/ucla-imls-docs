---
title: Checker
layout: default
parent: Carpentries Workbench Checker
nav_order: 2
---

The mechanical checker mirrors, locally and in under a second, a subset of
what `sandpaper::validate_lesson()` and the `pegboard` package's
`validate_divs()` / `validate_headings()` / `validate_links()` check in
Carpentries' CI (which runs inside a Docker container on every PR and takes
several minutes). It's not a replacement for that check — sandpaper is still
the final word — it exists so you catch the obvious problems before you push.

| Category | What we check | Mirrors |
|---|---|---|
| `config` | Placeholder values left unfilled in `config.yaml`, missing `created` date, episode list vs. files on disk | `sandpaper::validate_lesson()` |
| `front-matter` | `title` / `teaching` / `exercises` present and numeric in every episode | `sandpaper::validate_lesson()` |
| `divs` | Required `questions`/`objectives`/`keypoints` blocks, balanced `:::` fences, recognized div types, challenge/solution counts | `pegboard::validate_divs()` |
| `headings` | First heading is `##` (not `#` or lower), no duplicate headings | `pegboard::validate_headings()` |
| `links` | Missing image alt text, broken internal links/images — including Workbench's `episodes/fig/`-relative image convention and `.html` links resolving to `.md` sources | `pegboard::validate_links()` |

## Basic usage

```
pixi run check ./my-lesson
```

You can also point it straight at a lesson's git URL — it clones to a temp
directory and cleans up after itself:

```
pixi run check https://github.com/librarycarpentry/lc-git.git
```

Example output (run against a real public lesson):

```
Lesson Check Report — https://github.com/librarycarpentry/lc-git.git
1 error(s), 18 warning(s), 2 note(s)

config.yaml
  ❌ [config] `contact` is still the template placeholder or empty
     → Set `contact` to your lesson's real value.

episodes/02-getting-started.md
  ⚠️ [headings] first heading `### Setting up Git` on line 18 is level 3, expected level 2
  ⚠️ [links] image on line 189 has no alt text: `fig/git-branch.svg`
     → Add descriptive alt text for accessibility.
```

Exit code is `1` if any error-level finding was reported, `0` otherwise —
safe to use in a pre-commit hook or your own CI step.

## Flags

| Flag | Purpose |
|---|---|
| `--episode <name>` | Only check this one episode file, e.g. `--episode 03-sharing.md` |
| `--format {terminal,markdown,json}` | Report format (default `terminal`) |
| `--output <path>` | Write the report to a file instead of stdout |
| `--html` | Also render the markdown report to HTML with [Quarto](https://quarto.org), if it's installed. Falls back to a warning (not a failure) if it isn't. |
| `--ai` | Also run an AI narrative review — see [AI Review](ai-review) |

## Report formats

**`markdown`** produces an actionable checklist you can paste directly into a
PR description or read locally — unchecked boxes are the things to fix:

```
pixi run check ./my-lesson --format markdown --output report.md
```

```markdown
## config.yaml

- [ ] ❌ **config** — `contact` is still the template placeholder or empty
      *Fix:* Set `contact` to your lesson's real value.
```

**`json`** is machine-readable, for wiring into your own tooling:

```
pixi run check ./my-lesson --format json
```

**`--html`** renders the markdown report through Quarto for a nicer page —
useful if you want to share a report outside GitHub:

```
pixi run check ./my-lesson --format markdown --output report.md --html
```
