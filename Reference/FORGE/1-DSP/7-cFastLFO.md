---
title: cFastLFO
parent: DSP
layout: default
nav_order: 7
---
# DadDSP::cFastLFO

**Namespace:** DadDSP::cFastLFO  
**Files:** cFastLFO.h / [N/A]  
**Directory:** DAD_FORGE/DSP  
**Description:** Fast LFO (Low Frequency Oscillator) implementation using a sinusoidal lookup table (LUT) with linear interpolation for optimized performance.

---

## 📋 Class Description

The `cFastLFO` class is a template implementation of a low-frequency oscillator (LFO) designed for maximum performance in real-time DSP applications. It uses a precomputed sinusoidal lookup table (LUT) combined with linear interpolation to generate high-quality sine waves with minimal computational cost.

**Main Features:**
- **Template class:** Table size configurable via the `TABLE_SIZE` template parameter (default: 1024)
- **Shared static table:** Single instance of the sine table per size, shared across all instances to save memory
- **Dual processing methods:**
  - `process()`: with linear interpolation for superior quality
  - `processFast()`: without interpolation for maximum performance
- **Flexible phase control:** Supports both normalized phase control (0.0-1.0) and radians (0.0-2π)
- **Single initialization:** The sine table is generated only once per template type

This class is ideal for modulating audio parameters such as filters, amplitude (vibrato/tremolo), or other effects requiring precise and performant periodic modulation.

---

## 🎯 Associated Enumerations and Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **cFastLFO** (Constructor)

| Item | Details |
|:---|:---|
| **Method** | `cFastLFO()` |
| **Description** | Default constructor that initializes the class without parameters. The sine table will be generated upon the first call to a method requiring table access (Initialize, process, or processFast). |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Initialize**

| Item | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, float frequency, float initialPhase)` |
| **Description** | Fully initializes the LFO by configuring the audio sample rate, modulation frequency, and initial phase. Automatically generates the sine table if not already done, calculates the phase increment based on frequency, and normalizes the initial phase to the interval [0.0, 1.0). |
| **Parameter(s)** | |
| `sampleRate` | Audio sample rate in Hz (e.g., 44100, 48000). Used to calculate phase increment per sample. |
| `frequency` | LFO frequency in Hz. Determines modulation speed (e.g., 5Hz for slow vibrato, 20Hz for fast tremolo). |
| `initialPhase` | Initial normalized phase between 0.0 and 1.0. Allows starting the LFO at a specific point in the sine cycle. |
| **Return** | None (void) |

---

### **setFrequency**

| Item | Details |
|:---|:---|
| **Method** | `inline void setFrequency(float freq)` |
| **Description** | Changes the LFO frequency at runtime. Immediately updates the phase increment to reflect the new modulation rate. Useful for dynamic modulations or frequency automation. |
| **Parameter(s)** | |
| `freq` | New LFO frequency in Hz (positive). |
| **Return** | None (void) |

---

### **getFrequency**

| Item | Details |
|:---|:---|
| **Method** | `inline float getFrequency() const` |
| **Description** | Returns the current LFO frequency. Allows reading the current modulation rate for display or debugging purposes. |
| **Parameter(s)** | None |
| **Return** | Float value representing current frequency in Hz |

---

### **setPhase**

| Item | Details |
|:---|:---|
| **Method** | `inline void setPhase(float newPhase)` |
| **Description** | Manually sets the current phase of the LFO. The value is automatically normalized to remain in the [0.0, 1.0) interval, allowing "jumping" to any point in the sine cycle. Useful for glitch effects or external synchronization. |
| **Parameter(s)** | |
| `newPhase` | New normalized phase between 0.0 (cycle start) and 1.0 (cycle end). |
| **Return** | None (void) |

---

### **getPhase**

| Item | Details |
|:---|:---|
| **Method** | `inline float getPhase() const` |
| **Description** | Returns the current LFO phase in normalized form between 0.0 and 1.0. Useful for determining where the LFO is in its current cycle (0.0 = positive peak, 0.5 = negative peak, etc.). |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 and 1.0 representing normalized phase |

---

### **setPhaseRad**

| Item | Details |
|:---|:---|
| **Method** | `inline void setPhaseRad(float newPhaseRadian)` |
| **Description** | Sets the LFO phase in radians instead of normalized format. Automatically converts radians to normalized format [0.0, 1.0) for consistent internal use. Accepts values in the interval [0.0, 2π). |
| **Parameter(s)** | |
| `newPhaseRadian` | New phase in radians (0.0 to 2π ≈ 6.283185). For example: π/2 = 1.5708 rad for the positive peak. |
| **Return** | None (void) |

---

### **getPhaseRad**

| Item | Details |
|:---|:---|
| **Method** | `inline float getPhaseRad() const` |
| **Description** | Returns the current LFO phase in radians. Converts internal normalized phase to radians format [0.0, 2π). Useful for applications requiring direct mathematical control (trigonometric calculations, synchronization with other oscillators, etc.). |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 and 2π representing phase in radians |

---

### **setSampleRate**

| Item | Details |
|:---|:---|
| **Method** | `inline void setSampleRate(float sr)` |
| **Description** | Changes the audio sample rate at runtime. Automatically recalculates the phase increment to maintain correct LFO frequency despite sample rate changes. Essential in dynamic sample rate systems (VST plugins, etc.). |
| **Parameter(s)** | |
| `sr` | New sample rate in Hz (positive). |
| **Return** | None (void) |

---

### **process**

| Item | Details |
|:---|:---|
| **Method** | `inline float process()` |
| **Description** | Main processing method that returns the next LFO value with linear interpolation. Calculates the index in the sine table, performs interpolation between two adjacent samples to smooth the output, then advances the phase. Offers the best quality/performance compromise for most audio applications. |
| **Parameter(s)** | None |
| **Return** | Float value of the interpolated sine wave (between -1.0 and 1.0) |

---

### **processFast**

| Item | Details |
|:---|:---|
| **Method** | `inline float processFast()` |
| **Description** | Optimized processing method without interpolation, performing direct access to the sine table. Faster than `process()` but with slightly lower quality (no smoothing between samples). Recommended for applications where every CPU cycle counts or for very low LFO frequencies where interpolation is less critical. |
| **Parameter(s)** | None |
| **Return** | Float value of the sine wave directly from the table (between -1.0 and 1.0) |

---

### **reset**

| Item | Details |
|:---|:---|
| **Method** | `inline void reset()` |
| **Description** | Resets the LFO phase to 0.0 (start of the sine cycle). Useful for synchronizing the LFO with other audio events, or for restarting a modulation from a known point. Phase is automatically normalized after reset. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

## 🔒 Protected / Private Methods

### **initTable**

| Item | Details |
|:---|:---|
| **Method** | `static void initTable()` |
| **Description** | Private static method that generates the sine table once per template type. Uses `std::sin()` to compute 1024 (or configurable) sine values over one complete cycle [0, 2π). The table is shared across all instances of the same template specialization to save memory. |
| **Parameter(s)** | None |
| **Return** | None (void) |

### **updatePhaseIncrement**

| Item | Details |
|:---|:---|
| **Method** | `inline void updatePhaseIncrement()` |
| **Description** | Private method that recalculates the phase increment based on frequency and sample rate. The increment represents how much phase advances per audio sample (phaseIncrement = frequency / sampleRate). |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_sampleRate` | `float` | Audio sample rate in Hz. Used to calculate phase increment and maintain LFO temporal accuracy. |
| `m_frequency` | `float` | LFO frequency in Hz. Determines the number of cycles per second of the generated sinusoidal modulation. |
| `m_phase` | `float` | Current normalized phase between 0.0 and 1.0. Represents position in the sine cycle (0.0 = start, 0.5 = middle, 1.0 = back to start). |
| `m_phaseIncrement` | `float` | Phase increment per audio sample. Calculated as `frequency / sampleRate`. Determines LFO advancement speed on each process call. |

### Static Members (Template)

| Member | Type | Description |
|:---|:---|:---|
| `m_sineTable` | `static std::array<float, TABLE_SIZE>` | Sine table shared across all instances of the same template specialization. Contains 1024 (or configurable) precomputed sine values for one complete cycle [0, 2π). |
| `m_tableInitialized` | `static bool` | Status flag indicating whether the sine table has already been generated. Prevents redundant recalculation during multiple initializations. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cFastLFO.h"

using namespace DadDSP;

int main() {
    // Audio configuration
    const float SAMPLE_RATE = 48000.0f;
    
    // --- Scenario 1: Standard LFO with interpolation (optimal quality) ---
    
    // Create an LFO of size 2048 (default)
    cFastLFO lfo1;
    
    // Initialize LFO: 48kHz sample rate, 5Hz frequency, initial phase at 0.25
    lfo1.Initialize(SAMPLE_RATE, 5.0f, 0.25f);
    
    float modulation = 0.0f;  // To store LFO output value
    
    // Generate 48000 samples (1 second) with interpolation
    for (int i = 0; i < SAMPLE_RATE; i++) {
        modulation = lfo1.process();  // Value between -1.0 and 1.0
        
        // Application example: Vibrato on carrier frequency
        float carrierFreq = 440.0f + (modulation * 50.0f);  // ±50Hz modulation
        float sample = std::sin(2.0f * M_PI * carrierFreq * i / SAMPLE_RATE);
    }
    
    // --- Scenario 2: High-performance LFO without interpolation ---
    
    // Create a smaller LFO for better performance (512 values)
    cFastLFO<512> lfo2;
    lfo2.Initialize(SAMPLE_RATE, 10.0f, 0.0f);
    
    for (int i = 0; i < SAMPLE_RATE; i++) {
        modulation = lfo2.processFast();  // Faster, less precise
        
        // Example: Amplitude modulation (Tremolo)
        float amplitude = 1.0f + (modulation * 0.3f);  // ±30% modulation
        float inputSignal = 0.5f;  // Signal to modulate
        float outputSignal = inputSignal * amplitude;
    }
    
    // --- Scenario 3: Dynamic frequency control ---
    
    cFastLFO<1024> lfo3;
    lfo3.Initialize(SAMPLE_RATE, 2.0f, 0.0f);
    
    for (int i = 0; i < SAMPLE_RATE; i++) {
        // Vary frequency dynamically
        float targetFreq = 2.0f + std::sin(i * 0.1f) * 3.0f;  // 2-5Hz
        
        lfo3.setFrequency(targetFreq);
        
        modulation = lfo3.process();
    }
    
    // --- Scenario 4: Synchronization and phase control ---
    
    cFastLFO<1024> lfo4;
    lfo4.Initialize(SAMPLE_RATE, 5.0f, 0.0f);
    
    // Reset LFO to beginning of cycle
    lfo4.reset();
    modulation = lfo4.process();  // Returns 0.0 (start of sine wave)
    
    // Jump directly to a specific point in the cycle
    lfo4.setPhase(0.5f);  // Go to negative peak
    modulation = lfo4.process();  // Returns approximately -1.0
    
    // Control in radians for mathematical calculations
    lfo4.setPhaseRad(M_PI_2);  // π/2 radians = normalized phase 0.25
    modulation = lfo4.process();
    
    return 0;
}
```
---

## 📊 Processing Method Comparison

| Method | Interpolation | Precision | Performance | Recommendation |
|:---|:---|:---|:---|:---|
| `process()` | Yes (linear) | High | Optimal | Standard use, audio quality |
| `processFast()` | No | Medium | Maximum | Critical real-time applications, low frequencies |

---

## ⚙️ Implementation Notes

1. **Template Parameter**: Table size can be configured via the TABLE_SIZE template parameter. A larger value improves precision but slightly increases memory usage and initialization time.

2. **Static Table**: The sine table is stored in static space and shared across all instances of the same template specialization, significantly optimizing memory usage.

3. **Phase Normalization**: All phase values are automatically normalized to remain in the [0.0, 1.0) interval, avoiding wrap-around errors.

4. **Linear Interpolation**: The `process()` method uses linear interpolation between two table points, considerably reducing audible "grain" compared to direct access without interpolation.

---
**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.
