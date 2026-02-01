# llmtokens

Count tokens and explore chat templates using HuggingFace tokenizers.

## Overview

Understanding how different models tokenize text is crucial for working with LLMs. Token counts affect API costs, context limits, and model performance. This tool helps you:

- **Count tokens accurately** using the actual tokenizer from any HuggingFace model
- **Compare tokenizer efficiency** across models and languages
- **Explore chat templates** to understand what gets added to your prompts
- **Go beyond the "~4 bytes per token" rule of thumb** with real data

## Installation

Run directly without installation:
```bash
uvx llmtokens
```

Or install as a persistent tool:
```bash
uv tool install llmtokens
```

## Quick Start

The simplest possible example:
```bash
echo "hello world" | llmtokens
```

Output:
```
============================================================
                           STATS
============================================================
            model      mode  lines  words  chars  bytes  tokens
---------------------------------------------------------------
Qwen/Qwen2.5-0.5B       raw      1      2     12     12       3
Qwen/Qwen2.5-0.5B  template      6     11    120    120      22
```

By default, `llmtokens` shows both raw token counts and templated counts (with the model's chat template applied).

## Counting Tokens

### From stdin
```bash
echo "your text here" | llmtokens
```

### From a file
```bash
llmtokens document.txt
```

### Raw counts only (no chat template overhead)
```bash
cat document.txt | llmtokens --mode raw
```

## Comparing Models Across Languages

How efficient are different tokenizers for different languages? Let's compare three models on classic book openings:

**English** (*Pride and Prejudice*, Jane Austen):
> It is a truth universally acknowledged, that a single man in possession of a good fortune, must be in want of a wife...

**Russian** (*War and Peace*, Leo Tolstoy):
> — Eh bien, mon prince. Gênes et Lucques ne sont plus que des apanages, des поместья, de la famille Buonaparte...

**Chinese** (*Dream of the Red Chamber*, Cao Xueqin, 红楼梦):
> 甄士隐梦幻识通灵，贾雨村风尘怀闺秀。此开卷第一回也。作者自云：因曾历过一番梦幻之后...

### Example command
```bash
cat english.txt | llmtokens --mode raw \
  -m Qwen/Qwen2.5-0.5B \
  -m meta-llama/Llama-3.3-70B-Instruct \
  -m mistralai/Mistral-7B-Instruct-v0.3
```

Output:
```
============================================================
                           STATS
============================================================
                             model  mode  lines  words  chars  bytes  tokens
----------------------------------------------------------------------------
                 Qwen/Qwen2.5-0.5B   raw      0     70    375    375      76
 meta-llama/Llama-3.3-70B-Instruct   raw      0     70    375    375      77
mistralai/Mistral-7B-Instruct-v0.3   raw      0     70    375    375      80
```

### Results summary

| Language | Bytes | Qwen | Llama | Mistral | Notes |
|----------|-------|------|-------|---------|-------|
| English  | 378   | 77   | 78    | 81      | All models similar (~4.9 bytes/token) |
| Russian  | 515   | 111  | 108   | 132     | Mistral needs 22% more tokens than Llama |
| Chinese  | 336   | 97   | 107   | 130     | Qwen most efficient (Chinese-origin model); Mistral needs 34% more |

**Key observations:**
- English tokenization is efficient across all models (~4.9 bytes/token)
- For Russian text, Mistral requires 22% more tokens than Llama
- For Chinese text, Qwen (a Chinese-origin model) is most efficient, while Mistral needs 34% more tokens than Qwen
- Language-specific tokenizers matter: Qwen's efficiency on Chinese demonstrates the value of tokenizers trained on diverse datasets

## Chat Templates

LLMs don't just see your raw text. Models expect input in a specific format with special tokens, role markers, and system prompts. The same user message becomes different token sequences depending on the model's chat template.

Compare raw vs templated counts:
```bash
echo "What is 2+2?" | llmtokens --mode both
```

Output:
```
============================================================
                           STATS
============================================================
            model      mode  lines  words  chars  bytes  tokens
---------------------------------------------------------------
Qwen/Qwen2.5-0.5B       raw      1      3     13     13       7
Qwen/Qwen2.5-0.5B  template      6     12    121    121      26
```

The chat template overhead is significant: 7 tokens becomes 26 tokens (3.7x increase).

## Exploring Templates

### See what the model actually receives
```bash
echo "What is 2+2?" | llmtokens --show-rendered
```

Output shows the rendered template:
```
<|im_start|>system
You are a helpful assistant.<|im_end|>
<|im_start|>user
What is 2+2?
<|im_end|>
<|im_start|>assistant
```

### See the Jinja2 template itself
```bash
echo "What is 2+2?" | llmtokens --show-template
```

### Show everything
```bash
echo "What is 2+2?" | llmtokens --show-all
```

This displays:
- Original input
- Rendered template text
- The Jinja2 template definition
- Token statistics

## All Options

### Model selection
- `-m MODEL` - Specify model (default: `Qwen/Qwen2.5-0.5B`)
- Multiple `-m` flags to compare models side-by-side

### Modes
- `--mode raw` - Count raw text only (no chat template)
- `--mode template` - Apply chat template (warn if missing)
- `--mode either` - Use template if available, otherwise raw
- `--mode both` - Show both raw and templated (warn if template missing)
- `--mode both-auto` - Show both if template available (default)

### Input wrapping
- `--wrap-input auto` - Parse as JSON messages if possible, otherwise wrap as user message (default)
- `--wrap-input yes` - Always wrap input as a user message
- `--wrap-input no` - Parse input as JSON messages array

### Display options
- `--show-original` - Show the original input text
- `--show-template` - Show the Jinja2 chat template
- `--show-rendered` - Show rendered text after applying template
- `--show-all` - Show everything

### Other
- `-v, --verbose` - Verbose output
- `file` - Read from file instead of stdin

### Help
```bash
llmtokens --help
```

## Gated Models

Some models (e.g., Llama) require HuggingFace authentication. Either set the `HF_TOKEN` environment variable or run:
```bash
huggingface-cli login
```

## License

MIT
