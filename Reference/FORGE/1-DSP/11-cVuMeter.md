---
title: cVuMeter
parent: DSP
layout: default
nav_order: 11
---
# DadDSP::cVuMeter

**Namespace:** DadDSP::cVuMeter  
**Files:** cVuMeter.h / cVuMeter.cpp   
**Directory:** DAD_FORGE/DSP   
**Description:** VU meter class with peak detection, peak hold, and clipping monitoring for professional audio monitoring.

---

## 📋 Class Description

The `cVuMeter` class implements a complete audio level measurement system, inspired by traditional VU meters but with modern features essential for real-time DSP applications. It provides:

- **Smoothed RMS level measurement:** Smooth level tracking with asymmetric attack and release
- **Peak detection with hold:** Records maximum levels for a configurable duration (1 second)
- **Clipping monitoring:** Automatic detection when signal exceeds 85% (-1.4 dB) with status held for 4 seconds
- **dB/percent conversion:** Utility functions to display levels in decibels or percentage
- **Sample-rate configuration:** Automatic coefficient adjustment according to sample rate

**Typical Use Cases:**
- Audio monitoring in VST/AU plugins
- Visual indicators for user interfaces (UI)
- Overload detection and equipment protection
- Spectral analysis and audio metrics
- Limiters and compressors with visualization

---

## 🎯 Enumerations and Associated Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **cVuMeter** (Constructor)

| Element | Details |
|:---|:---|
| **Method** | `cVuMeter()` |
| **Description** | Default constructor that initializes the class without parameters. Parameters must be configured via the `Init()` method before use. The constructor is empty and does not configure any internal state. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(float sampleRate)` |
| **Description** | Fully initializes the VU meter with the specified audio sample rate. Calls `reset()` to initialize all internal states to their default values, then configures the sample rate which automatically calculates attack/release coefficients and hold counters in samples. |
| **Parameter(s)** | |
| `sampleRate` | Audio sample rate in Hz (e.g., 44100, 48000). Used to calculate envelope coefficients and convert times to number of samples. |
| **Return** | None (void) |

---

### **CalcPeakAndLevel**

| Element | Details |
|:---|:---|
| **Method** | `void CalcPeakAndLevel(float input)` |
| **Description** | Main processing method that calculates the current level, detects peaks, and monitors clipping from an audio sample. Applies asymmetric attack/release envelope for smooth level tracking, updates the peak level with hold for the configured duration, and detects if the signal exceeds the clipping threshold (85% = -1.4 dB) with status held for 4 seconds. |
| **Parameter(s)** | |
| `input` | Input audio sample (float value between -1.0 and 1.0). The absolute value is used for level calculation. |
| **Return** | None (void) |

---

### **getLevelPercent**

| Element | Details |
|:---|:---|
| **Method** | `float getLevelPercent() const` |
| **Description** | Returns the current audio level as a linear percentage between 0.0 and 1.0. Represents the smoothed RMS amplitude of the signal after attack/release envelope application. Useful for direct comparisons or simple mathematical calculations. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 (silence) and 1.0 (maximum signal) representing the current level as linear percentage |

---

### **getLevelDB**

| Element | Details |
|:---|:---|
| **Method** | `float getLevelDB() const` |
| **Description** | Returns the current audio level in decibels. Uses the standard logarithmic formula `20 * log10(linear)` to convert linear amplitude to dB scale, with a minimum value of -45 dB to avoid infinite negative values. Useful for displaying conventional audio levels (e.g., -6 dBFS, -20 dBFS). |
| **Parameter(s)** | None |
| **Return** | Float value in decibels between -45.0 dB and 0.0 dB |

---

### **getPeakPercent**

| Element | Details |
|:---|:---|
| **Method** | `float getPeakPercent() const` |
| **Description** | Returns the recorded peak level as a linear percentage between 0.0 and 1.0. Represents the maximum reached since the last reset or end of peak hold. Useful for displaying instantaneous peaks on a user interface. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 and 1.0 representing the peak level as linear percentage |

---

### **getPeakDB**

| Element | Details |
|:---|:---|
| **Method** | `float getPeakDB() const` |
| **Description** | Returns the recorded peak level in decibels. Converts the peak percentage to dB scale using the same logarithmic formula as `getLevelDB()`. Useful for displaying maximum reached peaks on a user interface or for overload alerts. |
| **Parameter(s)** | None |
| **Return** | Float value in decibels between -45.0 dB and 0.0 dB representing the maximum peak |

---

### **getLevelPercentDB**

| Element | Details |
|:---|:---|
| **Method** | `float getLevelPercentDB() const` |
| **Description** | Returns the current level normalized within the [0.0, 1.0] range based on the configurable dB scale (-45 to -6 dB). First converts the level to dB, then linearly normalizes between the min and max bounds. Useful for displaying VU meters where 0% corresponds to -45 dB and 100% to -6 dB. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 (-45 dB) and 1.0 (-6 dB) for normalized meter display |

---

### **getPeakPercentDB**

| Element | Details |
|:---|:---|
| **Method** | `float getPeakPercentDB() const` |
| **Description** | Returns the peak level normalized within the [0.0, 1.0] range based on the configurable dB scale (-45 to -6 dB). First converts the peak to dB, then linearly normalizes between the min and max bounds. Useful for displaying peak levels on a VU meter where the range is explicitly defined. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 (-45 dB) and 1.0 (-6 dB) for normalized meter display |

---

### **isClipping**

| Element | Details |
|:---|:---|
| **Method** | `bool isClipping() const` |
| **Description** | Checks whether clipping is currently detected. Returns `true` if the signal has exceeded the 85% threshold (-1.4 dB) and the hold counter is not yet expired (4 seconds). Allows displaying red visual indicators or triggering protection actions. |
| **Parameter(s)** | None |
| **Return** | `true` if clipping is active, `false` otherwise |

---

### **resetPeak**

| Element | Details |
|:---|:---|
| **Method** | `void resetPeak()` |
| **Description** | Manually resets the peak level to the current level and resets the peak hold counter to zero. Useful for synchronizing the meter with a specific audio event, or for forcing an immediate update of the peak display without waiting for the natural end of the hold period. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **reset**

| Element | Details |
|:---|:---|
| **Method** | `void reset()` |
| **Description** | Resets all VU meter states to initial values. Resets current level and peak to 0.0, resets peak hold and clipping counters to zero, and disables the clipping flag. Must be called during initialization or to return the meter to a known state. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **setSampleRate**

| Element | Details |
|:---|:---|
| **Method** | `void setSampleRate(float sampleRate)` |
| **Description** | Modifies the audio sample rate at runtime. Automatically recalculates all coefficients and counters based on the new sample rate, including the attack/release envelope and peak/clipping hold durations. Essential in systems with dynamic sample rate (VST plugins, etc.). |
| **Parameter(s)** | |
| `sampleRate` | New sample rate in Hz (positive). |
| **Return** | None (void) |

---

## 🔒 Protected / Private Methods

### **linearToDb**

| Member | Type | Description |
|:---|:---|:---|
| `linearToDb(float linear)` | `float` | Utility conversion that transforms a linear amplitude value (0.0-1.0) into decibels. Uses the formula `20 * log10(linear)` with a minimum bound of -45 dB to avoid infinite negative values. Protected method as it is only used by the class. |

### **dbToPercent**

| Member | Type | Description |
|:---|:---|:---|
| `dbToPercent(float db)` | `float` | Utility conversion that transforms a decibel value into percentage for meter display. Linearly normalizes between -45 dB (0%) and -6 dB (100%), with clamping to bounds. Protected method as it is only used by the class. |

### **updateAttackRelease()**

| Member | Type | Description |
|:---|:---|:---|
| `updateAttackRelease()` | `void` | Updates attack and release coefficients based on the sample rate. Also calculates the number of samples corresponding to peak hold times (1s) and clipping hold times (4s). Uses exponential formulas for natural transitions. Protected method as it is only called during initialization or sample rate changes. |

## 🔒 Protected / Private Members

| Member | Type | Description |
|:---|:---|:---|
| `m_sampleRate` | `float` | Audio sample rate in Hz (default: 0). Used for all time calculations and conversion between time and number of samples. |
| `m_currentLevel` | `float` | Current signal level (default: 0.0). Represents the smoothed RMS amplitude after attack/release envelope application. Value between 0.0 and 1.0. |
| `m_peakLevel` | `float` | Signal peak level (default: 0.0). Represents the maximum reached with hold for the configured duration. Value between 0.0 and 1.0. Declines gradually at 0.999x per sample after peak hold time expires. |
| `m_peakHoldCounter` | `uint32_t` | Peak hold counter in samples (default: 0). Initialized to the total hold duration upon a new peak, decremented each sample. Controls how long the peak level remains held after a maximum is reached. |
| `m_clipCounter` | `uint32_t` | Clipping hold counter in samples (default: 0). Initialized to the total hold duration upon clipping detection, decremented each sample. Controls how long the clipping status remains active after the clipped signal ends. |
| `m_clipActive` | `bool` | Clipping detection flag (default: false). Indicates whether clipping is currently active or has been recently detected and the hold time has not expired. |
| `m_peakHoldSamples` | `uint32_t` | Peak hold duration in samples (default: calculated). Calculated as `PEAK_HOLD_TIME * sampleRate` (1 second × sample rate). Used to initialize the peak hold counter. |
| `m_clipHoldSamples` | `uint32_t` | Clipping hold duration in samples (default: calculated). Calculated as `CLIP_HOLD_TIME * sampleRate` (4 seconds × sample rate). Used to initialize the clipping hold counter. |
| `m_attackCoeff` | `float` | Attack coefficient for the envelope (default: calculated). Calculated using the formula `1 - exp(-1 / (ATTACK_TIME * sampleRate))`. Controls the speed at which the current level rises toward a higher value. Typical value: 0.0067 at 48kHz. |
| `m_releaseCoeff` | `float` | Release coefficient for the envelope (default: calculated). Calculated using the formula `1 - exp(-1 / (RELEASE_TIME * sampleRate))`. Controls the speed at which the current level falls toward a lower value. Typical value: 0.03 at 48kHz. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cVuMeter.h"

using namespace DadDSP;

int main() {
    // --- Scenario 1: Simple audio monitoring ---
    
    cVuMeter vuMeter;
    
    // Initialize for 48kHz sample rate
    vuMeter.Init(48000.0f);
    
    float inputSample = 0.0f;
    float currentLevel = 0.0f;
    float peakLevel = 0.0f;
    bool isClipped = false;
    
    // Process audio samples
    for (uint32_t i = 0; i < 48000; i++) {
        // Example signal: A4 note (440Hz)
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        // Calculate level, peaks and clipping
        vuMeter.CalcPeakAndLevel(inputSample);
        
        // Retrieve values for display
        currentLevel = vuMeter.getLevelPercent();       // 0.0 - 1.0
        peakLevel = vuMeter.getPeakPercent();          // 0.0 - 1.0
        isClipped = vuMeter.isClipping();              // true/false
        
        // Display in dB (e.g., for a UI)
        float levelDb = vuMeter.getLevelDB();          // e.g., -23.5 dB
        float peakDb = vuMeter.getPeakDB();            // e.g., -18.2 dB
    }
    
    // --- Scenario 2: Normalized VU meter display ---
    
    cVuMeter meterForDisplay;
    meterForDisplay.Init(48000.0f);
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        meterForDisplay.CalcPeakAndLevel(inputSample);
        
        // Normalize for VU meter display (-45 dB to -6 dB)
        float displayPercent = meterForDisplay.getLevelPercentDB();
        
        // Display: 0% = -45 dB, 100% = -6 dB
        printf("VU Level: %.0f%% (%.2f dB)\n", 
               displayPercent * 100.0f, 
               meterForDisplay.getLevelDB());
    }
    
    // --- Scenario 3: Clipping detection with alert ---
    
    cVuMeter clipDetector;
    clipDetector.Init(48000.0f);
    
    for (uint32_t i = 0; i < 48000; i++) {
        // Signal that will exceed 0.70 (clipping)
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        clipDetector.CalcPeakAndLevel(inputSample);
        
        // Check for clipping
        if (clipDetector.isClipping()) {
            printf("⚠️ CLIPPING DETECTED! Level: %.2f dB\n", 
                   clipDetector.getPeakDB());
            
            // Possible protection action:
            // - Reduce gain
            // - Activate a limiter
            // - Display a red LED
        }
    }
    
    // --- Scenario 4: Manual peak control ---
    
    cVuMeter manualControl;
    manualControl.Init(48000.0f);
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        manualControl.CalcPeakAndLevel(inputSample);
        
        // Reset peak manually each sample
        // to display instantaneous current level
        manualControl.resetPeak();
        
        float current = manualControl.getLevelPercent();
        printf("Instant Level: %.2f%%\n", current * 100.0f);
    }
    
    // --- Scenario 5: Dynamic sample rate adaptation ---
    
    cVuMeter dynamicRate;
    dynamicRate.Init(48000.0f);
    
    for (uint32_t i = 0; i < 48000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        dynamicRate.CalcPeakAndLevel(inputSample);
    }
    
    // Change sample rate at runtime (e.g., VST plugin)
    dynamicRate.setSampleRate(96000.0f);  // Switch to 96kHz
    
    for (uint32_t i = 48000; i < 96000; i++) {
        inputSample = std::sin(2.0f * M_PI * 440.0f * i / 96000.0f);
        
        dynamicRate.CalcPeakAndLevel(inputSample);
    }
    
    // --- Scenario 6: Multi-channel stereo monitoring ---
    
    cVuMeter leftChannel, rightChannel;
    leftChannel.Init(48000.0f);
    rightChannel.Init(48000.0f);
    
    float leftInput = 0.0f, rightInput = 0.0f;
    
    for (uint32_t i = 0; i < 48000; i++) {
        // Stereo signal
        leftInput = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);
        rightInput = std::cos(2.0f * M_PI * 440.0f * i / 48000.0f);
        
        leftChannel.CalcPeakAndLevel(leftInput);
        rightChannel.CalcPeakAndLevel(rightInput);
        
        // Display stereo levels
        printf("L: %.2f%% (%.2f dB) | R: %.2f%% (%.2f dB)\n",
               leftChannel.getLevelPercent() * 100.0f,
               leftChannel.getLevelDB(),
               rightChannel.getLevelPercent() * 100.0f,
               rightChannel.getLevelDB());
    }
    
    // Full reset if necessary
    leftChannel.reset();
    rightChannel.reset();
    
    return 0;
}
```

---

## 📊 Default Configuration

| Parameter | Value | Description |
|:---|:---|:---|
| `PEAK_HOLD_TIME` | 1.0s | Duration peaks are held after being reached |
| `CLIP_THRESHOLD` | 0.70 (-1.4 dB) | Clipping detection threshold (85% of maximum) |
| `CLIP_HOLD_TIME` | 4.0s | Duration clipping status remains active |
| `DB_MIN` | -45.0 dB | Minimum for dB conversion (avoids -∞) |
| `DB_MAX` | -6.0 dB | Maximum for meter normalization |
| `ATTACK_TIME` | 0.15s | Envelope attack time (fast response) |
| `RELEASE_TIME` | 0.30s | Envelope release time (slow response) |

---

## 📊 Retrieval Method Comparison

| Method | Returns | Range | Typical Use |
|:---|:---|:---|:---|
| `getLevelPercent()` | Linear percentage | 0.0 - 1.0 | Simple mathematical calculations |
| `getLevelDB()` | Decibels | -45.0 dB - 0.0 dB | Conventional audio display |
| `getPeakPercent()` | Linear peak percentage | 0.0 - 1.0 | UI peak indicators |
| `getPeakDB()` | Peaks in decibels | -45.0 dB - 0.0 dB | Overload alerts, monitoring |
| `getLevelPercentDB()` | Normalized meter percentage | 0.0 - 1.0 | VU meter display (-45 to -6 dB) |
| `getPeakPercentDB()` | Normalized peak percentage | 0.0 - 1.0 | VU meter peak indicators |

---

## 📊 Clipping State Comparison

| State | Condition | Behavior |
|:---|:---|:---|
| No clipping | `level < 0.70` | `isClipping()` returns `false` |
| Clipping detected | `level >= 0.70` | `isClipping()` becomes `true`, counter initialized to 4s |
| Clipping held | `counter > 0` | `isClipping()` remains `true` during hold period |
| Return to normal | `counter == 0` | `isClipping()` becomes `false` |

---

## ⚙️ Implementation Notes

1. **Asymmetric Envelope:** Attack is fast (0.15s) to track peaks quickly, while release is slower (0.30s) to avoid rapid fluctuations and provide stable reading. Coefficients are calculated using exponential formulas: `coeff = 1 - exp(-1 / (time × sampleRate))`.

2. **Peak Hold:** When a new peak is detected, the counter is initialized to the total hold duration (1 second). On each subsequent sample, the counter decrements. If the current level still exceeds the peak, the counter is reset. Once the counter reaches zero, the peak declines gradually by a factor of 0.999x per sample.

3. **Clipping Detection:** The 0.70 threshold (-1.4 dB) corresponds to 85% of the numerical maximum, a common value for early overload detection before actual clipping (0.0). The status remains active for 4 seconds after the clipped signal ends to avoid rapid blinking.

4. **Logarithmic Conversion:** The conversion to dB uses `20 * log10(linear)` which is the standard formula for sound pressure and audio levels. The minimum bound of -45 dB avoids infinite negative values for silence.

5. **Meter Normalization:** The `getLevelPercentDB()` and `getPeakPercentDB()` methods linearly normalize between -45 dB (0%) and -6 dB (100%), which corresponds to a typical monitoring dynamic range where -6 dB is considered "hot" but not clipping.

6. **Sample Rate Adaptation:** All coefficients and counters are dynamically recalculated during sample rate changes, allowing use in VST/AU plugins with sample rate conversion.

7. **Absolute Value:** The `CalcPeakAndLevel()` method uses `std::abs(input)` to process level independently of signal polarity, which is correct for an RMS/VU meter.

8. **Clamping:** Input values are clamped to [0.0, 1.0] before processing to avoid artifacts from out-of-range samples.

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.