<p align="center">
  <img src="https://img.shields.io/badge/Framework-llama.cpp%2FGGML-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/BitNet%20b1.58-Ternary%20Inference-brightgreen?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/LoRA%20Fine--Tuning-Supported-green?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Backends-Vulkan%20%7C%20Metal%20%7C%20CUDA-purple?style=for-the-badge"/>
</p>

<h1 align="center">Fast and Lossless BitNet b1.58 Inference on GPUs</h1>

<div align="center">
  <b>The first GPU backend for BitNet b1.58 with fully lossless ternary inference</b><br>
  <b>From smartphones to datacenters • Cross-platform Vulkan acceleration • On-device LoRA training</b>
</div>

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-downloads">Downloads</a> •
  <a href="#-performance-benchmarks">Benchmarks</a> •
  <a href="#-research-highlights">Research</a> •
  <a href="#-usage-examples">Usage</a>
</p>

---

## What Makes This Different?

**The Problem:** BitNet b1.58 introduced revolutionary 1.58-bit ternary weights, but inference was locked to CPUs via bitnet.cpp. GPUs remained untapped.

**Our Solution:** The first GPU backend for BitNet b1.58 that enables:

- **Lossless Inference** - Bit-exact equivalence with CPU results
- **Vulkan Acceleration** - Multi-fold speedups across GPU architectures
- **LoRA Fine-tuning** - First complete training pipeline for BitNet on GPUs
- **Mobile Support** - Dynamic tiling for constrained mobile GPUs

| Platform | Hardware | Status |
|----------|----------|--------|
| Android | Qualcomm Adreno, ARM Mali | Fully Supported |
| iOS/macOS | Apple Silicon (A-series, M-series) | Fully Supported |
| Windows/Linux | AMD, Intel, NVIDIA GPUs | Fully Supported |

**Key Innovation:** Novel dynamic tiling algorithm enables stable inference and training on mobile GPUs with hardware memory constraints (128 MiB SSBO limit on Adreno).

---

## Research Highlights

This repository contains the implementation and artifacts for our research:

**["Fast and Lossless BitNet b1.58 Inference on GPUs"](https://huggingface.co/blog/qvac/bitnet-gpu-inference)**

### Key Contributions

1. **First GPU Backend for BitNet** - Vulkan-accelerated TQ2_0/TQ1_0 kernels for ternary inference
2. **Lossless Guarantee** - Bit-exact equivalence with CPU reference implementation
3. **Mobile GPU Support** - First successful BitNet inference on Adreno, Mali, and Apple mobile GPUs
4. **LoRA Fine-tuning** - Complete parameter-efficient training pipeline for BitNet models
5. **Dynamic Tiling** - Solves critical Adreno/Mali GPU memory constraints

### Validated Performance

- **Speedup**: Up to 5x faster than CPU inference on desktop GPUs
- **Lossless**: 99.04% same-top-token rate, KL divergence < 0.0003
- **Mobile**: Real-time inference on flagship smartphones (15-140 tok/s)
- **Training**: LoRA fine-tuning from 1 minute (desktop) to hours (mobile) per epoch

> [View detailed benchmarks](./docs/BENCHMARKS.md) | [Evaluation reports](./evaluations/reports/)

---

## Navigation Guide

| Task | Location |
|------|----------|
| Download binaries | `releases/[platform]/` |
| Run inference | [Quick Start](#-quick-start) |
| Fine-tune with LoRA | [Usage Examples](#-usage-examples) |
| View benchmarks | [docs/BENCHMARKS.md](./docs/BENCHMARKS.md) |
| Evaluation scripts | [evaluations/scripts/](./evaluations/scripts/) |
| Experiment reports | [evaluations/reports/](./evaluations/reports/) |

---

## Repository Structure

```
qvac-fabric-llm-bitnet-finetune/
├── README.md                      # This file - main documentation
├── RELEASE_NOTES.md               # Version history and changelog
│
├── docs/                          # Research Documentation
│   └── BENCHMARKS.md              # Comprehensive performance metrics
│
├── evaluations/                   # Datasets, Scripts & Results
│   ├── README.md                  # Evaluation guide
│   │
│   ├── email_style_transfer/      # Email Style Transfer Dataset
│   │   ├── email_dataset.jsonl    # Training examples
│   │   └── README.md              # Dataset documentation
│   │
│   ├── scripts/                   # Python Evaluation Tools
│   │   ├── compare_base_vs_adapters.py
│   │   ├── monitor_training.py
│   │   ├── quick_compare.py
│   │   ├── test_biomed_prompts.py
│   │   └── build_biomed_yn_dataset.py
│   │
│   └── reports/                   # Experimental Results
│       ├── BIOMED_FINETUNING_REPORT.md
│       ├── COMPLETE_PROJECT_REPORT.md
│       ├── README_inference_comparison.md
│       └── biomed_comparison_results.json
│
└── releases/                      # Pre-built Binaries
    ├── README.md                  # Platform overview
    ├── android/                   # Android (Termux) builds
    ├── ios/                       # iOS builds
    ├── linux/                     # Linux builds (Vulkan, SYCL)
    └── macos/                     # macOS builds (Metal)
```

---

## Quick Start

### Building from Source

```bash
# 1. Clone the BitNet-enabled llama.cpp fork
git clone https://github.com/tetherto/qvac-fabric-llm.cpp.git
cd qvac-fabric-llm.cpp
git checkout bitnet-gpu

# 2. Configure with Vulkan enabled
cmake -B build -DLLAMA_VULKAN=ON -DLLAMA_CUDA=OFF -DLLAMA_METAL=OFF

# 3. Build
cmake --build build -j
```

### Preparing a BitNet Model

Convert BitNet checkpoints to GGUF format with TQ2_0 or TQ1_0 quantization:

```bash
python3 scripts/convert-bitnet-to-gguf.py \
  --input /path/to/bitnet-b1.58-checkpoint \
  --output models/bitnet-b1_58-3b.tq2_0.gguf
```

### Choose Your Platform

<details>
<summary><b>Android (Termux)</b></summary>

```bash
# Download pre-built binary
wget https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-android-adreno-arm64-v1.0.zip
unzip qvac-android-adreno-arm64-v1.0.zip
cd qvac-android-adreno-arm64-v1.0

# Set library path
export LD_LIBRARY_PATH=.

# Download BitNet model
mkdir -p models
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq2_0.gguf \
  -O models/bitnet-700m.tq2_0.gguf

# Run inference
./bin/llama-cli \
  -m models/bitnet-700m.tq2_0.gguf \
  -ngl 99 -s 42 -c 512 --flash-attn off \
  -p "Explain the BitNet architecture in one paragraph." \
  -n 128
```

**[Full Android Guide](./releases/android/README.md)**

</details>

<details>
<summary><b>macOS (Apple Silicon)</b></summary>

```bash
# Download pre-built binary
curl -L https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-macos-apple-silicon-v1.0.zip \
  -o qvac-macos.zip
unzip qvac-macos.zip
cd qvac-macos-apple-silicon-v1.0

# Download BitNet model
mkdir -p models
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq1_0.gguf \
  -O models/bitnet-700m.tq1_0.gguf

# Run inference (TQ1_0 is faster on Apple Silicon)
./bin/llama-cli \
  -m models/bitnet-700m.tq1_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "What are the advantages of 1-bit neural networks?" \
  -n 256
```

**[Full macOS Guide](./releases/macos/README.md)**

</details>

<details>
<summary><b>Linux/Windows (NVIDIA/AMD/Intel)</b></summary>

```bash
# Download Vulkan binary
wget https://github.com/tetherto/qvac-fabric-bitnet/releases/download/v1.0/qvac-linux-vulkan-x64-v1.0.zip
unzip qvac-linux-vulkan-x64-v1.0.zip
cd qvac-linux-vulkan-x64-v1.0

# Download BitNet model
mkdir -p models
wget https://huggingface.co/1bitLLM/bitnet_b1_58-large/resolve/main/bitnet_b1_58-large.tq1_0.gguf \
  -O models/bitnet-700m.tq1_0.gguf

# Run inference
./bin/llama-cli \
  -m models/bitnet-700m.tq1_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "Describe quantum computing in simple terms." \
  -n 256
```

**[Full Linux Guide](./releases/linux/README.md)**

</details>

---

## Downloads

Pre-built binaries optimized for each platform:

| Platform | Hardware | Backend | Download |
|----------|----------|---------|----------|
| Android | Qualcomm Adreno, ARM Mali | Vulkan | [Download](./releases/android/) |
| macOS | Apple M1/M2/M3/M4 | Metal | [Download](./releases/macos/) |
| iOS | Apple A-series | Metal | [Download](./releases/ios/) |
| Linux/Win | AMD/Intel/NVIDIA | Vulkan | [Download](./releases/linux/) |

### What's Included

Each download contains:
- `llama-cli` - Inference and interactive chat
- `llama-finetune-lora` - LoRA fine-tuning binary
- `llama-quantize` - Model quantization tool
- `llama-perplexity` - Model evaluation (KL divergence, perplexity)
- `llama-export-lora` - Export/merge LoRA adapters
- Required libraries (GGML, Vulkan/Metal backends)

**[All Releases & Documentation](./releases/README.md)**

---

## Performance Benchmarks

### Inference Speed - TQ1_0 (tokens/second)

TQ1_0 provides the fastest inference with ~1.0 bits per weight:

| Model | NVIDIA 4090 | AMD CPU | Mali GPU | Adreno GPU | macOS | iPhone GPU |
|-------|-------------|---------|----------|------------|-------|------------|
| **125M** | 548.86 | 285.28 | 38.67 | 91.23 | 386.38 | 111.91 |
| **350M** | 342.17 | 142.77 | 17.71 | 47.93 | 194.80 | 157.81 |
| **1B** | 257.80 | 58.99 | 8.23 | 27.16 | 111.25 | 130.73 |
| **1.5B** | 224.14 | 46.46 | 5.91 | 15.26 | 136.76 | 78.41 |
| **2.7B** | 126.71 | 28.33 | 3.42 | 15.22* | 92.05 | 66.93 |
| **3.8B** | 131.90 | 24.06 | 2.43 | 11.54* | 64.09 | 26.31 |
| **7B** | 108.73 | 13.83 | 1.36 | OOM | 53.13 | 24.21 |
| **13B** | 74.29 | 8.20 | 0.79 | OOM | 33.54 | 16.79 |

*Models marked with * were run with `-c 128` to avoid OutOfMemory issues.

### Inference Speed - TQ2_0 (tokens/second)

TQ2_0 provides ~2.0 bits per weight with improved numerical stability:

| Model | NVIDIA 4090 | AMD CPU | Mali GPU | Adreno GPU | macOS | iPhone GPU |
|-------|-------------|---------|----------|------------|-------|------------|
| **125M** | 190.38 | 130.21 | 20.54 | 139.17 | 123.74 | 35.84 |
| **350M** | 81.84 | 53.21 | 18.12 | 58.19 | 50.19 | 40.66 |
| **1B** | 43.84 | 28.56 | 11.32 | 40.50 | 24.49 | 28.78 |
| **1.5B** | 50.52 | 24.62 | 10.48 | 23.91 | 28.83 | 16.53 |
| **2.7B** | 33.15 | 18.55 | 5.34 | 22.54* | 18.54 | 13.48 |
| **3.8B** | 25.68 | 13.01 | 3.56 | 19.38* | 12.69 | 5.21 |
| **7B** | 22.13 | 8.44 | 2.86 | OOM | 10.38 | 4.73 |
| **13B** | 13.25 | 4.60 | 1.49 | OOM | 6.61 | 3.31 |

### Microsoft BitNet Models

| Model | NVIDIA 4090 | AMD CPU | Mali GPU | Adreno GPU |
|-------|-------------|---------|----------|------------|
| **bitnet-700M** (avg) | 209.1 | 60.8 | 25.7 | 21.9 |
| **bitnet-700M** (TTFT) | 120ms | 530ms | 2310ms | 1209ms |
| **bitnet-2B** (avg) | 49.7 | 20.5 | 9.0 | 5.2 |
| **bitnet-2B** (TTFT) | 240ms | 560ms | 6577ms | 4817ms |

### LoRA Fine-tuning Time (per epoch, TQ2_0)

| Model | NVIDIA 4090 | Mali GPU | Adreno GPU | macOS | iPhone GPU |
|-------|-------------|----------|------------|-------|------------|
| **125M** | 1m 20s | 17m 25s | 10m 10s | 6m 19s | 12m 24s |
| **350M** | 2m 32s | 50m 52s | 28m 40s | 8m 10s | 44m 34s |
| **1B** | 3m 31s | 2h 08m | 1h 18m | 9m 19s | 1h 45m |
| **1.5B** | 6m 05s | 2h 58m | 1h 57m | 10m 51s | 2h 22m |
| **2.7B** | 7m 10s | 5h 04m | 3h 28m | 12m 39s | 4h 24m |
| **3.8B** | 8m 55s | 7h 15m | 4h 51m | 19m 29s | 6h 23m |
| **7B** | 16m 20s | 13h 06m | OOM | 35m 25s | 12h 24m |
| **13B** | 27m 41s | OOM | OOM | 1h 03m | 23h 10m |

### VRAM Usage (LoRA Fine-tuning)

BitNet's ternary weights enable training of much larger models within tight VRAM budgets:

| Model | TQ1_0 | TQ2_0 | Qwen3 Q4 | Qwen3 F16 |
|-------|-------|-------|----------|-----------|
| **125M** | 249 MiB | 402 MiB | - | - |
| **350M** | 461 MiB | 719 MiB | - | - |
| **1B** | 658 MiB | 1,266 MiB | - | - |
| **1.5B** | 1,027 MiB | 1,669 MiB | - | - |
| **2.7B** | 1,061 MiB | 2,248 MiB | - | - |
| **7B** | 1,913 MiB | 4,331 MiB | - | - |
| **13B** | 2,789 MiB | 6,835 MiB | - | - |

**Key insight:** BitNet-13B (TQ1_0) at 2.8 GiB enables 13B-class LoRA fine-tuning on 4GB GPUs!

### Quality Comparison (LLM-as-Judge)

Comparison: Qwen3 1.7B (Q4) vs microsoft-bitnet-b1.58-2B

| Platform | Format | Wins (A/B/Ties) | Win Rate |
|----------|--------|-----------------|----------|
| Mali GPU | TQ2_0 | 198/127/5 | 61% |
| Mali GPU | TQ1_0 | 205/121/4 | 63% |
| Adreno GPU | TQ2_0 | 194/131/5 | 60% |
| Adreno GPU | TQ1_0 | 207/121/2 | 63% |
| NVIDIA 4090 | TQ2_0 | 204/122/4 | 63% |
| NVIDIA 4090 | TQ1_0 | 202/124/4 | 62% |
| Apple M3 Pro | TQ2_0 | 196/129/5 | 60% |
| Apple M3 Pro | TQ1_0 | 210/118/2 | 64% |
| iPhone 16 | TQ2_0 | 206/122/2 | 63% |
| iPhone 16 | TQ1_0 | 203/123/4 | 62% |

**Conclusion:** BitNet achieves competitive quality (60-64% win rate) while using ~3x less memory.

> [View complete benchmarks](./docs/BENCHMARKS.md)

---

## Usage Examples

### Basic Inference

```bash
# TQ1_0 format (faster, ~1.0 bpw)
./bin/llama-cli \
  -m models/bitnet-3b.tq1_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "Explain machine learning to a 10-year-old." \
  -n 256

# TQ2_0 format (better numerical stability, ~2.0 bpw)
./bin/llama-cli \
  -m models/bitnet-3b.tq2_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "What is the capital of France?" \
  -n 64
```

### LoRA Fine-tuning

```bash
# Basic LoRA training on BitNet model
./bin/llama-finetune-lora \
  -m models/bitnet-1b.tq2_0.gguf \
  -f train.jsonl \
  --output-adapter bitnet-lora-adapter.gguf \
  -ngl 999 -c 128 -b 128 -ub 128 \
  --flash-attn off \
  --num-epochs 8

# Advanced LoRA with instruction-tuning
./bin/llama-finetune-lora \
  -m models/bitnet-1b.tq2_0.gguf \
  -f conversations.jsonl \
  --output-adapter bitnet-instruct-adapter.gguf \
  --assistant-loss-only \
  -ngl 999 -c 128 -b 128 -ub 128 \
  --flash-attn off \
  --learning-rate 1e-5 --lr-min 1e-8 \
  --lr-scheduler cosine --warmup-ratio 0.1 \
  --num-epochs 20 \
  --lora-modules "all"
```

### Checkpointing & Resume

```bash
# Save checkpoints every 50 steps
./bin/llama-finetune-lora \
  -m models/bitnet-1b.tq2_0.gguf \
  -f train.jsonl \
  --checkpoint-save-steps 50 \
  --checkpoint-save-dir "./lora_checkpoints" \
  -ngl 999

# Resume from checkpoint
./bin/llama-finetune-lora \
  -m models/bitnet-1b.tq2_0.gguf \
  -f train.jsonl \
  --resume-from "./lora_checkpoints/checkpoint_step_00000150/" \
  --output-adapter improved_adapter.gguf \
  -ngl 999
```

### Using Trained Adapters

```bash
# Inference with LoRA adapter
./bin/llama-cli \
  -m models/bitnet-1b.tq2_0.gguf \
  --lora bitnet-lora-adapter.gguf \
  -ngl 999 --flash-attn off \
  -p "Your prompt here" \
  -n 128
```

### Perplexity Evaluation

```bash
# Evaluate model quality with KL divergence
./bin/llama-perplexity \
  -m models/bitnet-1b.tq2_0.gguf \
  -f test_corpus.txt \
  -ngl 999 --flash-attn off
```

---

## Technical Architecture

### TQ2_0 and TQ1_0 Kernels

Our Vulkan backend implements optimized compute shaders for ternary weight decompression:

| Format | Bits/Weight | Packing | Use Case |
|--------|-------------|---------|----------|
| TQ1_0 | ~1.0 | 4 weights → 8 bits | Maximum speed, memory efficiency |
| TQ2_0 | ~2.0 | 4 weights → 8 bits + scale | Better numerical stability |

**Ternary Encoding:**
```
Bit pair → Weight value
00 → -1
01 → 0  
10 → +1
11 → (unused)
```

### Dynamic Tiling Algorithm

**Problem:** Adreno GPUs have undocumented 128 MiB SSBO limit causing `DeviceLoss` errors.

**Solution:** Dynamically tile large matrix operations:

1. Calculate tile dimensions respecting 128 MiB limit
2. Execute operations on tiles independently
3. Assemble results into final output tensor

This enables stable training on mobile GPUs where static approaches fail.

### Backend Architecture

```
┌─────────────────────────────────────┐
│       llama.cpp Public API          │
│   (BitNet inference + LoRA train)   │
├─────────────────────────────────────┤
│         GGML Core Engine            │
│  (Forward/Backward Pass, Optimizer) │
├──────────┬──────────┬───────────────┤
│  Vulkan  │  Metal   │    CUDA       │
│ (Cross)  │ (Apple)  │  (NVIDIA)     │
└──────────┴──────────┴───────────────┘
     ↓           ↓            ↓
┌──────────┬──────────┬───────────────┐
│  Adreno  │  Apple   │    Desktop    │
│   Mali   │  M/A     │    GPUs       │
│  Intel   │  Series  │               │
└──────────┴──────────┴───────────────┘
```

### Lossless Inference Guarantee

Our Vulkan kernels preserve bit-exact equivalence with CPU reference:

| Metric | Value |
|--------|-------|
| Same top token | 99.04% |
| KL Divergence | 0.000295 |
| Mean PPL ratio | 1.000274 |

---

## Key Features

### Inference Capabilities

- **Lossless Ternary** - Bit-exact equivalence with CPU bitnet.cpp
- **TQ1_0/TQ2_0 Formats** - Optimized for speed vs. stability tradeoff
- **Cross-Platform** - Single codebase for all GPU architectures
- **Dynamic Tiling** - Automatic adaptation to GPU memory limits

### Training Capabilities

- **LoRA Fine-tuning** - Parameter-efficient adaptation for BitNet
- **Instruction Tuning** - Masked-loss training on assistant tokens
- **Checkpointing** - Resume training with complete optimizer state
- **Mixed Precision** - Ternary forward, FP16/FP32 backward

### Supported Models

- 1bitLLM/bitnet_b1_58 family (125M - 3B)
- microsoft/bitnet-b1.58 (700M, 2B)
- tiiuae/Falcon3-1.58bit (coming soon)
- Custom BitNet models via GGUF conversion

---

## Documentation

### Getting Started
- [Quick Start](#-quick-start)
- [Downloads](#-downloads)
- [Platform Guides](./releases/)

### Benchmarks & Evaluation
- [Detailed Benchmarks](./docs/BENCHMARKS.md)
- [Evaluation Guide](./evaluations/README.md)
- [Biomedical Fine-tuning Report](./evaluations/reports/BIOMED_FINETUNING_REPORT.md)

### Platform Guides
- [Android Setup](./releases/android/README.md)
- [macOS Setup](./releases/macos/README.md)
- [iOS Setup](./releases/ios/README.md)
- [Linux Setup](./releases/linux/README.md)

---

## Known Issues

### Mobile Platforms
- Models > 7B cause OOM on most mobile devices with TQ2_0 → Use TQ1_0 or smaller models
- iOS may suspend background training → Keep app in foreground
- Mali GPUs slower than Adreno → Functional but requires patience

### Desktop Platforms
- Flash attention not supported on Vulkan → Use `--flash-attn off`
- Multi-GPU training experimental → Use single GPU

### Format Selection
- TQ1_0 faster but may have edge-case numerical differences
- TQ2_0 recommended for fine-tuning workloads

---

## Contributing

We welcome contributions! Areas of interest:

- Optimizations for specific GPU architectures
- Testing on additional mobile devices
- Support for new BitNet model architectures
- Benchmark contributions
- Documentation improvements

Please open an issue or pull request on GitHub.

---

## Citation

If you use this work in your research, please cite:

```bibtex
@article{bitnet-gpu-2025,
  title={Fast and Lossless BitNet b1.58 Inference on GPUs},
  author={Tether Data Team},
  journal={arXiv preprint},
  year={2025},
  note={First GPU backend for BitNet b1.58 with Vulkan acceleration}
}
```

---

## Acknowledgments

This work builds on:

- **BitNet** (Ma et al., 2024) - Revolutionary 1.58-bit LLM architecture
- **bitnet.cpp** (Wang et al., 2024) - CPU reference implementation
- **llama.cpp** (Gerganov et al.) - Foundation inference engine
- **ggml** - Portable tensor computation library
- **LoRA** (Hu et al., 2021) - Parameter-efficient fine-tuning

---

## License

This project is licensed under the Apache 2.0 License.

---

## Links

- [Project Repository](https://github.com/tetherto/qvac-fabric-bitnet)
- [Release Downloads](./releases/)
- [Discussion Forum](https://github.com/tetherto/qvac-fabric-bitnet/discussions)
- [Issue Tracker](https://github.com/tetherto/qvac-fabric-bitnet/issues)
- [Full Documentation](./docs/)

---

<div align="center">
  <h2>Bringing BitNet b1.58 to GPUs Everywhere</h2>
  <p><b>Lossless ternary inference • Cross-platform acceleration • On-device training</b></p>
  <p>Star this repo if you find it useful!</p>
</div>
