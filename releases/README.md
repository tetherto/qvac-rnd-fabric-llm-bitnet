# BitNet GPU Pre-built Releases

Pre-built binaries for BitNet b1.58 GPU inference and LoRA fine-tuning across all supported platforms.

**Download from:** [GitHub Releases (b7334)](https://github.com/tetherto/qvac-fabric-llm.cpp/releases/tag/b7334)

---

## Platform Overview

| Platform | Architecture | Backend | Artifact |
|----------|--------------|---------|----------|
| Android | ARM64 (Adreno/Mali) | Vulkan | `llama-b7334-bin-android.zip` |
| iOS | ARM64 (Apple A-series) | Metal | `llama-b7334-xcframework.zip` |
| macOS | ARM64 (Apple Silicon) | Metal | `llama-b7334-bin-macos-arm64.zip` |
| macOS | x64 (Intel) | Metal | `llama-b7334-bin-macos-x64.zip` |
| Linux | x64 | CPU | `llama-b7334-bin-ubuntu-x64.zip` |
| Linux | x64 | Vulkan | `llama-b7334-bin-ubuntu-x64-vulkan.zip` |
| Windows | x64 | CPU | `llama-b7334-bin-win-x64.zip` |
| Windows | ARM64 | CPU | `llama-b7334-bin-win-arm64.zip` |
| Windows | x64 | Vulkan | `llama-b7334-bin-win-vulkan-x64.zip` |
| Windows | x64 | SYCL (Intel) | `llama-b7334-bin-win-sycl-x64.zip` |
| Windows | x64 | HIP (AMD) | `llama-b7334-bin-win-hip-x64.zip` |

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

## Download & Installation

### Android (Termux)

```bash
# In Termux
pkg update && pkg install wget unzip

# Download from GitHub Releases
wget https://github.com/tetherto/qvac-fabric-llm.cpp/releases/download/b7334/llama-b7334-bin-android.zip
unzip llama-b7334-bin-android.zip
cd llama-b7334-bin-android

# Set library path (required)
export LD_LIBRARY_PATH=.

# Test installation
./bin/llama-cli --version
```

**Requirements:**
- Android 10+ (API 29+)
- Vulkan 1.1 support
- Termux app installed
- 4GB+ RAM recommended

**Performance Notes:**
- **Adreno 830 (S25):** 15-140 tok/s depending on model size
- **Mali G715 (Pixel 9):** 0.8-38 tok/s depending on model size
- Models > 3.8B may require reduced context length (`-c 128`)

---

### macOS (Apple Silicon)

```bash
# Download from GitHub Releases
curl -LO https://github.com/tetherto/qvac-fabric-llm.cpp/releases/download/b7334/llama-b7334-bin-macos-arm64.zip
unzip llama-b7334-bin-macos-arm64.zip
cd llama-b7334-bin-macos-arm64

# Remove quarantine (if needed)
xattr -cr .

# Test installation
./bin/llama-cli --version
```

**Requirements:**
- macOS 13.0+ (Ventura or newer)
- Xcode Command Line Tools
- For Apple Silicon: M1 or newer
- For Intel: use `llama-b7334-bin-macos-x64.zip`

**Performance Notes:**
- **Apple M3 Pro:** 33-386 tok/s (TQ1_0), 6-124 tok/s (TQ2_0)
- **Apple M1:** ~70% of M3 Pro performance
- Models up to 13B supported on 16GB+ machines
- Unified memory enables larger models than discrete GPU systems

---

### iOS

```bash
# Download from GitHub Releases
curl -LO https://github.com/tetherto/qvac-fabric-llm.cpp/releases/download/b7334/llama-b7334-xcframework.zip
unzip llama-b7334-xcframework.zip
```

**Requirements:**
- iOS 16+
- iPhone 12 or newer recommended
- A14 Bionic or newer for optimal performance

**Performance Notes:**
- **iPhone 16 (A18):** 3-112 tok/s (TQ1_0), 3-36 tok/s (TQ2_0)
- **iPhone 14 Pro (A16):** Similar performance
- Models up to 7B supported
- Keep app in foreground during training

---

### Linux (Vulkan)

```bash
# Download from GitHub Releases
wget https://github.com/tetherto/qvac-fabric-llm.cpp/releases/download/b7334/llama-b7334-bin-ubuntu-x64-vulkan.zip
unzip llama-b7334-bin-ubuntu-x64-vulkan.zip
cd llama-b7334-bin-ubuntu-x64-vulkan

# Test installation
./bin/llama-cli --version

# Verify Vulkan
vulkaninfo | head -20
```

**Requirements:**
- Linux kernel 5.4+
- Vulkan 1.1+ drivers
- glibc 2.31+

**Vulkan Driver Installation:**

```bash
# NVIDIA
sudo apt install nvidia-driver-535 nvidia-utils-535

# AMD
sudo apt install mesa-vulkan-drivers

# Intel
sudo apt install intel-media-va-driver mesa-vulkan-drivers
```

**Performance Notes:**
- **NVIDIA RTX 4090:** 74-549 tok/s (TQ1_0), 13-190 tok/s (TQ2_0)
- **AMD 7900 XTX:** Similar to RTX 4090
- **Intel Arc A770:** ~50% of RTX 4090
- All model sizes supported with sufficient VRAM

---

### Windows

```powershell
# Download from GitHub Releases (use browser or PowerShell)
# Vulkan: llama-b7334-bin-win-vulkan-x64.zip
# CPU: llama-b7334-bin-win-x64.zip
# SYCL (Intel): llama-b7334-bin-win-sycl-x64.zip
# HIP (AMD): llama-b7334-bin-win-hip-x64.zip

# Extract and run
Expand-Archive llama-b7334-bin-win-vulkan-x64.zip -DestinationPath .
cd llama-b7334-bin-win-vulkan-x64
.\bin\llama-cli.exe --version
```

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

## Building from Source

If pre-built binaries don't work for your system:

```bash
# Clone repository
git clone https://github.com/tetherto/qvac-fabric-llm.cpp.git
cd qvac-fabric-llm.cpp

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

- [GitHub Issues](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/issues)
- [Discussions](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/discussions)

---

## Version History

| Version | Date | Notes |
|---------|------|-------|
| b7334 | March 2026 | Current release with TQ1_0/TQ2_0 support |

**[Full Release Notes](../RELEASE_NOTES.md)**

---

<div align="center">
  <p><b>BitNet b1.58 GPU Inference - Available Everywhere</b></p>
  <p><a href="https://github.com/tetherto/qvac-fabric-llm.cpp/releases/tag/b7334">Download from GitHub Releases</a></p>
</div>
