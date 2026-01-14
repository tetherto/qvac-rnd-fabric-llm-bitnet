# BitNet GPU Pre-built Releases

Pre-built binaries for BitNet b1.58 GPU inference and LoRA fine-tuning across all supported platforms.

---

## Platform Overview

| Platform | Architecture | Backend | Status | Recommended Models |
|----------|--------------|---------|--------|-------------------|
| [Android](#android) | ARM64 (Adreno/Mali) | Vulkan | Available | Up to 3.8B |
| [iOS](#ios) | ARM64 (Apple A-series) | Metal | Available | Up to 7B |
| [macOS](#macos) | ARM64 (Apple Silicon) | Metal | Available | Up to 13B |
| [Linux](#linux) | x64/ARM64 | Vulkan/SYCL | Available | Up to 13B+ |
| Windows | x64 | Vulkan | Coming Soon | Up to 13B+ |

---

## Quick Selection Guide

### By Use Case

| Use Case | Recommended Platform | Format | Notes |
|----------|---------------------|--------|-------|
| **Fastest Inference** | Linux (NVIDIA/AMD) | TQ1_0 | 500+ tok/s on RTX 4090 |
| **Mobile Inference** | Android/iOS | TQ1_0 | 15-140 tok/s |
| **LoRA Fine-tuning** | Linux/macOS | TQ2_0 | Better numerical stability |
| **Low Memory** | Any | TQ1_0 | ~40% less VRAM |
| **Maximum Quality** | Any | TQ2_0 | Improved stability |

### By Model Size

| Model Size | Android (6GB) | iOS (8GB) | macOS (16GB) | Desktop (24GB) |
|------------|---------------|-----------|--------------|----------------|
| 125M - 1B | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 |
| 1.5B - 3.8B | TQ1_0 | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 |
| 7B | OOM | TQ1_0 | TQ1_0 or TQ2_0 | TQ1_0 or TQ2_0 |
| 13B | OOM | OOM | TQ1_0 | TQ1_0 or TQ2_0 |

---

## Android

### Builds Available

| Build | GPU Support | Download |
|-------|-------------|----------|
| `qvac-android-adreno-arm64-v1.0.zip` | Qualcomm Adreno | [Download](./android/) |
| `qvac-android-mali-arm64-v1.0.zip` | ARM Mali | [Download](./android/) |

### Requirements

- Android 10+ (API 29+)
- Vulkan 1.1 support
- Termux app installed
- 4GB+ RAM recommended

### Installation

```bash
# In Termux
pkg update && pkg install wget unzip

# Download and extract
wget https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-android-adreno-arm64-v1.0.zip
unzip qvac-android-adreno-arm64-v1.0.zip
cd qvac-android-adreno-arm64-v1.0

# Set library path (required)
export LD_LIBRARY_PATH=.

# Test installation
./bin/llama-cli --version
```

### Performance Notes

- **Adreno 830 (S25):** 15-140 tok/s depending on model size
- **Mali G715 (Pixel 9):** 0.8-38 tok/s depending on model size
- Models > 3.8B may require reduced context length (`-c 128`)

**[Full Android Guide](./android/README.md)**

---

## iOS

### Builds Available

| Build | GPU Support | Download |
|-------|-------------|----------|
| `qvac-ios-v1.0.zip` | Apple A-series (Metal) | [Download](./ios/) |

### Requirements

- iOS 16+
- iPhone 12 or newer recommended
- A14 Bionic or newer for optimal performance

### Performance Notes

- **iPhone 16 (A18):** 3-112 tok/s (TQ1_0), 3-36 tok/s (TQ2_0)
- **iPhone 14 Pro (A16):** Similar performance
- Models up to 7B supported
- Keep app in foreground during training

**[Full iOS Guide](./ios/README.md)**

---

## macOS

### Builds Available

| Build | Hardware | Download |
|-------|----------|----------|
| `qvac-macos-apple-silicon-v1.0.zip` | M1/M2/M3/M4 | [Download](./macos/) |
| `qvac-macos-intel-v1.0.zip` | Intel x64 | [Download](./macos/) |

### Requirements

- macOS 13.0+ (Ventura or newer)
- Xcode Command Line Tools
- For Apple Silicon: M1 or newer
- For Intel: macOS with Metal support

### Installation

```bash
# Download
curl -L https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-macos-apple-silicon-v1.0.zip \
  -o qvac-macos.zip

# Extract
unzip qvac-macos.zip
cd qvac-macos-apple-silicon-v1.0

# Remove quarantine (if needed)
xattr -cr .

# Test installation
./bin/llama-cli --version
```

### Performance Notes

- **Apple M3 Pro:** 33-386 tok/s (TQ1_0), 6-124 tok/s (TQ2_0)
- **Apple M1:** ~70% of M3 Pro performance
- Models up to 13B supported on 16GB+ machines
- Unified memory enables larger models than discrete GPU systems

**[Full macOS Guide](./macos/README.md)**

---

## Linux

### Builds Available

| Build | GPU Support | Download |
|-------|-------------|----------|
| `qvac-linux-vulkan-x64-v1.0.zip` | AMD/NVIDIA/Intel (Vulkan) | [Download](./linux/) |
| `qvac-linux-arm64-v1.0.zip` | ARM64 CPU | [Download](./linux/) |
| `qvac-linux-sycl-intel-v1.0.zip` | Intel GPU (SYCL) | [Download](./linux/) |

### Requirements

- Linux kernel 5.4+
- Vulkan 1.1+ drivers
- glibc 2.31+

### Vulkan Driver Installation

```bash
# NVIDIA
sudo apt install nvidia-driver-535 nvidia-utils-535

# AMD
sudo apt install mesa-vulkan-drivers

# Intel
sudo apt install intel-media-va-driver mesa-vulkan-drivers
```

### Installation

```bash
# Download
wget https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-linux-vulkan-x64-v1.0.zip

# Extract
unzip qvac-linux-vulkan-x64-v1.0.zip
cd qvac-linux-vulkan-x64-v1.0

# Test installation
./bin/llama-cli --version

# Verify Vulkan
vulkaninfo | head -20
```

### Performance Notes

- **NVIDIA RTX 4090:** 74-549 tok/s (TQ1_0), 13-190 tok/s (TQ2_0)
- **AMD 7900 XTX:** Similar to RTX 4090
- **Intel Arc A770:** ~50% of RTX 4090
- All model sizes supported with sufficient VRAM

**[Full Linux Guide](./linux/README.md)**

---

## Included Binaries

Each release package contains:

| Binary | Description |
|--------|-------------|
| `llama-cli` | Interactive inference and chat |
| `llama-finetune-lora` | LoRA adapter fine-tuning |
| `llama-quantize` | Model quantization (convert to TQ1_0/TQ2_0) |
| `llama-perplexity` | Model evaluation (perplexity, KL divergence) |
| `llama-export-lora` | Export/merge LoRA adapters |
| `libggml.*` | GGML tensor library |
| `libllama.*` | LLaMA inference library |

---

## Verifying Downloads

All releases include SHA256 checksums:

```bash
# Verify checksum
sha256sum -c checksums.txt

# Or manually
sha256sum qvac-linux-vulkan-x64-v1.0.zip
# Compare with published checksum
```

---

## Building from Source

If pre-built binaries don't work for your system:

```bash
# Clone repository
git clone https://github.com/tetherto/qvac-fabric-llm.cpp.git
cd qvac-fabric-llm.cpp
git checkout bitnet-gpu

# Configure (choose your backend)
# Vulkan (cross-platform)
cmake -B build -DLLAMA_VULKAN=ON

# Metal (macOS/iOS)
cmake -B build -DLLAMA_METAL=ON

# CUDA (NVIDIA)
cmake -B build -DLLAMA_CUDA=ON

# Build
cmake --build build -j$(nproc)
```

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| `libvulkan.so not found` | Install Vulkan drivers for your GPU |
| `DeviceLoss` on mobile | Reduce context length (`-c 128`) |
| `OOM` errors | Use smaller model or TQ1_0 format |
| Slow inference | Ensure GPU layers enabled (`-ngl 999`) |
| macOS quarantine | Run `xattr -cr .` on extracted folder |

### Getting Help

- [GitHub Issues](https://github.com/tetherto/qvac-fabric-bitnet/issues)
- [Discussions](https://github.com/tetherto/qvac-fabric-bitnet/discussions)

---

## Version History

| Version | Date | Notes |
|---------|------|-------|
| v1.0 | January 2026 | Initial release with TQ1_0/TQ2_0 support |

**[Full Release Notes](../RELEASE_NOTES.md)**

---

<div align="center">
  <p><b>BitNet b1.58 GPU Inference - Available Everywhere</b></p>
</div>
