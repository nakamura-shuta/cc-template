---
allowed_tools: Bash(gemini), Read(*), Edit(*)
description: "This command use gemini cli"
---

## Gemini Search

`gemini` is google gemini cli. You can use it for web search.

Run web search via Task Tool with `gemini -p 'WebSearch: ...'`.

```bash
gemini -p "WebSearch: $ARGUMENTS"
```