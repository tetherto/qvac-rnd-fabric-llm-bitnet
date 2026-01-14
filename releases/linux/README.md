# BitNet GPU - Linux Setup Guide

Pre-built binaries for BitNet b1.58 inference and LoRA fine-tuning on Linux systems.

---

## Supported Hardware

| GPU | Backend | Status | Notes |
|-----|---------|--------|-------|
| NVIDIA RTX 40/30/20 series | Vulkan | Fully Supported | Best performance |
| NVIDIA GTX 16/10 series | Vulkan | Fully Supported | Good performance |
| AMD Radeon RX 7000/6000 | Vulkan | Fully Supported | Excellent performance |
| AMD Radeon RX 5000 | Vulkan | Fully Supported | Good performance |
| Intel Arc A-series | Vulkan/SYCL | Fully Supported | Good performance |
| Intel integrated (Xe) | Vulkan | Supported | Limited VRAM |

---

## Requirements

- Linux kernel 5.4 or later
- glibc 2.31 or later
- Vulkan 1.1+ drivers
- GPU with Vulkan support

---

## Available Builds

| Build | GPU Support | Download |
|-------|-------------|----------|
| `qvac-linux-vulkan-x64-v1.0.zip` | AMD/NVIDIA/Intel (Vulkan) | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-linux-vulkan-x64-v1.0.zip) |
| `qvac-linux-arm64-v1.0.zip` | ARM64 CPU | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-linux-arm64-v1.0.zip) |
| `qvac-linux-sycl-intel-v1.0.zip` | Intel GPU (SYCL) | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-linux-sycl-intel-v1.0.zip) |

---

## Installation

### 1. Install Vulkan Drivers

#### NVIDIA

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install nvidia-driver-535 nvidia-utils-535

# Fedora
sudo dnf install nvidia-driver
```

#### AMD

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install mesa-vulkan-drivers

# Fedora
sudo dnf install mesa-vulkan-drivers
```

#### Intel

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install intel-media-va-driver mesa-vulkan-drivers

# Fedora
sudo dnf install intel-media-driver mesa-vulkan-drivers
```

### 2. Verify Vulkan Installation

```bash
# Install vulkan-tools if needed
sudo apt install vulkan-tools  # Ubuntu/Debian
sudo dnf install vulkan-tools  # Fedora

# Verify
vulkaninfo | head -30
```

### 3. Download and Extract

```bash
# Download
wget https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-linux-vulkan-x64-v1.0.zip

# Extract
unzip qvac-linux-vulkan-x64-v1.0.zip
cd qvac-linux-vulkan-x64-v1.0

# Make binaries executable
chmod +x bin/*

# Test installation
./bin/llama-cli --version
```

### 4. Download a BitNet Model

```bash
mkdir -p models

# Download model
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
  -p "Explain quantum computing in simple terms." \
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
  --learning-rate 1e-5 \
  --num-epochs 8
```

### Benchmarking

```bash
./bin/llama-cli \
  -m models/bitnet-700m.tq1_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "Test prompt" \
  --bench 5 -n 256
```

---

## Performance

### Inference Speed (tokens/second)

| Model | NVIDIA 4090 (TQ1_0) | NVIDIA 4090 (TQ2_0) | AMD CPU |
|-------|---------------------|---------------------|---------|
| 125M | 548.86 | 190.38 | 285.28 |
| 1B | 257.80 | 43.84 | 58.99 |
| 7B | 108.73 | 22.13 | 13.83 |
| 13B | 74.29 | 13.25 | 8.20 |

### LoRA Training Time (per epoch)

| Model | NVIDIA 4090 |
|-------|-------------|
| 125M | 1m 20s |
| 1B | 3m 31s |
| 7B | 16m 20s |
| 13B | 27m 41s |

---

## Troubleshooting

### Vulkan Not Found

```bash
# Check Vulkan installation
vulkaninfo

# If missing, install appropriate drivers (see above)
```

### Permission Denied

```bash
chmod +x bin/*
```

### GPU Not Detected

```bash
# List available GPUs
./bin/llama-cli --list-devices

# Check Vulkan devices
vulkaninfo --summary
```

### Out of Memory

- Reduce context length: `-c 512` or `-c 256`
- Use TQ1_0 format instead of TQ2_0
- Use smaller model

---

## Environment Variables

```bash
# Force specific Vulkan device (if multiple GPUs)
export GGML_VK_DEVICE=0

# Disable GPU (CPU only)
export GGML_VK_DISABLE=1
```

---

## Backend Selection

| Backend | Best For | Command |
|---------|----------|---------|
| Vulkan | AMD, NVIDIA, Intel | Default (auto-detected) |
| SYCL | Intel Arc GPUs | Use SYCL build |
| CPU | Fallback | `-ngl 0` |

---

**[Back to Releases](../README.md)** | **[Main Documentation](../../README.md)**
