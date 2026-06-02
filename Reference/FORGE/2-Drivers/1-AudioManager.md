---
title: AudioManager
parent: Drivers
layout: default
nav_order: 1
---
# DadDrivers::AudioManager

**Namespace:** DadDrivers (external module)  
**Files:** AudioManager.h / AudioManager.cpp  
**Directory:** DAD_FORGE/Drivers  
**Description:** The AudioManager module manages the full-duplex stereo SAI interface of the STM32H.

---

## 📋 Module Description

The AudioManager module manages the SAI (Serial Audio Interface) of the STM32H microcontroller in full-duplex stereo mode.  
It handles both reading (recording) and writing (playback) of the audio signal in a high-performance, real-time manner, relying on interrupts and DMA transfers.

### Main Features

* **Double-buffer management:** The module uses a double-buffering technique to allow continuous audio processing without audible latency: while the DMA fills one buffer, the processor can process the other in parallel.
* **Optimized conversions:** Automatic and efficient conversion between the native int32 format of the SAI and the float format used for audio processing (and vice versa).

---

## 🎯 Associated Enumerations and Structures

### AudioBuffer

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `Right` | `float` | Right channel audio data |
| `Left` | `float` | Left channel audio data |

---

## User-Defined Constant
`AUDIO_BUFFER_SIZE`: Size of the audio buffer, typically 4 samples (determines latency).

---

## 📚 External Function

### **AudioCallback**

| Element | Details |
|:---|:---|
| **Method** | `void AudioCallback(AudioBuffer *pIn, AudioBuffer *pOut)` |
| **Description** | External callback for user audio processing (must be defined in user code). Receives raw incoming audio data (converted from int32 to float) and returns processed data. |
| **Parameter(s)** | |
| `pIn` | Pointer to input audio data in `AudioBuffer` format |
| `pOut` | Pointer to output `AudioBuffer` audio data to be filled by the user with the processed signal |
| **Return** | None |

## 📚 Public Function

### **StartAudio**

| Element | Details |
|:---|:---|
| **Method** | `HAL_StatusTypeDef StartAudio(SAI_HandleTypeDef *phSaiTx, SAI_HandleTypeDef *phSaiRx)` |
| **Description** | Initializes and starts double-buffer audio processing. Configures input/output buffers (In, Out1, Out2), initializes DMA rxBuffer/txBuffer to zero, attaches SAI handles to DMA transfers, and enables completion interrupts. The pOut pointer is initialized to point to Out1 for the first output buffer. Returns HAL_OK if both DMA starts (Tx and Rx) succeed. |
| **Parameter(s)** | |
| `phSaiTx` | Pointer to the SAI handle configured for transmission (audio output) |
| `phSaiRx` | Pointer to the SAI handle configured for reception (audio input) |
| **Return** | HAL status code: `HAL_OK` success, `HAL_ERROR` DMA initialization failure |

---

## 🔒 Private Functions

### **ConvertToAudioBuffer**

| Element | Details |
|:---|:---|
| **Method** | `ConvertToAudioBuffer(const int32_t* __restrict intBuf, AudioBuffer* __restrict floatBuf)` |
| **Description** | Convert int32_t buffer to float AudioBuffer (Optimized). |
| **Parameter(s)** | |
| `intBuf` | Pointer to input audio data in `int32` format. |
| `floatBuf` | Pointer to output `AudioBuffer` audio data. |
| **Return** | None |

### **ConvertFromAudioBuffer**

| Element | Details |
|:---|:---|
| **Method** | `void ConvertFromAudioBuffer(const AudioBuffer* __restrict floatBuf, int32_t* __restrict intBuf)` |
| **Description** | Convert float AudioBuffer to int32_t buffer (Optimized). |
| **Parameter(s)** | |
| `floatBuf` | Pointer to input `AudioBuffer` audio data. |
| `intBuf` | Pointer to output audio data in `int32` format. |
| **Return** | None |

### **HAL_SAI_TxCpltCallback**

| Element | Details |
|:---|:---|
| **Method** | `HAL_SAI_TxCpltCallback(SAI_HandleTypeDef *hsai)` |
| **Description** | SAI Callback called by the DMA interrupt when the transmission buffer is full. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| **Return** | None |

### **HAL_SAI_TxHalfCpltCallback**

| Element | Details |
|:---|:---|
| **Method** | `HAL_SAI_TxHalfCpltCallback(SAI_HandleTypeDef *hsai)` |
| **Description** | SAI Callback called by the DMA interrupt when the transmission buffer is half full. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| **Return** | None |

### **HAL_SAI_RxCpltCallback**

| Element | Details |
|:---|:---|
| **Method** | `HAL_SAI_RxCpltCallback(SAI_HandleTypeDef *hsai)` |
| **Description** | SAI Callback called by the DMA interrupt when the reception buffer is full. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| **Return** | None |

### **HAL_SAI_RxHalfCpltCallback**

| Element | Details |
|:---|:---|
| **Method** | `HAL_SAI_RxHalfCpltCallback(SAI_HandleTypeDef *hsai)` |
| **Description** | SAI Callback called by the DMA interrupt when the reception buffer is half full. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| **Return** | None |

### **ProcessTxCallback**

| Element | Details |
|:---|:---|
| **Method** | `void ProcessTxCallback(SAI_HandleTypeDef *hsai, int32_t* targetBuffer)` |
| **Description** | Called by SAI transmission callbacks. Performs conversion and transfer from the user buffer to the DMA transmission buffer. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| `targetBuffer` | Pointer to the concerned DMA buffer. |
| **Return** | None |

### **ProcessRxCallback**

| Element | Details |
|:---|:---|
| **Method** | `void ProcessRxCallback(SAI_HandleTypeDef *hsai, int32_t* sourceBuffer, AudioBuffer* targetFloatBuf)` |
| **Description** | Called by SAI reception callbacks. Performs conversion and transfer from the DMA buffer to the user buffer. |
| **Parameter(s)** | |
| `hsai` | Pointer to the handle of the concerned SAI. |
| `sourceBuffer` | Pointer to the concerned DMA buffer. |
| `targetFloatBuf` | Pointer to the concerned user buffer. |
| **Return** | None |

---

## 📦 Data

### Variables

| Member | Type | Description |
|:---|:---|:---|
| `In` | `AudioBuffer[AUDIO_BUFFER_SIZE]` | Stereo float audio input buffer (non-cached, 8-byte aligned) |
| `Out1` | `AudioBuffer[AUDIO_BUFFER_SIZE]` | First stereo float audio output buffer for double-buffering (non-cached, 8-byte aligned) |
| `Out2` | `AudioBuffer[AUDIO_BUFFER_SIZE]` | Second stereo float audio output buffer for double-buffering (non-cached, 8-byte aligned) |
| `pOut` | `volatile AudioBuffer*` | Current pointer to the active output buffer (swapped after each DMA reception) |
| `rxBuffer` | `int32_t[SAI_BUFFER_SIZE]` | DMA buffer for SAI reception (int32 format, non-cached, 8-byte aligned) |
| `txBuffer` | `int32_t[SAI_BUFFER_SIZE]` | DMA buffer for SAI transmission (int32 format, non-cached, 8-byte aligned) |
| `__phSaiTx` | `SAI_HandleTypeDef*` | Globally stored transmission SAI handle for callbacks |
| `__phSaiRx` | `SAI_HandleTypeDef*` | Globally stored reception SAI handle for callbacks |

{: .warning}
> The audio buffers used by the DMA are placed in a non-cached memory region.
> This arrangement is important because it avoids data inconsistency issues between the processor cache and the memory accessible by the DMA controller. Without this measure, the DMA could read or write stale data present in the cache, leading to audio artifacts or unpredictable behavior.

---

## 💡 Usage Example

```cpp
#include "DadDrivers/AudioManager.h"
#include "main.h"

// User audio processing callback
void MyAudioCallback(AudioBuffer *pIn, AudioBuffer *pOut)
{
    // Custom DSP processing (e.g., equalizer, effects)
    for (size_t i = 0; i < AUDIO_BUFFER_SIZE; i++) {
        pOut[i].Left  = pIn[i].Left  * 1.5f;  // Left channel gain x1.5
        pOut[i].Right = pIn[i].Right * 1.5f;   // Right channel gain x1.5
        
        // Safety clipping protection
        if (pOut[i].Left > 1.0f) pOut[i].Left = 1.0f;
        if (pOut[i].Left < -1.0f) pOut[i].Left = -1.0f;
        if (pOut[i].Right > 1.0f) pOut[i].Right = 1.0f;
        if (pOut[i].Right < -1.0f) pOut[i].Right = -1.0f;
    }
}

int main()
{
    // SAI configuration (must be done before)
    SAI_HandleTypeDef hSaiTx;
    SAI_HandleTypeDef hSaiRx;
    
    HAL_SAI_Init(&hSaiTx, &hsai1);  // Configure Tx
    HAL_SAI_Init(&hSaiRx, &hsai2);  // Configure Rx
    
    // Start audio processing
    if (StartAudio(&hSaiTx, &hSaiRx) != HAL_OK) {
        Error_Handler();
    }
    
    // Register user callback
    AudioCallback = MyAudioCallback;
    
    // Main loop - audio runs in background via DMA
    while(1) {
        // Non real-time processing, GUI, ...
        
        // Audio processing occurs in the HAL_SAI callbacks:
        // - HAL_SAI_TxCpltCallback: converts float->int32 for Tx
        // - HAL_SAI_RxCpltCallback: converts int32->float, applies callback, swaps buffer
    }

    return 0;
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.
