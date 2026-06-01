---
title: cPitchShifter
parent: DSP
layout: default
nav_order: 10
---
# DadDSP::cPitchShifter

**Namespace:** DadDSP::cPitchShifter  
**Files:** cPitchShifter.h / [N/A]   
**Directory:** DAD_FORGE/DSP   
**Description:** High-quality pitch shifter for octave up (+1 octave) using 4 grains with temporal dispersion and optimal windowing, specifically designed for shimmer reverb effects.

---

## 📋 Class Description

The `cPitchShifter` class implements a professional pitch shifter specialized for octave up (+1 octave) using an advanced parallel grain technique. This approach achieves exceptional sound quality while maintaining optimal performance, making it ideal for "shimmer" reverb effects where an additional octave is added to the original signal.

**Technical Architecture:**
- **4 parallel grains:** Simultaneous processing with temporal dispersion to avoid phase artifacts
- **75% overlap:** Each grain overlaps the others by 75% for smooth transitions
- **Optimized Hann window:** Window applied to each grain with overlap compensation
- **Configurable interpolation:** Supports fast linear or high-quality Hermite interpolation
- **Complete signal processing:** Integrated pre-emphasis, anti-aliasing, DC blocker, and soft saturation

**Primary Use Cases:**
- Shimmer reverb effects (adding bright octave)
- Spectral widening for melodic instruments
- Atmospheric effects and synth pads
- Vocal processing with additional harmonics

---

## 🎯 Enumerations and Associated Structures

### Grain
Internal structure defining the state of each grain in the pitch shifting system. It contains all necessary information to drive the processing of an individual grain.

| Element | Type | Description |
|:---|:---|:---|
| `active` | `bool` | Grain activation state: `true` if the grain is active and processing samples, `false` otherwise |
| `phase` | `uint32_t` | Current phase within the grain (read position). Advances with each processed sample. |
| `startPos` | `uint32_t` | Start position in the circular buffer where this grain begins reading. Used to calculate read position with offset. |
| `startDelay` | `uint32_t` | Delay in samples before the grain activates. Allows progressive grain activation at startup. |

---

## 📚 Public Methods

### **cPitchShifter** (Constructor)

| Element | Details |
|:---|:---|
| **Method** | `cPitchShifter()` |
| **Description** | Default constructor that initializes the class without parameters. Full configuration must be performed via the `Initialize()` method before use. The circular buffer is allocated as a static data member. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(uint32_t sampleRate)` |
| **Description** | Fully initializes the pitch shifter with the system sample rate. Fills the circular buffer with zeros, generates the optimized Hann window for 4 grains at 75% overlap, initializes the 4 grains with progressive temporal dispersion, and resets all filter states (pre-emphasis, DC blocker, anti-aliasing). |
| **Parameter(s)** | |
| `sampleRate` | Audio sample rate in Hz (e.g., 44100, 48000). Used to configure internal parameters and properly initialize the system. |
| **Return** | None (void) |

---

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `inline float Process(float input)` |
| **Description** | Main processing method that applies +1 octave pitch shifting to an audio sample. Implements 8 processing steps: pre-emphasis to reduce low-frequency artifacts, writing to circular buffer, progressive grain activation, processing all active grains with interpolation and windowing, empirical normalization, low-pass anti-aliasing filter, DC blocker to eliminate DC components, and soft saturation to avoid hard clipping. |
| **Parameter(s)** | |
| `input` | Input audio sample (float value between -1.0 and 1.0). |
| **Return** | Float value of the processed sample with +1 octave pitch shift (between -1.0 and 1.0, softly saturated) |

---

### **SetQuality**

| Element | Details |
|:---|:---|
| **Method** | `void SetQuality(bool highQuality)` |
| **Description** | Configures the interpolation mode used for pitch shifting. High-quality mode uses 4-point Hermite interpolation for superior accuracy, at the cost of slightly higher CPU usage. Standard mode uses simple linear interpolation for maximum performance. Useful for adjusting the quality/performance tradeoff as needed. |
| **Parameter(s)** | |
| `highQuality` | `true` = use Hermite (superior quality), `false` = use linear (maximum performance). |
| **Return** | None (void) |

---

### **SetBrightness**

| Element | Details |
|:---|:---|
| **Method** | `void SetBrightness(float brightness)` |
| **Description** | Adjusts the brightness of the integrated low-pass anti-aliasing filter. A higher coefficient (more "bright") allows more high frequencies to pass but may increase noise and sampling artifacts. A lower coefficient (more "dark") attenuates high frequencies more for a smoother, cleaner sound. The value is clamped between 0.3 and 0.95. |
| **Parameter(s)** | |
| `brightness` | Filter brightness level, ideally between 0.5 (darker) and 0.9 (brighter). Automatically clamped to [0.3, 0.95]. |
| **Return** | None (void) |

---

## 🔒 Protected / Private Methods

### **Grain** (Local Structure)

| Element | Type | Description |
|:---|:---|:---|
| `active` | `bool` | Grain activation state. Determines whether the grain participates in current processing. |
| `phase` | `uint32_t` | Current read phase within the grain. Advances with each ProcessGrain() call. |
| `startPos` | `uint32_t` | Start position in the circular buffer. Reference point for calculating reads with offset. |
| `startDelay` | `uint32_t` | Delay before grain activation. Allows progressive grain startup. |

### **ProcessGrain**

| Member | Type | Description |
|:---|:---|:---|
| `ProcessGrain(Grain& grain)` | `float` | Private method that processes an individual grain. Reads the sample with 2x offset (for +1 octave), applies the configured interpolation, multiplies by the Hann window, and returns the grain's contribution to the final output. |

### **LinearInterpolate**

| Member | Type | Description |
|:---|:---|:---|
| `LinearInterpolate(uint32_t basePos, float offset)` | `float` | Simple linear interpolation between two adjacent buffer samples. Uses the fractional part of the offset to weight values at positions base and base+1. Faster but less accurate than Hermite. |

### **HermiteInterpolate**

| Member | Type | Description |
|:---|:---|:---|
| `HermiteInterpolate(uint32_t basePos, float offset)` | `float` | 4-point Hermite interpolation for superior quality. Uses 4 consecutive samples (base-1 to base+2) and calculates cubic coefficients for smoother, more accurate interpolation. Better quality/performance tradeoff than higher-order methods. |

### **GenerateWindow**

| Member | Type | Description |
|:---|:---|:---|
| `GenerateWindow()` | `void` | Generates the optimized Hann window function for the 4-grain 75% overlap system. Applies a 0.85 power to the standard Hann window to compensate for overlap and achieve theoretical constant gain. Calculated once during initialization, stored in `m_Window`. |

### **SoftSaturate**

| Member | Type | Description |
|:---|:---|:---|
| `SoftSaturate(float x)` | `float` | Asymmetric (tube-like) soft saturation function to prevent hard clipping. For values > 0.7, applies a compressive tanhf curve. For values < -0.7, applies the same symmetric compression. Values within [-0.7, 0.7] are returned unchanged. Creates a more musical and natural sound than hard clipping. |

## 🔒 Protected / Private Members

| Member | Type | Description |
|:---|:---|:---|
| `m_Buffer` | `float[BUFFER_SIZE]` | Circular buffer of size 8192 samples (power of 2). Stores input samples for offset readback. Uses a mask for fast modulo operations during access. |
| `m_Window` | `float[GRAIN_SIZE]` | Hann window table of size 1536 samples. Applied to each grain to avoid discontinuities and optimize gain with 75% overlap. Generated once during initialization. |
| `m_Grains` | `Grain[NUM_GRAINS]` | Array of 4 parallel grains. Each grain reads the buffer with a different time offset and contributes to the final output. Allows temporal dispersion to avoid phase artifacts. |
| `m_WritePos` | `uint16_t` | Current write position in the circular buffer. Advances with each incoming sample, masked to avoid overflow. |
| `m_SampleCounter` | `uint16_t` | Global sample counter since initialization. Used to trigger progressive grain activation according to their start delay. |
| `m_PreEmphZ1` | `float` | Pre-emphasis filter state. Used to calculate: `preEmph = input - m_PreEmphZ1 * 0.97f`. Reduces low-frequency artifacts typical of pitch shifters. |
| `m_DcBlockX1` | `float` | DC blocker input state. Used to detect and eliminate DC components that could cause humming. |
| `m_DcBlockY1` | `float` | DC blocker output state. Used in calculation: `dcBlocked = output - m_DcBlockX1 + 0.998f * m_DcBlockY1`. Effectively eliminates DC offsets while minimizing signal impact. |
| `m_LpfState` | `float` | Low-pass anti-aliasing filter state. Used in calculation: `m_LpfState = m_LpfState * m_LpfCoeff + output * (1.0f - m_LpfCoeff)`. Filters high frequencies that could cause aliasing during 2x pitch shifting. |
| `m_LpfCoeff` | `float` | Low-pass anti-aliasing filter coefficient. Controls filter "brightness": higher value = brighter (more high frequencies), lower value = darker (fewer high frequencies). Default value: 0.7. |
| `m_UseHermite` | `bool` | Flag indicating use of Hermite interpolation. If `true`, uses 4-point Hermite for superior quality. If `false`, uses simple linear interpolation for maximum performance. Default value: `true`. |
| `m_SampleRate` | `uint32_t` | System sample rate configured during initialization. Used for time calculations and may be useful for future dynamic adjustments. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cPitchShifter.h"

using namespace DadDSP;

int main() {
    // --- Scenario 1: Shimmer pitch shifter for reverb ---
    
    cPitchShifter shimmer;
    
    // Initialize for 48kHz sample rate
    shimmer.Initialize(48000);
    
    float inputSample = 0.0f;
    float outputSample = 0.0f;
    
    // Process audio signal with shimmer (upper octave)
    for (uint32_t i = 0; i < 48000; i++) {
        // Example signal: A4 note (440Hz)
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        outputSample = shimmer.Process(inputSample);
        
        // outputSample now contains the original signal + upper octave
    }
    
    // --- Scenario 2: Interpolation quality adjustment ---
    
    cPitchShifter qualityTest;
    qualityTest.Initialize(48000);
    
    // High-quality mode for critical applications
    qualityTest.SetQuality(true);  // Hermite interpolation
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        outputSample = qualityTest.Process(inputSample);
    }
    
    // Performance mode for CPU-constrained real-time applications
    cPitchShifter performanceTest;
    performanceTest.Initialize(48000);
    performanceTest.SetQuality(false);  // Linear interpolation
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        outputSample = performanceTest.Process(inputSample);
    }
    
    // --- Scenario 3: Anti-aliasing filter brightness adjustment ---
    
    cPitchShifter brightShimmer;
    brightShimmer.Initialize(48000);
    
    // Brighter filter to preserve high frequencies
    brightShimmer.SetBrightness(0.9f);  // More "bright", increased aliasing risk
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        outputSample = brightShimmer.Process(inputSample);
    }
    
    // Darker filter for smoother, cleaner sound
    cPitchShifter darkShimmer;
    darkShimmer.Initialize(48000);
    darkShimmer.SetBrightness(0.5f);  // More "dark", less aliasing
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        outputSample = darkShimmer.Process(inputSample);
    }
    
    // --- Scenario 4: Integration into VST/AU plugin ---
    
    class MyEffectPlugin {
    public:
        float processAudio(float input) {
            // Process with shimmer to add bright octave
            return shimmer.Process(input);
        }
        
        void setup() {
            shimmer.Initialize(48000);
        }
        
    private:
        cPitchShifter shimmer;
    };
    
    MyEffectPlugin plugin;
    plugin.setup();
    
    while (true) {
        float audioInput = getAudioInput();
        float audioOutput = plugin.processAudio(audioInput);
        setAudioOutput(audioOutput);
    }
    
    return 0;
}
```

## 📊 Grain Architecture

| Grain | `startDelay` | Activation | Role |
|:---|:---|:---|:---|
| Grain 0 | 0 samples | Immediate | First grain, main processing |
| Grain 1 | 384 samples (7.5ms) | After 7.5ms | Temporal dispersion #1 |
| Grain 2 | 768 samples (15ms) | After 15ms | Temporal dispersion #2 |
| Grain 3 | 1152 samples (22.5ms) | After 22.5ms | Temporal dispersion #3 |

---

## 📊 Interpolation Mode Comparison

| Mode | Method | Accuracy | CPU | Recommendation |
|:---|:---|:---|:---|:---|
| High Quality | 4-point Hermite | High | Medium | Audio quality priority |
| Performance | 2-point Linear | Medium | Low | CPU-constrained real-time |

---

## 📊 Windowing Parameters

| Parameter | Value | Role |
|:---|:---|:---|
| Window type | Modified Hann | Avoids discontinuities between grains |
| Power | 0.85 | Compensates 75% overlap for constant gain |
| Size | 1536 samples (~32ms) | Individual grain duration |

---

## ⚙️ Implementation Notes

1. **2x Pitch Shift:** Reading with `readOffset = phase * 2.0f` achieves +1 octave pitch shifting in real-time with no processing delay. This is a standard yet effective technique.

2. **Temporal Dispersion:** The 4 grains start at different delays (0, 384, 768, 1152 samples) to avoid phase interference and create a richer, more spatial sound.

3. **75% Overlap:** With `HOP_SIZE = 384` on `GRAIN_SIZE = 1536`, each grain overlaps the next by 75%. This allows smooth transitions between grains without audible discontinuities.

4. **Empirical Normalization:** The `0.35f` factor is empirically adjusted to compensate for the sum of squared windows with 75% overlap, ensuring constant gain.

5. **Deterministic Micro-variation:** On each grain reset, a slight random but deterministic variation (`offset = 32 + (startPos * 7) & 63`) is added to avoid long-term phase artifacts.

6. **Pre-emphasis:** The filter `preEmph = input - m_PreEmphZ1 * 0.97f` reduces low-frequency artifacts typical of pitch shifters by slightly boosting high frequencies before processing.

7. **Low-pass Anti-aliasing:** The filter `m_LpfState = m_LpfState * m_LpfCoeff + output * (1.0f - m_LpfCoeff)` is essential because 2x pitch shift doubles the maximum frequency, requiring filtering to avoid audible aliasing.

8. **DC Blocker:** The algorithm `dcBlocked = output - m_DcBlockX1 + 0.998f * m_DcBlockY1` effectively eliminates DC components that could cause low-frequency humming.

9. **Soft Saturation:** The `SoftSaturate()` function uses an asymmetric tanhf curve to gently compress peaks beyond ±0.7, creating a more musical and natural sound than traditional hard clipping.

10. **Power-of-2 Circular Buffer:** The buffer size (8192 = 2^13) allows using a mask (`BUFFER_MASK = BUFFER_SIZE - 1`) for modulo operations, avoiding expensive division and accelerating processing.

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.