# Marp slide sources

Markdown sources for workshop slides, rendered with [Marp](https://marp.app/).
This directory is excluded from Jekyll builds (see `exclude` in `_config.yml`).

Rendered output goes to `workshop-slides/` (published by Jekyll as static files).

    npx @marp-team/marp-cli marp/<deck>.md -o workshop-slides/<deck>.html
    npx @marp-team/marp-cli marp/<deck>.md -o workshop-slides/<deck>.pdf
    npx @marp-team/marp-cli marp/<deck>.md -o workshop-slides/<deck>.pptx

Render all decks to one format:

    npx @marp-team/marp-cli --input-dir marp/ -o workshop-slides/ --html
