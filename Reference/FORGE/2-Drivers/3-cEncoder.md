---
title: cEncoder
parent: Drivers
layout: default
nav_order: 3
---
# DadDrivers::cEncoder

**Namespace:** DadDrivers::cEncoder  
**Files:** cEncoder.h / cEncoder.cpp   
**Directory:** DAD_FORGE/Drivers   
**Description:** Rotary encoder management class with debouncing functionality

---

## 📋 Class Description

The `cEncoder` class manages the interface of a 3-contact rotary encoder (channels A, B, and switch). It implements a rotation direction detection algorithm based on GPIO signal transitions, as well as debounce management for the switch contact. The class provides incremented values since the last read, allowing simple integration into UI navigation or music control applications.

---

## 🎯 Enumerations and Associated Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(GPIO_TypeDef* pAPort, uint16_t APIn, GPIO_TypeDef* pBPort, uint16_t BPIn, GPIO_TypeDef* pSWPort, uint16_t SWPIn, uint32_t EncoderUpdatePeriod, uint32_t SwitchUpdatePeriod)` |
| **Description** | Initializes the encoder with GPIO configurations and timing parameters for signal processing. Configures input ports for channels A, B, and the switch, as well as update periods for debouncing. |
| **Parameter(s)** | |
| `pAPort` | Pointer to the GPIO port register configured for channel A (e.g., GPIOA) |
| `APIn` | GPIO pin number for channel A (e.g., GPIO_PIN_0) |
| `pBPort` | Pointer to the GPIO port register configured for channel B (e.g., GPIOB) |
| `BPIn` | GPIO pin number for channel B (e.g., GPIO_PIN_1) |
| `pSWPort` | Pointer to the GPIO port register configured for the switch (e.g., GPIOC) |
| `SWPIn` | GPIO pin number for the switch (e.g., GPIO_PIN_2) |
| `EncoderUpdatePeriod` | Encoder update period in 1/SAMPLING_RATE units (for debounce and direction detection) |
| `SwitchUpdatePeriod` | Switch update period in 1/SAMPLING_RATE units (for debounce processing with integration) |
| **Return** | None |

### **Debounce**

| Element | Details |
|:---|:---|
| **Method** | `void Debounce()` |
| **Description** | Processes debouncing of encoder and switch signals, updates position and state. Reads GPIO states, detects transitions to determine rotation direction, and applies temporal filtering to avoid false triggers. Must be called regularly from an interrupt routine or timer. |
| **Parameter(s)** | None |
| **Return** | None |

### **getIncrement**

| Element | Details |
|:---|:---|
| **Method** | `int8_t getIncrement()` |
| **Description** | Returns the current encoder position increment since the last read and resets the counter. Allows atomic read without risk of data loss in case of concurrent interruption. |
| **Parameter(s)** | None |
| **Return** | `int8_t` value representing the position increment (-nbStep, 0, +nbStep) |

### **getSwitchState**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getSwitchState() const` |
| **Description** | Returns the current state of the switch after debounce processing. The state is stable and filtered to avoid contact bounce. |
| **Parameter(s)** | None |
| **Return** | `uint8_t` value: 0 = switch released (not pressed), 1 = switch pressed |

---

## 🔒 Protected/Private Methods

No protected or private methods.

---

## 📦 Data Members (Variables)

### Public Variables

No public variables.

### Protected/Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pGPIO_APort` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for channel A |
| `m_GPIO_APin` | `uint16_t` | GPIO pin number for channel A (PIN_MASK) |
| `m_pGPIO_BPort` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for channel B |
| `m_GPIO_BPin` | `uint16_t` | GPIO pin number for channel B (PIN_MASK) |
| `m_pGPIO_SWPort` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for the switch |
| `m_GPIO_SWPin` | `uint16_t` | GPIO pin number for the switch (PIN_MASK) |
| `m_ctEncoderPeriod` | `uint32_t` | Incremental counter for encoder debounce (sample count since last update) |
| `m_EncoderUpdatePeriod` | `uint32_t` | Encoder update period in 1/SAMPLING_RATE units |
| `m_A` | `uint8_t` | Current state of channel A (bits 0-1: values 0x00 to 0x03) |
| `m_B` | `uint8_t` | Current state of channel B (bits 0-1: values 0x00 to 0x03) |
| `m_Inc` | `int8_t` | Encoder position increment value (-1, 0, or +1) |
| `m_ctSwitchPeriod` | `uint32_t` | Incremental counter for switch debounce (sample count since last update) |
| `m_SwitchUpdatePeriod` | `uint32_t` | Switch update period in 1/SAMPLING_RATE units |
| `m_ctSwitchIntegrate` | `int32_t` | Switch signal integration counter for stable debounce (values clamped between -10 and +10) |

---

## 💡 Usage Example

```cpp

#include "DadDrivers/cEncoder.h"

using namespace DadDrivers;

int main()
{
    cEncoder encoder;

    // Encoder initialization
    // GPIOA Pin0, GPIOB Pin1 for A/B channels
    // GPIOC Pin2 for the switch
    // Periods of 500 units (1/20kHz = 50µs)
    encoder.Init(GPIOA, GPIO_PIN_0,
                 GPIOB, GPIO_PIN_1,
                 GPIOC, GPIO_PIN_2,
                 500u, 500u);

    // Main loop with real-time processing
    while(1) {
        // Call Debounce regularly (e.g., from Timer ISR at 20kHz)
        encoder.Debounce();

        // Read increment
        int8_t increment = encoder.getIncrement();
        
        if (increment != 0) {
            // Rotation detected, update UI position
            uint32_t newPosition += (uint32_t)increment;
        }

        // Read switch state
        uint8_t switchState = encoder.getSwitchState();
        if (switchState == 1) {
            // Switch pressed - trigger action
            ButtonPressed();
        }
    }

    return 0;
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
