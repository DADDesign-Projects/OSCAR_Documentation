---
title: cModulator
parent: DSP
layout: default
nav_order: 8
---
# DadDSP::cModulator

**Namespace:** DadDSP::cModulator  
**Files:** cModulator.h / cModulator.cpp   
**Directory:** DAD_FORGE/DSP   
**Description:** Pitch modulation effect using an LFO-driven delay line to create vibrato, chorus, and other time-based modulations.

---

## 📋 Class Description

The `cModulator` class implements a sophisticated pitch modulation effect that combines an LFO-driven delay line with a frequency-controlled digital oscillator (DCO). It allows creating rich audio effects such as vibrato, chorus, and other time-based modulations by dynamically varying the delay applied to audio samples.

**Architecture:**
- **Dynamic Delay Line:** Delay varies according to LFO waveform (sine or triangle)
- **Integrated DCO:** Generates LFO waveforms (sinusoidal or triangular)
- **Pitch Compensation:** Automatic sample offset calculation based on pitch ratio
- **Optional Feedback:** Supports feedback loop with sin/cos gain control
- **Inversion Mode:** Option to invert the output signal

**Use Cases:**
- Vibrato (fine pitch modulation)
- Chorus (thickening effect with multiple modulations)
- Flanger/Phaser (with feedback and complex modulation)
- Modulated delay effects

---

## 🎯 Enumerations and Associated Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **cModulator** (Constructor)

| Element | Details |
|:---|:---|
| **Method** | `cModulator()` |
| **Description** | Default constructor that initializes the class without parameters. Parameters must be configured via the `Initialize()` method before use. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **~cModulator** (Destructor)

| Element | Details |
|:---|:---|
| **Method** | `~cModulator()` |
| **Description** | Default destructor that cleans up class resources. No special cleanup required as the class uses automatically managed pointers and objects. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, float* pBuffer, uint32_t BufferSize, float LFOFrequency, float PitchVariationMin, float PitchVariationMax, float TimeOffset)` |
| **Description** | Fully initializes the pitch modulator with all necessary parameters. Configures the delay line, DCO oscillator, and calculates initial delay values based on LFO frequency and specified pitch variations. Buffer memory must be allocated before this call. |
| **Parameter(s)** | |
| `sampleRate` | Audio sample rate in Hz (e.g., 44100, 48000). Used for all time and delay calculations. |
| `pBuffer` | Pointer to memory buffer containing sample history for the delay line. Must be allocated with at least `BufferSize` float elements. |
| `BufferSize` | Buffer size in samples. Determines the maximum possible delay duration (e.g., 48000 samples at 48kHz = 1 second). |
| `LFOFrequency` | Base LFO frequency in Hz. Determines modulation speed (e.g., 5Hz for slow vibrato, 20Hz for fast chorus). |
| `PitchVariationMin` | Minimum pitch variation as a percentage (e.g., -50% = semitone down, -100% = octave down). Used to calculate minimum delay. |
| `PitchVariationMax` | Maximum pitch variation as a percentage. Used to calculate maximum delay. |
| `TimeOffset` | Initial time offset in seconds. Added to base delay to position the delay start point (e.g., 0.01s = 10ms offset). |
| **Return** | None (void) |

---

### **calcSample**

| Element | Details |
|:---|:---|
| **Method** | `float calcSample(float Pitch, float SampleRate, float LFO) const` |
| **Description** | Public method that calculates delay in samples based on a specific pitch ratio and given LFO frequency. Uses the physical pitch modulation formula to determine how many samples of delay correspond to a given pitch variation. Useful for preliminary calculations or dynamic adjustments. |
| **Parameter(s)** | |
| `Pitch` | Pitch variation as a percentage (e.g., -50 for semitone down, 100 for octave up). |
| `SampleRate` | Audio sample rate in Hz. |
| `LFO` | LFO frequency in Hz. |
| **Return** | Float value representing the calculated delay in samples (positive) |

---

### **setFreq**

| Element | Details |
|:---|:---|
| **Method** | `void setFreq(float Freq)` |
| **Description** | Modifies the LFO frequency at runtime. Automatically recalculates minimum and maximum delays based on the new frequency, and updates the DCO oscillator. Validation checks that the buffer is large enough to accommodate the new maximum delay with the time offset. |
| **Parameter(s)** | |
| `Freq` | New LFO frequency in Hz (must be > 0). |
| **Return** | None (void) |

---

### **setPitchVariation**

| Element | Details |
|:---|:---|
| **Method** | `void setPitchVariation(float PitchVariationMin, float PitchVariationMax)` |
| **Description** | Modifies the pitch variation range. Recalculates the corresponding minimum and maximum delays. Validation checks that the buffer is large enough to accommodate the new maximum delay with the time offset. Useful for dynamic modulations or automations. |
| **Parameter(s)** | |
| `PitchVariationMin` | New minimum pitch variation as a percentage. |
| `PitchVariationMax` | New maximum pitch variation as a percentage. |
| **Return** | None (void) |

---

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `float Process(float Sample, float Depth, uint8_t Shape = 0, float Feedback = 0, bool Mode = false)` |
| **Description** | Main processing method that applies pitch modulation to an audio sample. Advances the DCO oscillator, calculates dynamic delay based on current LFO waveform and modulation depth, then retrieves the sample from the buffer with the calculated delay. Supports feedback, phase inversion, and different waveforms. |
| **Parameter(s)** | |
| `Sample` | Input audio sample (float value between -1.0 and 1.0). |
| `Depth` | Modulation depth (0.0 to 1.0). Controls the amplitude of delay variation between min/max values. 0.0 = no modulation, 1.0 = full modulation. |
| `Shape` | LFO waveform: `0` = sinusoidal (default), `1` = triangular. Influences modulation character (smooth vs linear). |
| `Feedback` | Feedback gain (0.0 to 1.0). Creates a loop by reinjecting part of the signal into the delay line. 0.0 = no feedback. |
| `Mode` | Inversion mode: `false` = normal output, `true` = output phase inversion. Used for specific effects like flanger. |
| **Return** | Float value of the processed (modulated) sample. Returns input unchanged if parameters are invalid (Depth > 1.0 or BufferSize == 0). |

---

## 🔒 Protected / Private Members

| Member | Type | Description |
|:---|:---|:---|
| `m_sampleRate` | `float` | Audio sample rate in Hz. Used for all time calculations and conversion between time and samples. |
| `m_BufferSize` | `uint32_t` | Delay line buffer size in samples. Determines maximum possible delay duration and is used to validate delay calculations. |
| `m_LFOFrequency` | `float` | LFO frequency in Hz with a +1 offset applied during initialization. Used to calculate modulation delays and drive the DCO oscillator. |
| `m_PitchVariationMin` | `float` | Minimum pitch variation as a percentage (e.g., -50% = semitone down). Used to calculate minimum delay during modulation. |
| `m_PitchVariationMax` | `float` | Maximum pitch variation as a percentage. Used to calculate maximum delay during modulation. |
| `m_SamplesMax` | `float` | Maximum calculated delay in samples based on `PitchVariationMax`. Used as upper bound for dynamic delay. |
| `m_SamplesMin` | `float` | Minimum calculated delay in samples based on `PitchVariationMin`. Used as lower bound for dynamic delay. |
| `m_NbSampleOffset` | `float` | Initial time offset converted to samples (`sampleRate × TimeOffset`). Added to all delays to position the delay start point. |
| `m_DelayLine` | `cDelayLine` | Delay line instance used to store sample history and allow recall with variable delay. Automatically manages buffer memory. |
| `m_DCO` | `cDCO` | Frequency-controlled digital oscillator instance used to generate LFO waveforms (sinusoidal or triangular). Driven by LFO frequency. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cModulator.h"

using namespace DadDSP;

int main() {
    // Audio configuration
    const float SAMPLE_RATE = 48000.0f;
    const uint32_t BUFFER_SIZE = 96000;  // 2 seconds at 48kHz
    
    // Allocate buffer for delay line
    float* delayBuffer = new float[BUFFER_SIZE];
    
    // --- Scenario 1: Classic Vibrato (fine modulation) ---
    
    cModulator vibrato;
    
    // Initialize with small pitch variation
    vibrato.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        5.0f,           // LFO frequency: 5Hz (slow vibrato)
        -50.0f,         // Min variation: -50% (semitone down)
        50.0f,          // Max variation: +50% (semitone up)
        0.01f           // Time offset: 10ms
    );
    
    float inputSample = 0.0f;
    float outputSample = 0.0f;
    
    // Process samples with vibrato
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);  // A4 note
        
        outputSample = vibrato.Process(inputSample, 1.0f, 0, 0.0f, false);
        
        // OutputSample now contains the vibrato signal
    }
    
    // --- Scenario 2: Chorus effect ---
    
    cModulator chorus;
    
    // Initialize for chorus effect
    chorus.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        0.5f,           // LFO frequency: 0.5Hz (slow modulation)
        -20.0f,         // Min variation: -20%
        20.0f,          // Max variation: +20%
        0.02f           // Time offset: 20ms
    );
    
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);
        
        // Reduced depth for subtle chorus effect
        outputSample = chorus.Process(inputSample, 0.7f, 0, 0.0f, false);
    }
    
    // --- Scenario 3: Flanger with feedback ---
    
    cModulator flanger;
    
    // Initialize for flanger effect (feedback + modulation)
    flanger.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        0.4f,           // LFO frequency: 0.4Hz (typical flanger)
        -30.0f,         // Min variation: -30%
        30.0f,          // Max variation: +30%
        0.015f          // Time offset: 15ms
    );
    
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);
        
        // Strong feedback for pronounced flanger effect
        float feedbackGain = 0.8f;  // 80% feedback
        
        outputSample = flanger.Process(inputSample, 1.0f, 0, feedbackGain, false);
    }
    
    // --- Scenario 4: Dynamic frequency modulation ---
    
    cModulator dynamicMod;
    dynamicMod.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        5.0f,           // Initial frequency
        -50.0f,         // Min variation
        50.0f,          // Max variation
        0.01f           // Offset
    );
    
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);
        
        // Dynamic modulation: vary LFO frequency
        float targetFreq = 5.0f + std::sin(i * 0.1f) * 3.0f;  // 2-8Hz
        
        dynamicMod.setFreq(targetFreq);
        
        outputSample = dynamicMod.Process(inputSample, 1.0f, 0, 0.0f, false);
    }
    
    // --- Scenario 5: Triangular waveform ---
    
    cModulator triangleMod;
    triangleMod.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        10.0f,          // LFO frequency: 10Hz
        -40.0f,         // Min variation
        40.0f,          // Max variation
        0.015f          // Offset
    );
    
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);
        
        // Use triangular waveform for linear modulation
        outputSample = triangleMod.Process(inputSample, 0.8f, 1, 0.0f, false);
    }
    
    // --- Scenario 6: Inversion mode (specific flanger) ---
    
    cModulator invertedMod;
    invertedMod.Initialize(
        SAMPLE_RATE, 
        delayBuffer, 
        BUFFER_SIZE,
        0.3f,           // LFO frequency: 0.3Hz
        -25.0f,         // Min variation
        25.0f,          // Max variation
        0.01f           // Offset
    );
    
    for (uint32_t i = 0; i < BUFFER_SIZE; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / SAMPLE_RATE);
        
        // Enable inversion mode for specific effect
        outputSample = invertedMod.Process(inputSample, 1.0f, 0, 0.0f, true);
    }
    
    // Cleanup
    delete[] delayBuffer;
    
    return 0;
}
```
---
## 📊 LFO Waveform Comparison

| Waveform | `Shape` Value | Characteristic | Typical Use |
|:---|:---|:---|:---|
| Sinusoidal | `0` (default) | Smooth curve, progressive transition | Classic vibrato, natural chorus |
| Triangular | `1` | Linear transition, fast attack/release | Flanger, more aggressive effects |

---

## ⚙️ Implementation Notes

1. **Parameter Validation**: The `Process()` method returns the input sample unchanged if `Depth > 1.0` or `BufferSize == 0`, avoiding audio artifacts.

2. **Delay Clamping**: The calculated delay is clamped between 0 and `BufferSize - 1` to prevent invalid memory access in the delay line.

3. **Feedback Mathematics**: The method uses `arm_cos_f32()` and `arm_sin_f32()` (ARM CMSIS library) to calculate feedback and mix gains according to orthogonal sin/cos separation, ensuring energy conservation.

4. **Pitch Calculation**: The formula used is `samples = |(1 - PitchRatio) × Fs| / (2π × f_LFO)` where `PitchRatio = PitchPercentage/100 + 1`. This physical relationship ensures that delay modulation correctly corresponds to perceived pitch variation.

5. **LFO Offset**: During initialization, the LFO frequency has a +1 offset applied (`m_LFOFrequency = LFOFrequency + 1`), likely to avoid division by zero issues or for specific calibration.

6. **Validation Error**: If the maximum delay with offset exceeds the buffer size, `Error_Handler()` is called, indicating a critical configuration problem.

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.