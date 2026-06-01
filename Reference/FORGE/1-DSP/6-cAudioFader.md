---
title: cAudioFader
parent: DSP
layout: default
nav_order: 6
---
# DadDSP::cAudioFader

**Namespace:** DadDSP::cAudioFader  
**Files:** cAudioFader.h / cAudioFader.cpp  
**Directory:** DAD_FORGE/DSP  
**Description:** Audio crossfading class implementing equal-power curves for smooth transitions between two audio sources while maintaining constant perceived volume.

---

## 📋 Class Description

The `cAudioFader` class implements a professional audio crossfade system that enables smooth transitions between two audio sources (A and B). It uses equal-power curves based on sine/cosine functions to maintain constant perceived loudness during the transition, avoiding unwanted volume peaks or dips.

The class automatically handles:
- Calculation of the total number of samples needed based on sample rate and desired fade duration
- Application of equal-power crossfade curves for natural audio transitions
- Complete state management of transition phases (no fade, fade A→B, fade B→A)
- Preservation of the final state after a fade completes

This class is designed for use in real-time DSP applications where smooth audio transitions are required.

---

## 🎯 Associated Enumerations and Structures

### FadeState
Enumeration defining the possible states of the audio crossfade process. It allows tracking the lifecycle of a transition between two sources.

| Element | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `NO_FADE` | `0` | No active fade - stable state where only one source is active |
| `FADING_IN_B` | A to B Transition | Transition in progress from source A to source B (gainA decreases, gainB increases) |
| `FADING_OUT_A` | B to A Transition | Transition in progress from source B to source A (gainA increases, gainB decreases) |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(uint32_t sampleRate, float fadeTimeSeconds)` |
| **Description** | Initializes the audio fader by configuring the sample rate and transition duration. Automatically calculates the total number of samples needed for a complete fade based on these parameters. Resets internal state to NO_FADE. |
| **Parameter(s)** | |
| `sampleRate` | Audio sample rate in Hz (e.g., 44100, 48000). Used to calculate the number of samples per second. |
| `fadeTimeSeconds` | Fade transition duration in seconds. Determines how long a complete transition between two sources will take. |
| **Return** | None (void) |

---

### **startFadeInB**

| Element | Details |
|:---|:---|
| **Method** | `void startFadeInB()` |
| **Description** | Starts a crossfade transition from source A to source B. Resets the sample counter to zero and sets the state to FADING_IN_B. During this transition, source A will gradually decrease while source B increases until it becomes the only active source. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **startFadeOutA**

| Element | Details |
|:---|:---|
| **Method** | `void startFadeOutA()` |
| **Description** | Starts a crossfade transition from source B to source A. Resets the sample counter to zero and sets the state to FADING_OUT_A. During this transition, source B will gradually decrease while source A increases until it becomes the only active source. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `void Process(float inputA1, float inputA2, float inputB1, float inputB2, float& output1, float& output2)` |
| **Description** | Main processing method that applies crossfade to incoming audio samples. Calculates appropriate gains for each source based on current state and fade progress, then mixes the two sources to produce outputs. Supports stereo processing with two channels (1 and 2) per source. |
| **Parameter(s)** | |
| `inputA1` | Audio sample for channel 1 of source A |
| `inputA2` | Audio sample for channel 2 of source A |
| `inputB1` | Audio sample for channel 1 of source B |
| `inputB2` | Audio sample for channel 2 of source B |
| `output1` | [output] Mixed audio sample for channel 1 (modified by reference) |
| `output2` | [output] Mixed audio sample for channel 2 (modified by reference) |
| **Return** | None (void) |

---

### **getProgress**

| Element | Details |
|:---|:---|
| **Method** | `float getProgress() const` |
| **Description** | Returns the current fade progress normalized between 0.0 (start) and 1.0 (end). Useful for displaying visual indicators or debugging. If no fade is active, returns the final state of the last transition. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 and 1.0 representing current fade progress |

---

### **getState**

| Element | Details |
|:---|:---|
| **Method** | `FadeState getState() const` |
| **Description** | Returns the currently active crossfade state. Allows determining whether the system is in stable mode or executing a transition, as well as the type of transition in progress. |
| **Parameter(s)** | None |
| **Return** | FadeState value indicating current state (NO_FADE, FADING_IN_B, or FADING_OUT_A) |

---

### **getLastState**

| Element | Details |
|:---|:---|
| **Method** | `FadeState getLastState() const` |
| **Description** | Returns the state of the last completed fade transition. Useful for determining which source is currently active after a completed transition (FADING_IN_B means B is active, FADING_OUT_A means A is active). |
| **Parameter(s)** | None |
| **Return** | FadeState value indicating the last completed fade state |

---

### **isFading**

| Element | Details |
|:---|:---|
| **Method** | `bool isFading() const` |
| **Description** | Checks if a crossfade operation is currently in progress. Inline method for fast state access without expensive function calls. Returns true if the system is not in the NO_FADE state. |
| **Parameter(s)** | None |
| **Return** | `true` if a transition is active, `false` otherwise |

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
| `m_sampleRate` | `uint32_t` | Audio sample rate in Hz (e.g., 44100, 48000). Used to calculate total number of samples needed. |
| `m_fadeTimeSeconds` | `float` | Fade transition duration in seconds. Determines how fast transitions occur. |
| `m_totalFadeSamples` | `int` | Total number of samples required for a complete transition. Automatically calculated as `sampleRate × fadeTimeSeconds`. |
| `m_currentFadeSample` | `int` | Current sample position in the fade sequence. Incremented with each Process call during an active transition. |
| `m_state` | `FadeState` | Currently active crossfade state. Can be NO_FADE, FADING_IN_B, or FADING_OUT_A. |
| `m_lastState` | `FadeState` | Last completed fade state. Maintains history of the last completed transition to determine which source is active after a fade. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cAudioFader.h"

using namespace DadDSP;

int main() {
    // Initialize the audio fader
    // Sample rate: 48000 Hz, Fade duration: 2.0 seconds
    cAudioFader audioFader;
    audioFader.Initialize(48000, 2.0f);
    
    // FadeState variables to read crossfade states
    FadeState State;
    FadeState lastState;

    // Audio inputs and outputs
    float inputA1 = 0.0f, inputA2 = 0.0f;  // Source A (stereo)
    float inputB1 = 0.0f, inputB2 = 0.0f;  // Source B (stereo)
    float output1 = 0.0f, output2 = 0.0f;  // Mixed outputs
        
    lastState = audioFader.getLastState();  // Returns FADING_OUT_A
    
    // Smooth transition from source A to source B ---
    
    // Start A to B transition
    audioFader.startFadeInB();
    
    // Execute crossfade over 48000 samples (2 seconds)
    for (int i = 0; i < 48000; i++) {
        inputA1 = inputA2 = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);  // A4 note on A
        inputB1 = inputB2 = 0.0f;

        audioFader.Process(inputA1, inputA2, inputB1, inputB2, output1, output2);

        // Exit loop when fading is complete
        // audioFader.isFading() returns false after completion
        if(!audioFader.isFading()) break;
    }
    State     = audioFader.getState();      // Returns NO_FADE
    lastState = audioFader.getLastState();  // Returns FADING_IN_B
 
    // Smooth transition from source B to source A ---
    
    // Start a new transition from B to A
    audioFader.startFadeOutA();

    for (int i = 0; i < 48000; i++) {
        inputA1 = inputA2 = std::sin(2.0f * M_PI * 440.0f * i / 48000.0f);  // A4 note on A
        inputB1 = inputB2 = std::sin(2.0f * M_PI * 880.0f * i / 48000.0f);  // C#5 note on B
        
        audioFader.Process(inputA1, inputA2, inputB1, inputB2, output1, output2);
        
        // Check fade progress
        float progress = audioFader.getProgress();

        // Exit loop when fading is complete
        // audioFader.isFading() returns false after completion
        if(!audioFader.isFading()) break;
    }
    
    FadeState lastState = audioFader.getLastState();  // Returns FADING_OUT_A
       
    return 0;
}
```
---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.