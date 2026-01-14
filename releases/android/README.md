# BitNet GPU - Android (Termux) Setup Guide

Pre-built binaries for BitNet b1.58 inference and LoRA fine-tuning on Android devices.

---

## Supported Hardware

| GPU | Status | Notes |
|-----|--------|-------|
| Qualcomm Adreno 830 (S25) | Fully Supported | Best mobile performance |
| Qualcomm Adreno 750 (S24) | Fully Supported | Good performance |
| Qualcomm Adreno 740 (S23) | Fully Supported | Good performance |
| ARM Mali G715 (Pixel 9) | Fully Supported | Slower than Adreno |
| ARM Mali G710 (Pixel 8) | Fully Supported | Slower than Adreno |

---

## Requirements

- Android 10+ (API 29+)
- Vulkan 1.1 support
- Termux app (from F-Droid recommended)
- 4GB+ RAM for small models, 8GB+ for larger models

---

## Available Builds

| Build | GPU Support | Download |
|-------|-------------|----------|
| `qvac-android-adreno-arm64-v1.0.zip` | Qualcomm Adreno + Mali | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-android-adreno-arm64-v1.0.zip) |

---

## Installation

### 1. Install Termux

Download Termux from F-Droid (recommended) or Google Play:
- F-Droid: https://f-droid.org/packages/com.termux/

### 2. Setup Termux Environment

```bash
# Update packages
pkg update && pkg upgrade -y

# Install required tools
pkg install wget unzip

# Grant storage access
termux-setup-storage
```

### 3. Download and Extract

```bash
# Download the binary
wget https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-android-adreno-arm64-v1.0.zip

# Extract
unzip qvac-android-adreno-arm64-v1.0.zip
cd qvac-android-adreno-arm64-v1.0

# Set library path (required for every session)
export LD_LIBRARY_PATH=.
```

### 4. Download a BitNet Model

```bash
mkdir -p models

# Small model (700M) - recommended for testing
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq2_0.gguf \
  -O models/bitnet-700m.tq2_0.gguf

# Or TQ1_0 for faster inference
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq1_0.gguf \
  -O models/bitnet-700m.tq1_0.gguf
```

---

## Usage

### Basic Inference

```bash
# Set library path
export LD_LIBRARY_PATH=.

# Run inference
./bin/llama-cli \
  -m models/bitnet-700m.tq2_0.gguf \
  -ngl 99 -s 42 -c 512 --flash-attn off \
  -p "Explain the BitNet architecture." \
  -n 128
```

### Interactive Chat

```bash
./bin/llama-cli \
  -m models/bitnet-700m.tq2_0.gguf \
  -ngl 99 -c 512 --flash-attn off \
  -i
```

### LoRA Fine-tuning

```bash
./bin/llama-finetune-lora \
  -m models/bitnet-700m.tq2_0.gguf \
  -f train.jsonl \
  --output-adapter my-adapter.gguf \
  -ngl 99 -c 128 -b 128 -ub 128 \
  --flash-attn off \
  --num-epochs 5
```

---

## Performance

### Inference Speed (tokens/second)

| Model | Adreno 830 (TQ1_0) | Adreno 830 (TQ2_0) | Mali G715 (TQ1_0) | Mali G715 (TQ2_0) |
|-------|--------------------|--------------------|-------------------|-------------------|
| 125M | 91.23 | 139.17 | 38.67 | 20.54 |
| 1B | 27.16 | 40.50 | 8.23 | 11.32 |
| 3.8B | 11.54* | 19.38* | 2.43 | 3.56 |

*Models marked with * require reduced context length (`-c 128`)

### LoRA Training Time (per epoch)

| Model | Adreno 830 | Mali G715 |
|-------|------------|-----------|
| 125M | 10m 10s | 17m 25s |
| 1B | 1h 18m | 2h 08m |
| 3.8B | 4h 51m | 7h 15m |

---

## Troubleshooting

### "DeviceLoss" Error

This occurs when GPU memory limits are exceeded. Solutions:
- Reduce context length: `-c 128` instead of `-c 512`
- Use smaller model
- Use TQ1_0 format instead of TQ2_0

### Out of Memory (OOM)

- Close other apps
- Use smaller model (< 3.8B recommended)
- Reduce batch size for training: `-b 64 -ub 64`

### Slow Performance

- Ensure GPU layers are enabled: `-ngl 99`
- Keep device cool (throttling reduces performance)
- Close background apps

### Library Not Found

Always set the library path before running:
```bash
export LD_LIBRARY_PATH=.
```

---

## Tips

1. **Keep Device Cool**: Mobile GPUs throttle when hot. Use in a cool environment.
2. **Plug In**: Training drains battery quickly. Keep device plugged in.
3. **Use TQ1_0**: For inference, TQ1_0 is faster and uses less memory.
4. **Start Small**: Test with 125M or 700M models before larger ones.

---

## Model Recommendations

| Use Case | Recommended Model | Format |
|----------|-------------------|--------|
| Quick testing | bitnet-125m | TQ1_0 |
| General use | bitnet-700m | TQ1_0 |
| Quality focus | bitnet-1b | TQ2_0 |
| Maximum quality | bitnet-3.8b | TQ2_0 (with -c 128) |

---

**[Back to Releases](../README.md)** | **[Main Documentation](../../README.md)**
