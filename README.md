[![Build](https://github.com/gweidart/rs-bpe/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/gweidart/rs-bpe/actions/workflows/ci.yml)
[![GH Release](https://github.com/gweidart/rs-bpe/actions/workflows/gh_release.yml/badge.svg?branch=main)](https://github.com/gweidart/rs-bpe/actions/workflows/gh_release.yml)
[![PyPI Release](https://github.com/gweidart/rs-bpe/actions/workflows/release.yml/badge.svg?branch=main)](https://github.com/gweidart/rs-bpe/actions/workflows/release.yml)

# rs-bpe

`rs-bpe` is a Rust-based BPE tokenizer with Python bindings. It is designed for fast, correct token counting and practical chunking workflows, while keeping an OpenAI-style API for common models such as `cl100k_base` and `o200k_base`.

## Why rs-bpe

- Fast token counting and encoding for Python workloads
- Exact split and chunk operations without breaking UTF-8 boundaries
- Batch and parallel APIs for larger datasets
- Prebuilt tokenizers for OpenAI and DeepSeek vocabularies

## Quickstart

Install from PyPI:

```bash
pip install rs-bpe
```

Encode, count, and decode text:

```python
import rs_bpe

tokenizer = rs_bpe.cl100k_base()
text = "Hello, world!"

tokens = tokenizer.encode(text)
count = tokenizer.count(text)
decoded = tokenizer.decode(tokens)

print(tokens)
print(count)
print(decoded)
```

## Basic usage

### Choose a tokenizer

```python
import rs_bpe

cl100k = rs_bpe.cl100k_base()
o200k = rs_bpe.o200k_base()
deepseek = rs_bpe.deepseek_base()
deepseek_32 = rs_bpe.deepseek_32()
```

### Split text into token-safe chunks

```python
import rs_bpe

tokenizer = rs_bpe.cl100k_base()
text = "A longer document that needs chunking for downstream LLM calls."

chunks = tokenizer.split_chunks(text, chunk_size=16)
print(chunks)
```

### Stop when a token limit is exceeded

```python
import rs_bpe

tokenizer = rs_bpe.cl100k_base()
text = "This is a sample input."

count = tokenizer.count_till_limit(text, limit=32)

if count is None:
    print("Token limit exceeded")
else:
    print(f"Token count: {count}")
```

### Encode batches in parallel

```python
import rs_bpe

tokenizer = rs_bpe.cl100k_base()
texts = ["hello", "world", "batch encoding"]

encoded, total_tokens, elapsed, threads = tokenizer.encode_batch_parallel(texts)

print(encoded)
print(total_tokens, elapsed, threads)
```

### Build a prompt with `apply_chat_template`

```python
import rs_bpe

tokenizer = rs_bpe.deepseek_32()
messages = [
    {"role": "system", "content": "You are a concise assistant."},
    {"role": "user", "content": "Explain BPE in one sentence."},
]

prompt = tokenizer.apply_chat_template(
    messages,
    thinking_mode="chat",
)

print(prompt)
```

## Performance

`rs-bpe` is built for high-throughput token counting and chunking, including adversarial cases where naive BPE workflows degrade badly. The repository includes benchmark scripts and result plots, but the README keeps the summary short on purpose.

For benchmark details, see [benchmark/benchmark_README.md](benchmark/benchmark_README.md) and the plots under [`benchmark/`](benchmark/) and [`assets/`](assets/).

## Development

```bash
cargo check --workspace
cargo test --workspace
cargo clippy --workspace
```

On Python 3.14, use:

```bash
PYO3_USE_ABI3_FORWARD_COMPATIBILITY=1 cargo check --workspace
```

## Project layout

- `crates/bpe`: core Rust implementation
- `crates/bpe-openai`: prebuilt tokenizer logic
- `crates/python-bpe`: PyO3 bindings
- `python/rs_bpe`: Python package wrapper
- `tests/`: tests
- `benchmark/`: benchmark scripts and reports
