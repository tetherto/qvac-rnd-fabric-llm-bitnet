# BitNet GPU - iOS Setup Guide

Pre-built binaries for BitNet b1.58 inference and LoRA fine-tuning on iOS devices.

---

## Supported Hardware

| Device | Chip | Status | Notes |
|--------|------|--------|-------|
| iPhone 16 Pro | A18 Pro | Fully Supported | Best performance |
| iPhone 16 | A18 | Fully Supported | Excellent performance |
| iPhone 15 Pro | A17 Pro | Fully Supported | Great performance |
| iPhone 14 Pro | A16 | Fully Supported | Good performance |
| iPhone 13 Pro | A15 | Fully Supported | Good performance |
| iPhone 12 | A14 | Supported | Minimum recommended |

---

## Requirements

- iOS 16.0 or later
- A14 Bionic or newer chip
- 6GB+ RAM for optimal performance

---

## Available Builds

| Build | Description | Download |
|-------|-------------|----------|
| `qvac-ios-v1.0.zip` | Universal iOS build (Metal) | [Download](https://github.com/tetherto/qvac-fabric-llm-bitnet-finetune/releases/download/v1.0/qvac-ios-v1.0.zip) |

---

## Installation

The iOS build is provided as a framework/library for integration into iOS apps. For standalone usage, please use the macOS build or integrate into an iOS application.

### Framework Integration

```swift
// Add to your Xcode project
import BitNetGPU

// Initialize model
let model = try BitNetModel(path: "bitnet-700m.tq1_0.gguf")

// Run inference
let response = try model.generate(prompt: "Hello, world!", maxTokens: 128)
```

---

## Performance

### Inference Speed (tokens/second)

| Model | iPhone 16 (TQ1_0) | iPhone 16 (TQ2_0) |
|-------|-------------------|-------------------|
| 125M | 111.91 | 35.84 |
| 350M | 157.81 | 40.66 |
| 1B | 130.73 | 28.78 |
| 1.5B | 78.41 | 16.53 |
| 7B | 24.21 | 4.73 |
| 13B | 16.79 | 3.31 |

### LoRA Training Time (per epoch)

| Model | iPhone 16 |
|-------|-----------|
| 125M | 12m 24s |
| 1B | 1h 45m |
| 7B | 12h 24m |
| 13B | 23h 10m |

---

## Important Notes

### Background Execution

iOS may suspend apps running in the background. For LoRA training:
- Keep the app in the foreground
- Disable auto-lock during training
- Enable "Background App Refresh" in Settings

### Thermal Management

Extended inference or training will cause the device to heat up. The system may throttle performance to prevent overheating.

### Memory Limits

iOS has strict memory limits. Recommended maximum model sizes:
- iPhone with 6GB RAM: Up to 3.8B (TQ1_0)
- iPhone with 8GB RAM: Up to 7B (TQ1_0)

---

## Model Recommendations

| Use Case | Recommended Model | Format |
|----------|-------------------|--------|
| Quick responses | bitnet-125m | TQ1_0 |
| Balanced | bitnet-1b | TQ1_0 |
| Quality focus | bitnet-1.5b | TQ2_0 |
| Maximum quality | bitnet-7b | TQ1_0 |

---

**[Back to Releases](../README.md)** | **[Main Documentation](../../README.md)**
