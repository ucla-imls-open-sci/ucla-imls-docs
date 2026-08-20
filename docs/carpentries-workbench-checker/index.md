---
title: Carpentries Workbench Checker
layout: default
permalink: /docs/carpentries-workbench-checker
has_children: true
nav_order: 6
---

Local pre-flight checks for Carpentries Workbench lessons: a fast, deterministic
structure check (front matter, required `:::` blocks, headings, links/images),
plus an optional AI narrative review of writing and pedagogy. Point it at a
local lesson directory or a lesson's git URL; run it before opening a PR
instead of waiting on the sandpaper CI build.

You can find the tool in this [repository](https://github.com/ucla-imls-open-sci/carpentries-workbench-checker.git).

**Pages in this section:**

- [Setup](setup) — installing pixi and the tool
- [Checker](checker) — the mechanical checks, CLI flags, report formats
- [AI Review](ai-review) — the optional `--ai` narrative review and its three backends
