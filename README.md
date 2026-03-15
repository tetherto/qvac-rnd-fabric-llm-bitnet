<p align="center">
  <img src="https://img.shields.io/badge/Framework-llama.cpp%2FGGML-blue?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/BitNet%20b1.58-Ternary%20Inference-brightgreen?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/LoRA%20Fine--Tuning-Supported-green?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Backends-Vulkan%20%7C%20Metal-purple?style=for-the-badge"/>
</p>

<h1 align="center">LoRA Fine-Tuning BitNet b1.58 LLMs on Heterogeneous Edge GPUs via QVAC Fabric</h1>

<div align="center">
  <b>The first GPU backend for BitNet b1.58 with ternary inference and LoRA finetuning</b><br>
  <b>From smartphones to desktops • Cross-platform Vulkan acceleration • Metal backend acceleration • On-device LoRA training</b>
</div>

<p align="center">
  <a href="#quick-start">Quick Start</a> •
  <a href="#downloads">Downloads</a> •
  <a href="#performance-benchmarks">Benchmarks</a> •
  <a href="#research-highlights">Research</a> •
  <a href="#usage-examples">Usage</a>
</p>

---

## What Makes This Different?

* [**Tether**](https://tether.io/data/) AI Research introduces the **world’s first LoRA BitNet fine-tuning framework** on vendor-agnostic consumer GPUs on consumer and edge devices. It’s an extension of [**QVAC-fabric-llm.cpp**](https://huggingface.co/blog/qvac/fabric-llm-finetune) (based on [llama.cpp](https://github.com/ggml-org/llama.cpp)) that brings BitNet fine-tuning capabilities to the open-source community.

* The framework offers cross-platform LoRA fine-tuning support for BitNet models across heterogeneous consumer GPUs including AMD, Apple M3 Pro (GPU) and others.

* This marks the first successful demonstration of BitNet fine-tuning on mobile GPUs (Adreno, Mali, Apple Bionic GPU). Users can fine-tune 125M-parameter BitNet models in \~10 minutes on a Samsung S25. For larger models (such as the 1B BitNet), fine-tuning \~300 documents (\~18k tokens) completes in 1 hour 18 mins on the Samsung S25 (Adreno GPU) and 1 hour 45 minutes on the iPhone 16.

* The BitNet architecture implementation demonstrates the capability to fine-tune models that are ~2 times larger on edge devices compared to Q4 non-BitNet models, showcasing the memory advantage of the BitNet architecture.
    
* It was also demonsrated that inference of a Bitnet model is significantly faster, ranging from **2.1 to 11.3 times**, on **edge GPUs compared to CPUs** across leading flagship devices, including the Samsung S25, Google Pixel 9, and iPhone 16.
    
* Our benchmark shows that the BitNet-1B model uses up to 77.8% less VRAM than Gemma-3-1B (F16) and 65.6% less VRAM than Qwen3-0.6B (F16). This substantial reduction in memory usage makes it significantly more feasible to deploy larger models on memory-constrained edge devices. Further reinforcing this advantage, despite having **3.25 times** more parameters **(13B vs. 4B)**, **BitNet-13B** uses 29% less VRAM (2,789 MB) than a 4-bit quantized Qwen3-4B (Q4) model. This breakthrough enables **13B-scale models to run on edge hardware** that previously struggled with 4B quantized models without the need for additional memory or hardware upgrades.


| Platform | Hardware | Status |
|----------|----------|--------|
| Android | Qualcomm Adreno, ARM Mali | Fully Supported |
| iOS/macOS | Apple Silicon (A-series, M-series) | Fully Supported |
| Windows/Linux | AMD, Intel, NVIDIA GPUs | Fully Supported |

**Key Innovation:** Novel dynamic tiling algorithm enables stable inference and training on mobile GPUs with hardware memory constraints (128 MiB SSBO limit on Adreno).

---

## Research Highlights

This repository contains the implementation and artifacts for our research:

**["LoRA Fine-Tuning BitNet b1.58 LLMs on Heterogeneous Edge GPUs via QVAC Fabric"](https://huggingface.co/blog/qvac/fabric-llm-finetune-bitnet)**

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
| Download binaries | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Run inference | [Quick Start](#quick-start) |
| Fine-tune with LoRA | [Usage Examples](#usage-examples) |
| View benchmarks | [docs/BENCHMARKS.md](./docs/BENCHMARKS.md) |
| Evaluation scripts | [evaluations/scripts/](./evaluations/scripts/) |
| Experiment reports | [evaluations/reports/](./evaluations/reports/) |

---

## Repository Structure

```
qvac-fabric-llm-bitnet-finetune/
├── README.md                      # This file - main documentation
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
└── releases/                      # Release Documentation
    └── README.md                  # Links to GitHub Releases
```

> **Pre-built Binaries:** Download from [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)

---

## Quick Start

### Building from Source

```bash
# 1. Clone the BitNet-enabled llama.cpp fork
git clone https://github.com/tetherto/qvac-fabric-llm.cpp/
cd qvac-fabric-llm.cpp

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

Please download the latest binaries from the release page.

```bash
# Download Android build from GitHub Releases
# Artifact: llama-{latest-release}-bin-android.zip

# Set library path
export LD_LIBRARY_PATH= "YOUR LD LIBRARY PATH"

# Download BitNet model
mkdir -p models
wget https://huggingface.co/qvac/fabric-llm-finetune-bitnet/resolve/main/1bitLLM-bitnet_b1_58-xl-tq2_0.gguf \
  -O models/bitnet-xl.tq2_0.gguf

# Run inference
./bin/llama-cli \
  -m models/bitnet-xl.tq2_0.gguf \
  -ngl 99 -s 42 -c 512 --flash-attn off \
  -p "Explain the BitNet architecture in one paragraph." \
  -n 128
```

**[All Downloads](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)**

</details>

<details>
<summary><b>macOS (Apple Silicon)</b></summary>

Please download the latest binaries from the release page.

```bash
# Download macOS Apple Silicon build from GitHub Releases
# Artifact: llama-{latest-release}-bin-macos-arm64.zip

# Remove quarantine (if needed)
xattr -cr .

# Download BitNet model
mkdir -p models
curl -L https://huggingface.co/qvac/fabric-llm-finetune-bitnet/resolve/main/1bitLLM-bitnet_b1_58-xl-tq2_0.gguf \
  -o models/bitnet-xl.tq2_0.gguf

# Run inference
./bin/llama-cli \
  -m models/bitnet-xl.tq2_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "What are the advantages of 1-bit neural networks?" \
  -n 256
```

**[All Downloads](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)**

</details>

<details>
<summary><b>Linux/Windows (NVIDIA/AMD/Intel)</b></summary>

```bash
# Download Linux/Windows Vulkan build from GitHub Releases
# Artifact: llama-{latest-release}-bin-ubuntu-vulkan-x64.zip ; llama-{latest-release}-bin-win-vulkan-x64.zip

# Download BitNet model
mkdir -p models
curl https://huggingface.co/qvac/fabric-llm-finetune-bitnet/resolve/main/1bitLLM-bitnet_b1_58-xl-tq2_0.gguf \
  -O models/bitnet-xl.tq2_0.gguf

# Run inference
./bin/llama-cli \
  -m models/bitnet-xl.tq2_0.gguf \
  -ngl 999 -c 512 --flash-attn off \
  -p "Describe quantum computing in simple terms." \
  -n 256
```

**[All Downloads](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)**

</details>

---

## Downloads

Pre-built binaries from [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases):

| Platform | Hardware | Artifact | Download |
|----------|----------|----------|----------|
| Android | Adreno/Mali | `llama-{latest-release}-bin-android.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| macOS | Apple Silicon | `llama-{latest-release}-bin-macos-arm64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| macOS | Intel | `llama-{latest-release}-bin-macos-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| iOS | A-series | `llama-{latest-release}-xcframework.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Linux | Vulkan | `llama-{latest-release}-bin-ubuntu-x64-vulkan.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Linux | CPU | `llama-{latest-release}-bin-ubuntu-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Windows | Vulkan | `llama-{latest-release}-bin-win-vulkan-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Windows | CPU | `llama-{latest-release}-bin-win-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Windows | SYCL (Intel) | `llama-{latest-release}-bin-win-sycl-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |
| Windows | HIP (AMD) | `llama-{latest-release}-bin-win-hip-x64.zip` | [qvac-fabric-llm.cpp/releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases) |

### What's Included

Each download contains:
- `llama-cli` - Inference and interactive chat
- `llama-finetune-lora` - LoRA fine-tuning binary
- `llama-quantize` - Model quantization tool
- `llama-perplexity` - Model evaluation (KL divergence, perplexity)
- `llama-export-lora` - Export/merge LoRA adapters
- Required libraries (GGML, Vulkan/Metal backends)

**[All Releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)**

---

## Performance Benchmarks

### Inference Speed Highlights

| Model | NVIDIA 4090 | Apple M3 | iPhone 16 | Adreno 830 | Mali G715 |
|-------|-------------|----------|-----------|------------|-----------|
| **1B** (TQ1_0) | 258 tok/s | 111 tok/s | 131 tok/s | 27 tok/s | 8 tok/s |
| **7B** (TQ1_0) | 109 tok/s | 53 tok/s | 24 tok/s | OOM | 1.4 tok/s |
| **13B** (TQ1_0) | 74 tok/s | 34 tok/s | 17 tok/s | OOM | 0.8 tok/s |

### LoRA Fine-tuning Time (per epoch)

| Model | NVIDIA 4090 | Apple M3 | iPhone 16 | Adreno 830 |
|-------|-------------|----------|-----------|------------|
| **1B** | 3m 31s | 9m 19s | 1h 45m | 1h 18m |
| **7B** | 16m 20s | 35m 25s | 12h 24m | OOM |
| **13B** | 27m 41s | 1h 03m | 23h 10m | OOM |

### Memory Efficiency: BitNet vs Standard Models

BitNet's ternary weights dramatically reduce VRAM requirements:

| Model Size | BitNet TQ1_0 | BitNet TQ2_0 | Qwen3 Q4 | Qwen3 F16 |
|------------|--------------|--------------|----------|-----------|
| **~0.6B** | 249 MiB | 402 MiB | 1,102 MiB | 1,918 MiB |
| **~1.7B** | 1,027 MiB | 1,669 MiB | 2,025 MiB | 4,307 MiB |
| **~4B** | 1,221 MiB | 2,797 MiB | 3,942 MiB | 9,335 MiB |
| **~7B** | 1,913 MiB | 4,331 MiB | - | - |
| **~13B** | 2,789 MiB | 6,835 MiB | - | - |

**Key insight:** BitNet-13B (TQ1_0) at 2.8 GiB enables 13B-class LoRA fine-tuning on 4GB GPUs!

### Quality (LLM-as-Judge)

BitNet-2B vs Qwen3-1.7B (Q4): **60-64% win rate** for BitNet across all platforms, achieving competitive quality with ~3x less memory.

> [View complete benchmarks with all platforms and model sizes](./docs/BENCHMARKS.md)

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
│  Vulkan  │  Metal   │   Vulkan      │
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
- [Quick Start](#quick-start)
- [Downloads](#downloads)
- [GitHub Releases](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)

### Benchmarks & Evaluation
- [Detailed Benchmarks](./docs/BENCHMARKS.md)
- [Evaluation Guide](./evaluations/README.md)
- [Biomedical Fine-tuning Report](./evaluations/reports/BIOMED_FINETUNING_REPORT.md)

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
@article{bitnet-heterogeneous-gpu-2026,
  title={LoRA Fine-Tuning BitNet b1.58 LLMs on Heterogeneous Edge GPUs via QVAC Fabric},
  author={Subash SN, Akshay Nambiar, Milan Gritta, Zhen Cong Chen, Arsalan Anwari, Gianfranco Cordella, Amril Nurman},
  journal={HuggingFace Blog},
  year={2026},
  note={First GPU backend for BitNet b1.58 with Vulkan and Metal acceleration}
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

- [Project Repository](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune)
- [Release Downloads](https://github.com/tetherto/qvac-fabric-llm.cpp/releases)
- [Issue Tracker](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/issues)
- [Full Documentation](./docs/)

---

<div align="center">
  <h2>Bringing BitNet b1.58 to GPUs Everywhere</h2>
  <p><b>Lossless ternary inference • Cross-platform acceleration • On-device training</b></p>
  <p>Star this repo if you find it useful!</p>
</div>
