# llmtokens - Developer Guide

## Package Structure

```
llmtokens/
├── .gitignore
├── CLAUDE.md              # This file
├── LICENSE                # MIT license
├── README.md              # User-facing documentation
├── pyproject.toml         # Build configuration
└── src/
    └── llmtokens/
        ├── __init__.py    # Version string
        └── cli.py         # Main CLI logic
```

## Build System

This package uses:
- **src layout**: Package code lives in `src/llmtokens/` to ensure clean imports during development
- **hatchling**: Modern, minimal build backend that works well with uv/uvx
- **Python 3.12+**: Requires modern Python for type hints and language features

## Development Commands

### Install in development mode
```bash
uv pip install -e .
```

### Run locally
```bash
uv run llmtokens --help
echo "hello world" | uv run llmtokens
```

### Run tests (when added)
```bash
uv run pytest
```

### Build package
```bash
uv build
```

### Lint (when configured)
```bash
uv run ruff check .
uv run mypy src/
```

## Dependencies

Runtime dependencies (installed automatically):
- `huggingface_hub` - Download tokenizers from HuggingFace Hub
- `tokenizers` - Fast tokenization
- `jinja2` - Chat template rendering

## Key Conventions

- **Conventional commits**: All commits follow the conventional commit format
  - `feat:` - New features
  - `fix:` - Bug fixes
  - `docs:` - Documentation changes
  - `chore:` - Maintenance tasks
  - `refactor:` - Code restructuring without behavior changes
  - `test:` - Test additions or modifications

- **Entry point**: CLI is exposed via `pyproject.toml` scripts section
  - Command: `llmtokens`
  - Target: `llmtokens.cli:main`

- **Version**: Single source of truth in `src/llmtokens/__init__.py`

## Publishing (future)

When ready to publish to PyPI:
```bash
# Build
uv build

# Publish (requires PyPI credentials)
uv publish
```

## Testing the Package

Before publishing, verify the package works correctly:
```bash
# Test help
uv run llmtokens --help

# Test basic functionality
echo "hello world" | uv run llmtokens

# Test with file input
echo "test content" > /tmp/test.txt
uv run llmtokens /tmp/test.txt

# Test multiple models
echo "compare models" | uv run llmtokens -m Qwen/Qwen2.5-0.5B -m meta-llama/Llama-3.3-70B-Instruct
```
