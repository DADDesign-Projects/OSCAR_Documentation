---
title: cAllPass2
parent: DSP
layout: default
nav_order: 4
---
# DadDSP::cAllPass2

**Namespace :** DadDSP::cAllPass2  
**Files :** cAllPass.h / N/A (interface only)   
**Directory :** DAD_FORGE/DSP  
**Description :** Second-order all-pass filter for audio signal processing. Offers finer phase control with adjustable coefficients via center frequency and Q factor.

---

## 📋 Class Description
The `cAllPass2` class implements a second-order all-pass filter, offering increased flexibility compared to the first-order filter. This type of filter allows independent control of center frequency and quality factor (Q), which is essential for applications requiring higher precision in phase processing. The filter uses advanced bilinear transformation and supports the use of external states via a pointer to an `sAPF2State` structure. Coefficients are clamped to guarantee filter stability.

---

## 🎯 Associated Enums and Structures

### sAPF2State
State storage structure for the second-order all-pass filter. Contains previous samples needed for higher-order recursive filter calculation.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `x1` | `float` = 0.0f | Previous input sample (n-1) |
| `x2` | `float` = 0.0f | Prior input sample (n-2) |
| `y1` | `float` = 0.0f | Previous output sample (n-1) |
| `y2` | `float` = 0.0f | Prior output sample (n-2) |

**Methods:**
- `void Reset()` : Resets the filter state by setting x1, x2, y1 and y2 to 0.0f

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, sAPF2State* pState)` |
| **Description** | Initializes the filter with a given sampling rate and sets default Q factor to 0.707 (Q=1/√2, Butterworth response). Optionally attaches an external state structure for storing previous samples. If pState is null, internal state is not used. |
| **Parameter(s)** | |
| `sampleRate` | System sampling rate in Hz (e.g., 48000, 44100) |
| `pState` | Pointer to an external sAPF2State structure. If nullptr, internal state is not used. |
| **Return** | None (void) |

### **SetParameters**

| Element | Details |
|:---|:---|
| **Method** | `void SetParameters(float freq, float Q)` |
| **Description** | Sets center frequency and Q factor of the filter, then recalculates all all-pass coefficients using bilinear transformation. Applies clamps to guarantee stability: minimum frequency of 1 Hz and Q factor between 0.1 and 20.0. |
| **Parameter(s)** | |
| `freq` | Desired center frequency in Hz |
| `Q` | Quality factor (Q) of the filter, controlling frequency selectivity |
| **Return** | None (void) |

### **SetFrequency**

| Element | Details |
|:---|:---|
| **Method** | `void SetFrequency(float freq)` |
| **Description** | Sets only center frequency while maintaining current Q factor. Useful for dynamically adjusting frequency without modifying other filter parameters. |
| **Parameter(s)** | |
| `freq` | New center frequency in Hz |
| **Return** | None (void) |

### **SetQ**

| Element | Details |
|:---|:---|
| **Method** | `void SetQ(float Q)` |
| **Description** | Sets only Q factor while maintaining current center frequency. Allows adjusting filter selectivity without changing its frequency position. |
| **Parameter(s)** | |
| `Q` | New quality factor (Q) of the filter |
| **Return** | None (void) |

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `float Process(float x, sAPF2State &s)` |
| **Description** | Processes an input sample using the provided state structure. Implements the second-order all-pass filter difference equation: y[n] = -a1*y[n-1] - a2*y[n-2] + a2*x[n] + a1*x[n-1] + x[n-2]. Updates previous samples in the state structure. |
| **Parameter(s)** | |
| `x` | Current input sample |
| `s` | Reference to an sAPF2State structure containing the filter state |
| **Return** | Calculated output sample (float) |

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `float Process(float x)` |
| **Description** | Overload that processes a sample using the external state attached during Initialize(). If no external state is attached (pState == nullptr), returns 0.0f. Useful for simplified usage when state is managed by the class itself. |
| **Parameter(s)** | |
| `x` | Current input sample |
| **Return** | Calculated output sample (float), or 0.0f if no state is available |

### **ResetState**

| Element | Details |
|:---|:---|
| **Method** | `void ResetState()` |
| **Description** | Resets the filter state by calling the Reset() method on the external state structure if it was attached. Useful for resetting the filter without needing to reset the entire system. |
| **Parameter(s)** | None |
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
| `m_sampleRate` | `float` = 48000.0f | System sampling rate in Hz, used for coefficient calculation |
| `m_Q` | `float` = 0.707f | Quality factor (Q) of the filter, controlling frequency selectivity |
| `m_freq` | `float` = 1000.0f | Current center frequency in Hz |
| `m_a1` | `float` = 0.0f | All-pass coefficient a1, calculated dynamically according to freq and Q |
| `m_a2` | `float` = 0.0f | All-pass coefficient a2, calculated dynamically according to freq and Q |
| `m_pState` | `sAPF2State*` = nullptr | Pointer to an optional external state structure |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cAllPass.h"

using namespace DadDSP;

int main() {
    // Initialize sampling rate (48kHz)
    cAllPass2 filter;
    
    // Create external state structure
    sAPF2State state;
    
    // Initialize the filter with sampling rate and state
    filter.Initialize(48000.0f, &state);
    
    // Set center frequency (e.g., 1000 Hz)
    filter.SetFrequency(1000.0f);
    
    // Adjust Q factor for more selective response
    filter.SetQ(2.0f);
    
    // Process an audio signal (example with a test sample)
    float inputSample = 0.3f;
    float outputSample = filter.Process(inputSample, state);
    
    // Dynamically change frequency
    filter.SetFrequency(1500.0f);
    outputSample = filter.Process(inputSample, state);
    
    // Reset the filter state if needed
    filter.ResetState();
    
    return 0;
}
```

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
