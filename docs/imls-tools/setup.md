---
title: Setup
layout: default
parent: IMLS Tools
nav_order: 1
---

The checker uses [pixi](https://pixi.sh) to manage its whole environment,
including [Ollama](https://ollama.com) itself (installed from conda-forge —
no separate `brew install ollama` step needed).

1. **Install pixi** (if you don't already have it)

    ```
    curl -fsSL https://pixi.sh/install.sh | sh
    ```

2. **Clone the repository**

    ```
    git clone https://github.com/ucla-imls-open-sci/imls-tools.git
    cd imls-tools
    ```

3. **Install the environment**

    ```
    pixi install
    ```

    This resolves and installs Python, Ollama, and every Python dependency
    (langchain, chromadb, the Anthropic SDK, etc.) into an isolated `.pixi/`
    folder — nothing is installed globally.

4. **Run a check**

    ```
    pixi run check ./my-lesson
    ```

    See [Checker](checker) for the full flag reference, and
    [AI Review](ai-review) for the optional AI narrative review and its
    three backends (`ollama`, `claude`, `codex`).

*NOTE: this replaces the older `content_check.py` (PySimpleGUI) and
`content_check.sh` scripts, which required a manual `pip install` /
Homebrew setup and are no longer part of the repository.*
