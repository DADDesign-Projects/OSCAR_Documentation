---
title: cAllPass
parent: DSP
layout: default
nav_order: 3
---
# DadDSP::cAllPass

**Namespace :** DadDSP::cAllPass  
**Files :** cAllPass.h / N/A (interface only)   
**Directory :** DAD_FORGE/DSP  
**Description :** First-order all-pass filter for audio signal processing. Allows modifying phase without affecting amplitude.

---

## 📋 Class Description

The `cAllPass` class implements a first-order all-pass filter used to modify the phase of an audio signal without affecting its amplitude response. This type of filter is particularly useful in DSP applications for creating phase effects, correcting delays, or as an intermediate component in more complex filters. The filter uses bilinear transformation to calculate coefficients and supports the use of external states via a pointer to an `sAPFState` structure.

---

## 🎯 Associated Enums and Structures

### sAPFState
State storage structure for the first-order all-pass filter. Contains previous samples needed for recursive filter calculation.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `x1` | `float` = 0.0f | Previous input sample (n-1) |
| `y1` | `float` = 0.0f | Previous output sample (n-1) |

**Methods:**
- `void Reset()` : Resets the filter state by setting x1 and y1 to 0.0f

---

## 📚 Public Methods
### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, sAPFState *pState)` |
| **Description** | Initializes the filter with a given sampling rate. Optionally attaches an external state structure for storing previous samples. If pState is null, the filter uses its own internal memory (not implemented in this case). |
| **Parameter(s)** | |
| `sampleRate` | System sampling rate in Hz (e.g., 48000, 44100) |
| `pState` | Pointer to an external sAPFState structure. If nullptr, internal state is not used. |
| **Return** | None (void) |

### **SetFrequency**

| Element | Details |
|:---|:---|
| **Method** | `void SetFrequency(float freq)` |
| **Description** | Sets the filter cutoff frequency and updates the all-pass coefficient using bilinear transformation. The frequency is clamped to avoid values below 1 Hz which could cause instabilities. |
| **Parameter(s)** | |
| `freq` | Desired cutoff frequency in Hz |
| **Return** | None (void) |

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `float Process(float x, sAPFState &s)` |
| **Description** | Processes an input sample using the provided state structure. Implements the first-order all-pass filter difference equation: y[n] = -a*x[n] + x[n-1] + a*y[n-1]. Updates previous samples in the state structure. |
| **Parameter(s)** | |
| `x` | Current input sample |
| `s` | Reference to an sAPFState structure containing the filter state |
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
| `m_a` | `float` = 0.0f | Calculated all-pass coefficient dynamically according to cutoff frequency |
| `m_pState` | `sAPFState*` = nullptr | Pointer to an optional external state structure |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cAllPass.h"

using namespace DadDSP;

int main() {
    // Initialize sampling rate (48kHz)
    cAllPass filter;
    
    // Create external state structure
    sAPFState state;
    
    // Initialize the filter with sampling rate and state
    filter.Initialize(48000.0f, &state);
    
    // Set cutoff frequency (e.g., 2000 Hz)
    filter.SetFrequency(2000.0f);
    
    // Process an audio signal (example with a test sample)
    float inputSample = 0.5f;
    float outputSample = filter.Process(inputSample, state);
    
    // Reset the filter state if needed
    filter.ResetState();
    
    return 0;
}
```

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
