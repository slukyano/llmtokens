# llmtokens

Count tokens, lines, words, characters, and bytes in text using HuggingFace tokenizers.

## Installation

Run directly with `uvx`:
```bash
uvx llmtokens
```

Or install as a tool:
```bash
uv tool install llmtokens
```

## Usage

### Basic usage (stdin)
```bash
echo "hello world" | llmtokens
```

### From a file
```bash
llmtokens input.txt
```

### Multiple models
```bash
echo "hello world" | llmtokens -m Qwen/Qwen2.5-0.5B -m meta-llama/Llama-3.3-70B-Instruct
```

### Modes
- `--mode raw` - Count raw text only (no chat template)
- `--mode template` - Apply chat template (warn if missing)
- `--mode either` - Use template if available, otherwise raw
- `--mode both` - Show both raw and templated (warn if template missing)
- `--mode both-auto` - Show both if template available (default)

### Input wrapping
- `--wrap-input auto` - Try to parse as JSON messages, wrap as user message if not (default)
- `--wrap-input yes` - Always wrap input as a user message
- `--wrap-input no` - Parse input as JSON messages array

### Display options
- `--show-original` - Show the original input text
- `--show-template` - Show the chat template
- `--show-rendered` - Show rendered text after applying template
- `--show-all` - Show all of the above

### Examples

Count tokens with chat template:
```bash
echo '[{"role": "user", "content": "Hello"}]' | llmtokens
```

Show template application:
```bash
echo "What is 2+2?" | llmtokens --show-all
```

Compare multiple models:
```bash
cat document.txt | llmtokens -m Qwen/Qwen2.5-0.5B -m meta-llama/Llama-3.3-70B-Instruct
```

## Help

For all available options:
```bash
llmtokens --help
```

## Gated Models

For gated models (e.g., Llama), set the `HF_TOKEN` environment variable or run:
```bash
huggingface-cli login
```

## License

MIT
