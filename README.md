[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/steimerbyte)

> ⭐ If you find this useful, consider [supporting me on Ko-fi](https://ko-fi.com/steimerbyte)!

<img src="https://storage.ko-fi.com/cdn/generated/fhfuc7slzawvi/2026-04-23_rest-162bec27f642a562eb8401eb0ceb3940-onjpojl8.jpg?w=250" alt="steimerbyte" style="border-radius: 5%; margin: 16px 0; max-width: 100%;"/>

# bsearch - Brave Search CLI

CLI for Brave Search API with **LLM Context** (pre-extracted content for AI/RAG) and **Web Search** modes.

## Features

- **LLM Context API** (default) - Pre-extracted web content optimized for AI agents and RAG pipelines
- **Web Search API** - Classic search with links and descriptions
- Dynamic mode switching via CLI flags or ENV variables
- Configurable token limits, thresholds, and source filtering
- Location-aware queries with POI support
- Goggles custom source ranking
- Auto-retry with exponential backoff
- Auto-fallback to web search if LLM Context not available in plan

## Prerequisites

- Node.js 20+
- Brave Search API key ([Get one](https://api.search.brave.com/app/keys))

## Installation

```bash
npm install -g brave-search-cli
# or
npm install -g @steimer/bsearch-cli
```

## Setup

```bash
export BRAVE_API_KEY="your-api-key-here"
```

## Usage

### Mode Switching

| Method | Example |
|--------|---------|
| **Default** | `bsearch "query"` (LLM Context) |
| **CLI Flag** | `bsearch "query" --web` or `bsearch "query" --llm` |
| **ENV Variable** | `BSEARCH_MODE=web bsearch "query"` |

**Priority:** `--llm`/`--web` > `BSEARCH_MODE` > default (llm)

### LLM Context Mode (default)

```bash
# Basic search with pre-extracted content
bsearch "python async await best practices"

# Compact output for faster results
bsearch "latest AI news" -f pd --compact

# Custom token budget
bsearch "research topic" --max-tokens 16384 --max-urls 30

# High precision (strict relevance filtering)
bsearch "medical information" --threshold strict

# Local/POI search
bsearch "coffee shop" --local --city "Berlin"
bsearch "restaurant" --lat 52.52 --long 13.405

# Custom source ranking
bsearch "documentation" --goggles "https://..."

# Freshness filter
bsearch "latest news" -f pd      # past day
bsearch "this week" -f pw       # past week
bsearch "this month" -f pm      # past month
bsearch "2024" -f 2024-01-01to2024-12-31
```

### Web Search Mode

```bash
# Classic web search
bsearch "python tutorial" --web

# With options
bsearch "news" -f pd -c 5 --web

# SafeSearch
bsearch "query" -s strict --web
```

### Output Options

```bash
bsearch "query" --raw       # Raw JSON response
bsearch "query" --compact   # Fewer snippets per source
```

## Options

### LLM Context Options

| Flag | Description | Default |
|------|-------------|---------|
| `--max-tokens N` | Max tokens in context (1024-32768) | 8192 |
| `--max-urls N` | Max URLs in response (1-50) | 20 |
| `--max-snippets N` | Max snippets total (1-100) | 50 |
| `--max-tokens-per-url N` | Max tokens per URL (512-8192) | 4096 |
| `--max-snippets-per-url N` | Max snippets per URL (1-100) | 50 |
| `--threshold mode` | strict/balanced/lenient/disabled | balanced |
| `--goggles url` | Goggles URL for custom ranking | - |
| `--local` | Force local/POI recall | - |
| `--lat N` | Latitude for location search | - |
| `--long N` | Longitude for location search | - |
| `--city name` | City for location headers | - |
| `--state code` | State/region code | - |
| `--country code` | Country code (2 letters) | - |
| `--postal code` | Postal code | - |

### Web Search Options

| Flag | Description | Default |
|------|-------------|---------|
| `-c N` | Results count (1-20) | 10 |
| `-s mode` | SafeSearch (off/moderate/strict) | off |
| `-f period` | Freshness (pd/pw/pm) | - |
| `-o N` | Pagination offset | 0 |

### General Options

| Flag | Description | Default |
|------|-------------|---------|
| `-m, --mode mode` | llm or web | llm |
| `--llm` | Explicit LLM Context mode | - |
| `--web` | Explicit web search mode | - |
| `--raw` | Raw JSON output | - |
| `--compact` | Compact output | - |
| `--timeout ms` | Request timeout | 30000 |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `BRAVE_API_KEY` | Your Brave Search API key (required) |
| `BSEARCH_MODE` | Default mode: `llm` or `web` |

## Output Format

### LLM Context
```
📄 Sources (5):

1. Page Title
   https://example.com/page
   Extracted snippet from the page...
   Another relevant passage...

2. ...
```

### Web Search
```
[Web Search] Found 10 results for "query":

1. Page Title
   https://example.com/page
   Page description...

2. ...
```

## Error Handling

- 30s timeout (configurable)
- Auto-retry with exponential backoff (up to 3 attempts)
- Rate limit handling with automatic retry
- Graceful fallback to web search if LLM Context unavailable
- Clear error messages for API errors (401, 429, 500)

## License

MIT
