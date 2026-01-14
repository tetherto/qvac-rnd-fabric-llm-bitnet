# BitNet GPU - macOS Setup Guide

Pre-built binaries for BitNet b1.58 inference and LoRA fine-tuning on macOS.

---

## Supported Hardware

| Chip | Status | Notes |
|------|--------|-------|
| Apple M4 Pro/Max | Fully Supported | Best performance |
| Apple M4 | Fully Supported | Excellent performance |
| Apple M3 Pro/Max | Fully Supported | Excellent performance |
| Apple M3 | Fully Supported | Great performance |
| Apple M2 Pro/Max/Ultra | Fully Supported | Great performance |
| Apple M2 | Fully Supported | Good performance |
| Apple M1 Pro/Max/Ultra | Fully Supported | Good performance |
| Apple M1 | Fully Supported | Good performance |
| Intel (with AMD GPU) | Supported | Use Intel build |
| Intel (integrated) | Supported | Limited performance |

---

## Requirements

- macOS 13.0 (Ventura) or later
- Xcode Command Line Tools
- For Apple Silicon: M1 or newer
- For Intel: macOS with Metal support

---

## Available Builds

| Build | Hardware | Download |
|-------|----------|----------|
| `qvac-macos-apple-silicon-v1.0.zip` | M1/M2/M3/M4 chips | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-macos-apple-silicon-v1.0.zip) |
| `qvac-macos-intel-v1.0.zip` | Intel x64 Macs | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-macos-intel-v1.0.zip) |

---

## Installation

### 1. Download and Extract

```bash
# For Apple Silicon (M1/M2/M3/M4)
curl -L https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-macos-apple-silicon-v1.0.zip \
  -o qvac-macos.zip

# For Intel Macs
curl -L https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-macos-intel-v1.0.zip \
  -o qvac-macos.zip

# Extract
unzip qvac-macos.zip
cd qvac-macos-apple-silicon-v1.0  # or qvac-macos-intel-v1.0
```

### 2. Remove Quarantine (Required)

macOS quarantines downloaded executables. Remove the quarantine attribute:

```bash
xattr -cr .
```

### 3. Verify Installation

```bash
./bin/llama-cli --version
```

### 4. Download a BitNet Model

```bash
mkdir -p models

# TQ1_0 recommended for Apple Silicon (faster)
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq1_0.gguf \
  -O models/bitnet-700m.tq1_0.gguf
```

---

## Usage

### Basic Inference

```bash
./bin/llama-cli \
  -m models/bitnet-700m.tq1_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "Explain the advantages of Apple Silicon for AI." \
  -n 256
```

### Interactive Chat

```bash
./bin/llama-cli \
  -m models/bitnet-700m.tq1_0.gguf \
  -ngl 999 -c 2048 --flash-attn off \
  -i
```

### LoRA Fine-tuning

```bash
./bin/llama-finetune-lora \
  -m models/bitnet-1b.tq2_0.gguf \
  -f train.jsonl \
  --output-adapter my-adapter.gguf \
  -ngl 999 -c 128 -b 128 -ub 128 \
  --flash-attn off \
  --num-epochs 8
```

---

## Performance

### Inference Speed (tokens/second)

| Model | M3 Pro (TQ1_0) | M3 Pro (TQ2_0) |
|-------|----------------|----------------|
| 125M | 386.38 | 123.74 |
| 350M | 194.80 | 50.19 |
| 1B | 111.25 | 24.49 |
| 1.5B | 136.76 | 28.83 |
| 7B | 53.13 | 10.38 |
| 13B | 33.54 | 6.61 |

### LoRA Training Time (per epoch)

| Model | M3 Pro |
|-------|--------|
| 125M | 6m 19s |
| 1B | 9m 19s |
| 7B | 35m 25s |
| 13B | 1h 03m |

---

## Memory Recommendations

Apple Silicon uses unified memory shared between CPU and GPU:

| Unified Memory | Maximum Model (TQ1_0) | Maximum Model (TQ2_0) |
|----------------|----------------------|----------------------|
| 8GB | 3.8B | 2.7B |
| 16GB | 7B | 3.8B |
| 32GB | 13B | 7B |
| 64GB+ | 13B+ | 13B |

---

## Troubleshooting

### "Cannot be opened because the developer cannot be verified"

```bash
# Remove quarantine attribute
xattr -cr .

# Or right-click the binary and select "Open" once
```

### Slow Performance

- Ensure GPU is being used: check `-ngl 999` is set
- Close memory-intensive apps
- Use TQ1_0 format for faster inference

### Out of Memory

- Close other applications
- Use smaller model
- Reduce context length: `-c 256`

---

## Tips for Apple Silicon

1. **Use TQ1_0**: TQ1_0 is significantly faster than TQ2_0 on Apple Silicon
2. **Unified Memory Advantage**: Apple Silicon can run larger models than discrete GPUs with similar VRAM
3. **Keep Cool**: Extended training can cause thermal throttling on laptops
4. **Power Adapter**: Plug in for sustained workloads

---

**[Back to Releases](../README.md)** | **[Main Documentation](../../README.md)**
