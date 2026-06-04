# Release v0.3.0

## 🎉 New Feature: Lenient Flag Mode

LLM agents and other automated tools often invoke CLI tools with slightly incorrect flag syntax. v0.3.0 introduces a new **Lenient Flag Mode** that makes `bsearch` more robust against such inputs:

- Unknown flags are **silently skipped** with a soft warning on stderr
- The search query still executes as expected
- Smart value consumption: if a flag looks like it takes a value (e.g., `--max-tok`, `--fresh`), the following value is consumed rather than being interpreted as part of the query
- `--help` and `--version` always work as built-in flags
- No breaking changes for correct invocations

Example:
```bash
# All of these work now:
bsearch "python tutorial" --max-tok 8192 --fresh    # Unknown flags skipped
bsearch "python tutorial" --unknown-flag value      # Both consumed
bsearch "python tutorial" --web                     # Normal web search
```

The warning goes to **stderr**, so `--raw` JSON output stays clean and parseable.

## 🐛 Bug Fixes

| # | Issue | Fix |
|---|-------|-----|
| 1 | Off-by-one error in retry logic (`attempt <= retries` made 4 attempts) | Changed to `attempt < retries` for exactly 3 attempts |
| 2 | Empty catch block in `getApiKey()` gave no feedback | Added explanatory comment |
| 3 | Duplicate `msgs` object in `performWebSearch` shadowed global | Removed local definition |
| 4 | Unused `force` parameter in `performLlmContext` | Removed, `--force-llm` now actually prevents fallback |
| 5 | Missing null safety for `meta.age` array | Added `Array.isArray()` and length check |
| 6 | Unsafe `Retry-After` header parsing (could produce `NaN`) | Wrapped in `parseInt()` |
| 7 | `lastError` potentially undefined when thrown | Initialized with fallback error |

## 📊 Code Stats

- **+134 insertions, -14 deletions** in 3 files
- **0 breaking changes** for correct usage
- **3 subagent reviews** completed (code-reviewer, security-reviewer, software-architect)
- **Security audit**: ✅ No vulnerabilities found

## 🚀 Installation

```bash
npm install -g @steimbyte/bsearch-cli
```

## 📝 Full Changelog

See [commits](https://github.com/steimbyte/bsearch-cli/compare/v0.2.3...v0.3.0) for details.
