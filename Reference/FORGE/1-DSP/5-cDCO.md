---
title: cDCO
parent: DSP
layout: default
nav_order: 5
---
# DadDSP::cDCO

**Namespace :** DadDSP::cDCO  
**Files :** cDCO.h / N/A (interface only)   
**Directory :** DAD_FORGE/DSP  
**Description :** Frequency Controlled Oscillator (DCO) implementing multiple waveforms with duty cycle modulation.

---

## 📋 Class Description
The `cDCO` (Digital Controlled Oscillator) class implements a versatile digital oscillator capable of generating multiple waveforms: sine, square, triangle, and their modified variants. Unlike traditional oscillators that use look-up tables (LUT), this DCO calculates values in real-time, offering higher precision and flexibility in duty cycle modulation. The oscillator uses an incremental phase system with automatic wrapping to guarantee perfect continuity between cycles. Mathematical constants PI and 2*PI are defined as private constants to optimize trigonometric calculations. The class uses `arm_sin_f32` from the "CMSIS DSP Software Library" for sine calculation acceleration.

---

## 🎯 Associated Enums and Structures

None.

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, float frequency, float minFreq, float maxFreq, float dutyCycle)` |
| **Description** | Initializes the oscillator with sampling rate, starting frequency, frequency limits and initial duty cycle. Configures internal parameters for phase increment calculation and applies clamps on duty cycle. |
| **Parameter(s)** | |
| `sampleRate` | System sampling rate in Hz (e.g., 48000, 44100) |
| `frequency` | Initial oscillator frequency in Hz |
| `minFreq` | Minimum possible frequency in Hz (for normalization) |
| `maxFreq` | Maximum possible frequency in Hz (for normalization) |
| `dutyCycle` | Initial duty cycle normalized between 0 and 1 (internal clamp: 0.1 to 0.9) |
| **Return** | None (void) |

### **setNormalizedFreq**

| Element | Details |
|:---|:---|
| **Method** | `void setNormalizedFreq(float frequency)` |
| **Description** | Sets frequency as normalized value between 0 and 1, where 0 corresponds to minFreq and 1 to maxFreq. Calculates corresponding phase step for per-sample increment. Used mainly by cModulator for simplified relative frequency management. |
| **Parameter(s)** | |
| `frequency` | Normalized value between 0 (minFreq) and 1 (maxFreq) |
| **Return** | None (void) |

### **setFreq**

| Element | Details |
|:---|:---|
| **Method** | `void setFreq(float frequency)` |
| **Description** | Directly sets frequency in Hz. Calculates phase step (m_dcoStep) as the ratio between frequency and sampling rate. Simple method for direct control of absolute frequency. |
| **Parameter(s)** | |
| `frequency` | Desired frequency in Hz |
| **Return** | None (void) |

### **setNormalizedDutyCycle**

| Element | Details |
|:---|:---|
| **Method** | `void setNormalizedDutyCycle(float dutyCycle)` |
| **Description** | Sets duty cycle for modulated waveforms. Applies internal clamping to avoid extreme values: effective value is between 0.1 and 0.9, allowing safety margin for rising/falling edge transitions. |
| **Parameter(s)** | |
| `dutyCycle` | Normalized value between 0 and 1 (internal clamp: 0.1 to 0.9) |
| **Return** | None (void) |

### **Step**

| Element | Details |
|:---|:---|
| **Method** | `void Step()` |
| **Description** | Advances the oscillator by one step, incrementing phase position by m_dcoStep. If phase exceeds 1.0, it is automatically wrapped back to [0, 1) by subtraction. Must be called at each sample to generate continuous output. |
| **Parameter(s)** | None |
| **Return** | None (void) |

### **getSquareValue**

| Element | Details |
|:---|:---|
| **Method** | `float getSquareValue()` |
| **Description** | Returns square wave value with rounded rising and falling edges. The waveform presents: a rising edge over 4% of the cycle, a constant high state, a falling edge starting at 70%, and a low state. Transitions are linear to avoid abrupt discontinuities. |
| **Parameter(s)** | None |
| **Return** | Square wave value (0.0 to 1.0) |

### **getSquareModValue**

| Element | Details |
|:---|:---|
| **Method** | `float getSquareModValue()` |
| **Description** | Returns square wave value with duty cycle modulation. Falling edge position is determined by m_dutyCycle, allowing dynamic control of duty ratio. Edges remain rounded to maintain continuity. |
| **Parameter(s)** | None |
| **Return** | Modulated square wave value (0.0 to 1.0) |

### **getTriangleValue**

| Element | Details |
|:---|:---|
| **Method** | `float getTriangleValue()` |
| **Description** | Returns symmetric triangular wave value. The waveform rises linearly from 0 to 1 over the first half-cycle (0-0.5) and falls linearly from 1 to 0 over the second half-cycle (0.5-1.0). Used for pitch modulation and LFO. |
| **Parameter(s)** | None |
| **Return** | Triangular wave value (0.0 to 1.0) |

### **getTriangleValuePhased**

| Element | Details |
|:---|:---|
| **Method** | `float getTriangleValuePhased(float phaseShift)` |
| **Description** | Returns triangular wave value with applied phase shift. The phaseShift parameter is added to current phase position and wrapped in [0, 1). Allows time-shifting a triangular wave relative to other signals (e.g., synchronization with LFO or other oscillators). |
| **Parameter(s)** | |
| `phaseShift` | Phase shift in cycle units (0.0 to 1.0) |
| **Return** | Phased triangular wave value (0.0 to 1.0) |

### **getTriangleModValue**

| Element | Details |
|:---|:---|
| **Method** | `float getTriangleModValue()` |
| **Description** | Returns triangular wave value with duty cycle modulation. The waveform is stretched or compressed according to m_dutyCycle: first segment rises from 0 to 1 on [0, dutyCycle] and falls from 1 to 0 on [dutyCycle, 1]. |
| **Parameter(s)** | None |
| **Return** | Modulated triangular wave value (0.0 to 1.0) |

### **getSineValue**

| Element | Details |
|:---|:---|
| **Method** | `float getSineValue()` |
| **Description** | Returns normalized sine wave value between 0 and 1. Uses arm_sin_f32 function with PI/2 offset to start at 0 and rise to 1 at first peak. Optimized with precalculated m_twoPI constant. |
| **Parameter(s)** | None |
| **Return** | Normalized sine wave value (0.0 to 1.0) |

### **getSymetricalSineValue**

| Element | Details |
|:---|:---|
| **Method** | `float getSymetricalSineValue()` |
| **Description** | Returns symmetric sine wave value between -1 and 1. Uses arm_sin_f32 with PI/2 offset to obtain a zero-centered waveform. Useful for applications requiring AC signals (e.g., modulation, white noise). |
| **Parameter(s)** | None |
| **Return** | Symmetric sine wave value (-1.0 to 1.0) |

### **getRectifiedSineValue**

| Element | Details |
|:---|:---|
| **Method** | `float getRectifiedSineValue()` |
| **Description** | Returns rectified (half-wave) sine wave value. Uses arm_sin_f32 without offset, producing positive waveform for half-cycles [0-1] and negative for [1-2], but only positive part is used. Values between 0 and PI. |
| **Parameter(s)** | None |
| **Return** | Rectified sine wave value (0.0 to ~3.14) |

### **setPosition**

| Element | Details |
|:---|:---|
| **Method** | `void setPosition(float position)` |
| **Description** | Directly sets oscillator phase position, allowing instant jump to any point in the cycle. Useful for "glitch" effects or resynchronization with other oscillators. Position is implicitly clamped within [0, 1). |
| **Parameter(s)** | |
| `position` | Target phase position (0.0 to 1.0) |
| **Return** | None (void) |

---

## 🔒 Protected/Private Methods

### N/A
No protected or private methods of technical interest to document.

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected/Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_twoPI` | `const float` = 6.28318530717959F | 2*PI constant for optimized trigonometric calculations |
| `m_PI` | `const float` = 3.14159265358979F | PI constant for phase offsets |
| `m_halfPI` | `const float` = 1.5707963267949F | PI/2 constant for sine offsets |
| `m_sampleRate` | `float` = 0.0f | System sampling rate in Hz, used for phase step calculation |
| `m_minFreq` | `float` = 0.0f | Minimum configured frequency in Hz (for normalization) |
| `m_maxFreq` | `float` = 0.0f | Maximum configured frequency in Hz (for normalization) |
| `m_dutyCycle` | `float` | Duty cycle for modulated waveforms (0.1 to 0.9 after clamp) |
| `m_dcoValue` | `float` = 0.0f | Current phase position, normalized within [0, 1) interval |
| `m_dcoStep` | `float` = 0.0f | Phase increment per sample, derived from frequency and sampleRate |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cDCO.h"

using namespace DadDSP;

int main() {
    // Initialize DCO (48kHz sampleRate, 1000Hz freq)
    cDCO oscillator;
    oscillator.Initialize(48000.0f, 1000.0f, 20.0f, 20000.0f, 0.5f);
    
    // Generate a sine wave (0-1)
    for(int i = 0; i < 48000; i++) {
        oscillator.Step();
        float sineValue = oscillator.getSineValue();
        // Use sineValue in your audio processing
    }
    
    // Change frequency
    oscillator.setFreq(1500.0f);
    
    // Generate a triangular wave with duty cycle modulation
    oscillator.setNormalizedDutyCycle(0.75f);
    for(int i = 0; i < 48000; i++) {
        oscillator.Step();
        float triangleMod = oscillator.getTriangleModValue();
        // Use triangleMod for LFO or modulation
    }
    
    // Phase-shift a triangular wave
    float phaseShift = 0.25f; // 1/4 cycle offset
    float phasedTriangle = oscillator.getTriangleValuePhased(phaseShift);
    
    // Instant position jump (glitch effect)
    oscillator.setPosition(0.9f);
    
    return 0;
}
```

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
