# Release Notes

## Version 1.0.0 (January 2026)

**First GPU Backend for BitNet b1.58**

This release introduces the first complete GPU backend for BitNet b1.58 models, enabling lossless ternary inference and LoRA fine-tuning across heterogeneous GPU platforms.

---

### Highlights

- **First GPU Backend for BitNet** - Vulkan-accelerated TQ2_0/TQ1_0 kernels
- **Lossless Inference** - 99.04% same-top-token rate with CPU reference
- **Cross-Platform Support** - Desktop (NVIDIA, AMD, Intel) and Mobile (Adreno, Mali, Apple)
- **LoRA Fine-tuning** - First complete training pipeline for BitNet on GPUs
- **Dynamic Tiling** - Enables stable operation on memory-constrained mobile GPUs

---

### New Features

#### GPU Inference

- Vulkan backend for TQ1_0 and TQ2_0 ternary formats
- Metal backend for Apple Silicon (M1/M2/M3/M4) and iOS (A-series)
- Up to 549 tok/s on NVIDIA RTX 4090 (TQ1_0)
- Up to 140 tok/s on mobile GPUs (Adreno 830)
- Lossless verification: KL divergence < 0.0003

#### LoRA Fine-tuning

- Complete forward/backward pass on GPU
- Instruction-tuning with assistant-only loss
- Checkpoint save/resume support
- Learning rate scheduling (cosine with warmup)
- All LoRA modules trainable (`--lora-modules "all"`)

#### Dynamic Tiling

- Automatic tile size calculation for memory-constrained GPUs
- Solves Adreno 128 MiB SSBO limit
- Enables models up to 7B on mobile devices
- Zero configuration required

#### Model Support

- 1bitLLM/bitnet_b1_58 family (125M - 3B)
- microsoft/bitnet-b1.58 (700M, 2B)
- Custom BitNet models via GGUF conversion

---

### Supported Platforms

| Platform | GPU | Backend | Status |
|----------|-----|---------|--------|
| Linux | NVIDIA RTX series | Vulkan | Fully Supported |
| Linux | AMD Radeon | Vulkan | Fully Supported |
| Linux | Intel Arc | Vulkan/SYCL | Fully Supported |
| macOS | Apple M1/M2/M3/M4 | Metal | Fully Supported |
| iOS | Apple A14+ | Metal | Fully Supported |
| Android | Qualcomm Adreno | Vulkan | Fully Supported |
| Android | ARM Mali | Vulkan | Fully Supported |

---

### Performance Summary

#### Inference (tokens/second)

| Model | NVIDIA 4090 (TQ1_0) | NVIDIA 4090 (TQ2_0) | Mobile Best |
|-------|---------------------|---------------------|-------------|
| 125M | 548.86 | 190.38 | 139.17 (Adreno) |
| 1B | 257.80 | 43.84 | 40.50 (Adreno) |
| 7B | 108.73 | 22.13 | 24.21 (iPhone) |
| 13B | 74.29 | 13.25 | 16.79 (iPhone) |

#### LoRA Training (time/epoch)

| Model | NVIDIA 4090 | macOS M3 | Mobile |
|-------|-------------|----------|--------|
| 1B | 3m 31s | 9m 19s | 1h 18m |
| 7B | 16m 20s | 35m 25s | 12h 24m |
| 13B | 27m 41s | 1h 03m | 23h 10m |

---

### VRAM Requirements

| Model | TQ1_0 | TQ2_0 |
|-------|-------|-------|
| 1B | 658 MiB | 1,266 MiB |
| 7B | 1,913 MiB | 4,331 MiB |
| 13B | 2,789 MiB | 6,835 MiB |

---

### Breaking Changes

None - this is the initial release.

---

### Known Issues

1. **Flash Attention** - Not supported on Vulkan backend. Use `--flash-attn off`.
2. **Multi-GPU** - Experimental. Single GPU recommended.
3. **Large Models on Mobile** - Models > 7B may cause OOM on mobile devices.
4. **iOS Background** - Training may pause when app is backgrounded.

---

### Upgrading

First release - no upgrade path required.

---

### Dependencies

- llama.cpp (extended with BitNet support)
- ggml (tensor computation library)
- Vulkan SDK 1.1+ (for Vulkan backend)
- Metal (for Apple platforms)

---

### Contributors

- Tether Data Team
- llama.cpp community
- BitNet research team (Microsoft Research)

---

### Links

- [Main Repository](https://github.com/tetherto/qvac-fabric-bitnet)
- [Documentation](./README.md)
- [Benchmarks](./docs/BENCHMARKS.md)
- [Issue Tracker](https://github.com/tetherto/qvac-fabric-bitnet/issues)

---

## Roadmap

### v1.1 (Planned)

- Flash attention support for Vulkan
- Multi-GPU training
- Windows pre-built binaries
- Additional BitNet model support (Falcon3-1.58bit)

### v1.2 (Planned)

- Quantization-aware training
- INT8 accumulator optimizations
- Mobile-specific kernel optimizations

---

<div align="center">
  <p><b>BitNet b1.58 GPU Backend v1.0</b></p>
  <p>Lossless ternary inference • Cross-platform • On-device training</p>
</div>
