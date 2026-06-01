---
title: cBiQuad
parent: DSP
layout: default
nav_order: 1
---
# DadDSP::cBiQuad

**Namespace :** DadDSP::cBiQuad  
**Files :** BiquadFilter.h / BiquadFilter.cpp   
**Directory :** DAD_FORGE/DSP  
**Description :** Versatile biquad filter class implementing Cookbook formulas for audio EQ filter coefficients, supporting 10 filter types (LPF, HPF, BPF, Notch, PEQ, Shelf, All-pass) with stereo processing and 24dB option.

---

## 📋 Class Description

The `cBiQuad` class is a biquad digital filter implementation used for real-time audio processing. It implements the coefficients formulas by Robert Bristow-Johnson (Cookbook formulae), allowing precise control over frequency responses. The class supports:

- **10 filter types** : Low Pass Filter (LPF), High Pass Filter (HPF), Band Pass Filter (BPF), Notch Filter, Parametric Equalizer (PEQ), and low/high Shelf filters (LSH/HSH) with 24dB variants
- **Stereo Processing** : Native management of left/right channels with separate filter states
- **24dB Option** : Cascade of two biquad stages for steep slope filters
- **Normalized Calculations** : Precalculated coefficients for optimized processing  
  
{: .tip}
> **Tip**  
>To help configure and visualize filters, you can use the Python utility:
>```text
>DAD_FORGE/@Python Utilities/BiQuad Visu/biquad.pyw
>```
>
>This tool displays the filter frequency response according to the selected parameters.
>![](biquadpyw.png)
---

## 🎯 Associated Enumerations and Structures

### FilterType

| Element | Value | Description |
|:---|:---|:---|
| `LPF` | 0 | Low Pass Filter |
| `LPF24` | 1 | 24 dB/octave Low Pass Filter |
| `HPF` | 2 | High Pass Filter |
| `HPF24` | 3 | 24 dB/octave High Pass Filter |
| `BPF` | 4 | Band Pass Filter |
| `NOTCH` | 5 | Notch Filter |
| `PEQ` | 6 | Parametric Equalizer (Peaking EQ) |
| `LSH` | 7 | Low Shelf Filter |
| `HSH` | 8 | High Shelf Filter |
| `AFP` | 9 | All-Pass Filter |

### sFilterState

| Element / Variable | Type | Description |
|:---|:---|:---|
| `x1` | `float` | Previous input sample x[n-1] |
| `x2` | `float` | Earlier input sample x[n-2] |
| `y1` | `float` | Previous output sample y[n-1] |
| `y2` | `float` | Earlier output sample y[n-2] |

Method: `Reset()` - Resets all states to zero.

### eChannel

| Element | Value | Description |
|:---|:---|:---|
| `Left` | 0 | Left audio channel |
| `Right` | 1 | Right audio channel |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float sampleRate, float cutoffFreq, float gainDb, float bandwidth, FilterType type)` |
| **Description** | Initializes the filter with configuration parameters: sampling frequency, cutoff frequency, gain in decibels, bandwidth, and filter type. Automatically calculates filter coefficients. |
| **Parameter(s)** | |
| `sampleRate` | Sampling rate in Hz (e.g., 44100.0f for CD audio) |
| `cutoffFreq` | Cutoff frequency in Hz where the response reaches -3dB |
| `gainDb` | Gain applied to the filter in decibels (positive = boost, negative = cut) |
| `bandwidth` | Filter bandwidth (wider = faster transition) |
| `type` | Type of filter to use (LPF, HPF, BPF, Notch, PEQ, LSH, HSH, AFP, LPF24, HPF24) |

### **CalculateParameters**

| Element | Details |
|:---|:---|
| **Method** | `void CalculateParameters()` |
| **Description** | Recalculates the filter coefficients based on current parameters (cutoff frequency, gain, bandwidth, and type). Uses Robert Bristow-Johnson's Cookbook formulas. Coefficients are normalized for optimized processing. Executes a memory barrier (__DMB__) to ensure data consistency in multi-threaded environments. |

### **GainDb**

| Element | Details |
|:---|:---|
| **Method** | `float GainDb(float freq)` |
| **Description** | Calculates the filter gain in decibels at a given frequency. Allows for analyzing the frequency response without sample processing. |
| **Parameter(s)** | |
| `freq` | Analysis frequency in Hz (0 to sampleRate/2) |
| **Return** | Filter gain at the specified frequency, expressed in decibels (dB) |

### **ProcessFlast12dbStereo**

| Element | Details |
|:---|:---|
| **Method** | `inline void ProcessFlast12dbStereo(AudioBuffer* pIn, AudioBuffer* pOut)` |
| **Description** | Fast stereo processing for 12dB/octave filters. Applies the filter to the left and right channels separately without additional stage cascading. Optimized for real-time processing with minimal CPU overhead. |
| **Parameter(s)** | |
| `pIn` | Pointer to the input audio buffer containing unfiltered samples |
| `pOut` | Pointer to the output audio buffer where filtered samples will be stored |

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `inline float Process(float sample, eChannel Channel = eChannel::Left)` |
| **Description** | Processes a single audio sample through the filter. For 24dB filters (LPF24, HPF24), it cascades two biquad stages: the first stage for the steep transition, and the second stage as the main stage. Supports mono or stereo processing via the Channel parameter. |
| **Parameter(s)** | |
| `sample` | Input audio sample (normalized amplitude) |
| `Channel` | Processing channel: Left for left channel, Right for right channel (default: Left) |
| **Return** | Filtered output audio sample |

### **setSampleRate**

| Element | Details |
|:---|:---|
| **Method** | `inline void setSampleRate(float sampleRate)` |
| **Description** | Sets the sampling frequency of the audio system. Updates the inverse sampling rate for frequency calculations. |
| **Parameter(s)** | |
| `sampleRate` | Sampling frequency in Hz |

### **setCutoffFreq**

| Element | Details |
|:---|:---|
| **Method** | `inline void setCutoffFreq(float cutoffFreq)` |
| **Description** | Sets the filter's cutoff frequency. The frequency at which the response reaches -3dB relative to the nominal gain. |
| **Parameter(s)** | |
| `cutoffFreq` | Cutoff frequency in Hz |

### **setGainDb**

| Element | Details |
|:---|:---|
| **Method** | `inline void setGainDb(float gainDb)` |
| **Description** | Sets the filter's gain in decibels. Positive values increase amplitude (boost), negative values reduce amplitude (cut). |
| **Parameter(s)** | |
| `gainDb` | Gain in decibels (e.g., +6.0f for 6dB boost, -12.0f for 12dB cut) |

### **setBandwidth**

| Element | Details |
|:---|:---|
| **Method** | `inline void setBandwidth(float bandwidth)` |
| **Description** | Sets the filter's bandwidth. Controls the transition slope around the cutoff frequency. Wider = sharper transition. |
| **Parameter(s)** | |
| `bandwidth` | Normalized bandwidth (typically 0.01 to 1.0) |

### **setType**

| Element | Details |
|:---|:---|
| **Method** | `inline void setType(FilterType type)` |
| **Description** | Sets the filter type to be used. Determines the coefficient formulas applied during parameter calculation. |
| **Parameter(s)** | |
| `type` | Filter type (LPF, HPF, BPF, Notch, PEQ, LSH, HSH, AFP, LPF24, HPF24) |

### **getSampleRate**

| Element | Details |
|:---|:---|
| **Method** | `inline float getSampleRate()` |
| **Description** | Retrieves the configured sampling frequency of the audio system. |
| **Return** | Sampling frequency in Hz |

### **getCutoffFreq**

| Element | Details |
|:---|:---|
| **Method** | `inline float getCutoffFreq()` |
| **Description** | Retrieves the current cutoff frequency of the filter. |
| **Return** | Cutoff frequency in Hz |

### **getGainDb**

| Element | Details |
|:---|:---|
| **Method** | `inline float getGainDb()` |
| **Description** | Retrieves the current gain of the filter in decibels. |
| **Return** | Gain in decibels (dB) |

### **getBandwidth**

| Element | Details |
|:---|:---|
| **Method** | `inline float getBandwidth()` |
| **Description** | Retrieves the current bandwidth of the filter. |
| **Return** | Normalized bandwidth |

### **getType**

| Element | Details |
|:---|:---|
| **Method** | `inline FilterType getType()` |
| **Description** | Retrieves the currently configured filter type. |
| **Return** | Current filter type (FilterType) |

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `inline float Process(float sample, sFilterState &FilterState)` |
| **Description** | Direct mono sample processing using the biquad difference equation: y[n] = b0*x[n] + b1*x[n-1] + b2*x[n-2] - a1*y[n-1] - a2*y[n-2]. Automatically updates the delay state for the next iteration. |
| **Parameter(s)** | |
| `sample` | Input audio sample (normalized amplitude) |
| `FilterState` | Reference to the filter state containing lag values x1, x2, y1, y2 |
| **Return** | Filtered output audio sample |

---

## 🔒 Protected / Private Methods

No protected or private methods.

---

## 📦 Data Members (Variables)

### Public Variables

No public variables.

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_InvSampleRate` | `float` | Inverse sampling rate (1/sampleRate) in seconds |
| `m_cutoffFreq` | `float` | Cutoff frequency in Hz |
| `m_gainDb` | `float` | Filter gain in decibels |
| `m_bandwidth` | `float` | Normalized bandwidth parameter |
| `m_type` | `FilterType` | Configured filter type |
| `m_a0` | `volatile float` | Normalized numerator coefficient b0 |
| `m_a1` | `volatile float` | Normalized numerator coefficient b1 |
| `m_a2` | `volatile float` | Normalized numerator coefficient b2 |
| `m_a3` | `volatile float` | Normalized denominator coefficient a1 |
| `m_a4` | `volatile float` | Normalized denominator coefficient a2 |
| `m_FilterState[0]` | `sFilterState` | Filter state: Stage 1 left channel |
| `m_FilterState[1]` | `sFilterState` | Filter state: Stage 2 left channel (main) |
| `m_FilterState[2]` | `sFilterState` | Filter state: Stage 1 right channel |
| `m_FilterState[3]` | `sFilterState` | Filter state: Stage 2 right channel (main) |

---

## 💡 Usage Example

```cpp
// Insert code example here
#include "DadDSP.h"

using namespace DadDSP;

int main() {
    // Configuration audio
    const float SAMPLE_RATE = 44100.0f;
    
    // Creation and initialization of a Parametric EQ bandpass filter
    cBiQuad filter;
    filter.Initialize(SAMPLE_RATE, 1000.0f, 3.0f, 1.0f, FilterType::PEQ);
    
    // Dynamic configuration - real-time adjustment
    filter.setCutoffFreq(500.0f);      // Change cutoff frequency
    filter.setGainDb(-6.0f);           // 6dB cut
    filter.setBandwidth(0.5f);         // Moderate bandwidth
    filter.CalculateParameters();      // Recalculates the filter coefficients based on current parameters
    
    // Frequency response analysis
    float gainAt100Hz = filter.GainDb(100.0f);
    float gainAt1000Hz = filter.GainDb(1000.0f);
    
    // Mono sample processing
    sFilterState state;
    state.Reset();
    for (int i = 0; i < numSamples; i++) {
        float inputSample = audioInput[i];
        float outputSample = filter.Process(inputSample, state);
        audioOutput[i] = outputSample;
    }
    
    // Fast stereo processing (12dB/octave)
    cBiQuad stereoFilter;
    stereoFilter.Initialize(SAMPLE_RATE, 2000.0f, 0.0f, 0.1f, FilterType::LPF);
    
    AudioBuffer* pIn = &inputBuffer;
    AudioBuffer* pOut = &outputBuffer;
    stereoFilter.ProcessFlast12dbStereo(pIn, pOut);
    
    // Full stereo processing (24dB/octave)
    cBiQuad highPassFilter;
    highPassFilter.Initialize(SAMPLE_RATE, 30.0f, 0.0f, 1.0f, FilterType::HPF24);
    
    for (int i = 0; i < numSamples; i++) {
        float leftInput = pIn->Left[i];
        float rightInput = pIn->Right[i];
        
        float leftOutput = highPassFilter.Process(leftInput, eChannel::Left);
        float rightOutput = highPassFilter.Process(rightInput, eChannel::Right);
        
        pOut->Left[i] = leftOutput;
        pOut->Right[i] = rightOutput;
    }
    
    return 0;
}
```
---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.