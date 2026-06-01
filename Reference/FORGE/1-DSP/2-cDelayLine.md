---
title: cDelayLine
parent: DSP
layout: default
nav_order: 2
---
# DadDSP::cDelayLine

**Namespace :** DadDSP::cDelayLine  
**Files :** cDelayLine.h / cDelayLine.cpp   
**Directory :** DAD_FORGE/DSP   
**Description :** Implementation of a delay line (FIFO buffer) for digital signal processing, including audio effects and temporal modifications.

---

## 📋 Class Description

The **cDelayLine** class is a fundamental structure for Digital Signal Processing (DSP), designed as a circular buffer (FIFO - First In, First Out) allowing temporary storage of audio samples. It provides essential functionalities for temporal effects such as echo, reverberation, and flanging.

This class efficiently manages a circular buffer that allows:
- Sequential storage of incoming audio samples
- Retrieval of samples with different timed delays
- Linear interpolation for non-integer (fractional) delays
- Automatic buffer wrapping without data loss

---

## 🎯 Associated Enumerations and Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **cDelayLine**

| Element | Details |
|:---|:---|
| **Method** | `cDelayLine()` |
| **Description** | Default constructor for the cDelayLine class. Initializes data members to their default values (nullptr for the buffer, 0 for counters). Allows object instantiation without external memory allocation. |

---

### **~cDelayLine**

| Element | Details |
|:---|:---|
| **Method** | `~cDelayLine()` |
| **Description** | Default destructor for the cDelayLine class. Releases resources associated with the object (note: the buffer itself is not dynamically allocated by this class, so no explicit release is necessary). |

---

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(float* buffer, uint32_t bufferSize)` |
| **Description** | Initializes the delay line with an external memory buffer provided. Configures the circular buffer by defining a pointer to the memory address of the buffer and its size. Automatically calls Clear() to initialize the content to zero before use. Useful when the buffer is already allocated elsewhere in the system (e.g., dynamic allocation or global buffer). |
| **Parameter(s)** | |
| `buffer` | Pointer to an array of floating-point numbers (`float*`) containing the memory buffer where audio samples will be stored. The buffer must be pre-allocated with sufficient size. |
| `bufferSize` | Buffer size in number of samples (uint32_t). Determines the maximum storage capacity and the maximum possible delay of the delay line. |
| **Return** | No return value (void). |

---

### **Clear**

| Element | Details |
|:---|:---|
| **Method** | `void Clear()` |
| **Description** | Completely empties the buffer by setting all values to zero. Uses memset for a fast and efficient operation. Useful for resetting the delay line between two audio processing segments or when it is necessary to eliminate any residual content from the buffer. |
| **Parameter(s)** | None. |
| **Return** | No return value (void). |

---

### **Push**

| Element | Details |
|:---|:---|
| **Method** | `void Push(float inputSample)` |
| **Description** | Adds a new audio sample to the delay line. The sample is stored in the circular buffer at the current position, then the current index is incremented with automatic wrapping. This method represents the data entry (input stream) into the FIFO system. |
| **Parameter(s)** | |
| `inputSample` | Floating-point audio sample (`float`) to add to the buffer. Value is generally between -1.0 and 1.0 for normalized audio signals. |
| **Return** | No return value (void). |

---

### **Pull**

| Element | Details |
|:---|:---|
| **Method** | `float Pull(uint32_t delay)` |
| **Description** | Retrieves an audio sample from the buffer with a fixed integer delay in number of samples. Uses circular wrapping to calculate the correct output index. Returns the sample directly without interpolation, ideal for precise and deterministic delays. |
| **Parameter(s)** | |
| `delay` | Delay in number of samples (uint32_t). Determines how many previous samples will be retrieved. Must be less than the buffer size for correct operation. |
| **Return** | Floating-point audio sample (`float`) representing the sample with the specified delay, or 0.0f if the buffer is not initialized. |

---

### **Pull**

| Element | Details |
|:---|:---|
| **Method** | `float Pull(float delay)` |
| **Description** | Retrieves an audio sample with linear interpolation for fractional (non-integer) delays. Allows for precise and smooth temporal shifts, essential for effects like flanger or chorus. Calculates the interpolation between two adjacent buffer samples. |
| **Parameter(s)** | |
| `delay` | Delay in floating-point number of samples (`float`). Can contain a fractional part (e.g., 45.7) to achieve precise delays beyond single-sample resolution. |
| **Return** | Floating-point audio sample (`float`) linearly interpolated between the two adjacent samples, or 0.0f if the buffer is not initialized. |

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
| `m_Buffer` | `float*` | Pointer to the allocated memory buffer (external or internal). Stores audio samples in a circular structure. Initialized to nullptr by default, must be initialized via Initialize(). |
| `m_NumElements` | `int32_t` | Number of elements in the buffer. Represents the buffer size and serves as a limit for circular wrapping operations. Initialized to 0 by default. |
| `m_CurrentIndex` | `int32_t` | Current index (zero delay position). Points to the most recent write location in the circular buffer. Incremented upon each Push() call with automatic wrapping. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cDelayLine.h"

int main() {
    // Configuration: Buffer size = 4410 samples (~50ms at 44.1kHz)
    const uint32_t BUFFER_SIZE = 4410;
    
    // Dynamic allocation of the audio buffer
    float* delayBuffer = new float[BUFFER_SIZE];
    
    // Initialization of the delay line
    DadDSP::cDelayLine delayLine;
    delayLine.Initialize(delayBuffer, BUFFER_SIZE);
    
    // Audio processing - sample loop
    for (int32_t sample = 0; sample < 44100; sample++) {
        // Input audio sample (e.g., from microphone or file)
        float inputSample = GetAudioInput(sample);
        
        // Add the sample to the delay line
        delayLine.Push(inputSample);
        
        // Retrieve the sample with a delay of 20 samples (~0.45ms)
        float delayedSample = delayLine.Pull(20u);
        
        // Mix direct signal and delayed signal (slapback effect)
        float outputSample = inputSample * 0.7f + delayedSample * 0.3f;
        
        // Additional processing...
        ProcessAudioOutput(outputSample, sample);
    }
    
    // Cleanup - interpolation for fractional delay (e.g., 25.5 samples)
    delayLine.Initialize(delayBuffer, BUFFER_SIZE);
    for (int32_t sample = 0; sample < 44100; sample++) {
        delayLine.Push(inputSample);
        
        // Delay with interpolation for smoother effect
        float fractionalDelay = 25.5f;
        float interpolatedOutput = delayLine.Pull(fractionalDelay);
    }
    
    // Resource release
    delete[] delayBuffer;
    
    return 0;
}
```

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.