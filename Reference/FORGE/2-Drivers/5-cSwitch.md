---
title: cSwitch
parent: Drivers
layout: default
nav_order: 5
---
# DadDrivers::cSwitch

**Namespace:** DadDrivers::cSwitch  
**Files:** cSwitch.h / cSwitch.cpp   
**Directory:** DAD_FORGE/Drivers   
**Description:** Debounce and state tracking class for digital switches with advanced temporal analysis

---

## 📋 Class Description

The `cSwitch` class implements a complete debounce and state tracking system for digital switches. It uses a signal integration approach combined with temporal filters (minimum/maximum period) to eliminate contact bounce. The class also calculates press duration, total press count, and maintains an exponential moving average (EMA) of the inter-press period for reliable frequency detection.

---

## 🎯 Enumerations and Associated Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(GPIO_TypeDef* pPort, uint16_t Pin, float DebounceRate, uint32_t UpdateInterval, uint32_t MinPeriod, uint32_t MaxPeriod, uint32_t AbordMaxPeriod)` |
| **Description** | Initializes the switch with hardware and timing parameters. Configures the GPIO port, input pin, debounce rate, update intervals, and temporal constraints for press validation. Resets all internal states to their default values. |
| **Parameter(s)** | |
| `pPort` | Pointer to the GPIO port register configured for the switch (e.g., GPIOA) |
| `Pin` | GPIO pin number for the switch (e.g., GPIO_PIN_0) |
| `DebounceRate` | Debounce rate in Hz, used to convert sample counters to milliseconds |
| `UpdateInterval` | State update interval in samples (delay before state change) |
| `MinPeriod` | Minimum valid period between two presses (in samples) to avoid accidental triggers |
| `MaxPeriod` | Maximum valid period between two presses (in samples) to detect long interruptions |
| `AbordMaxPeriod` | Absolute maximum period before timeout and tracking reset (in samples) |
| **Return** | None |

### **Debounce**

| Element | Details |
|:---|:---|
| **Method** | `void Debounce()` |
| **Description** | Processes the switch input with debounce and state tracking. Implements: contact bounce filtering, minimum/maximum period validation, exponential moving average (EMA) calculation between presses, and automatic timeout management. Must be called regularly from an interrupt routine or timer. |
| **Parameter(s)** | None |
| **Return** | None |

### **getState**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getState() const` |
| **Description** | Returns the current state of the switch after debounce. The state is stable and filtered to eliminate physical contact bounce. |
| **Parameter(s)** | None |
| **Return** | `uint8_t` value: 0 = switch released (not pressed), 1 = switch pressed |

### **getState (with duration)**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getState(float &PressDuration) const` |
| **Description** | Returns the switch state and calculates the press duration in milliseconds. The output parameter contains the duration since the start of the current press, converted from sample counter to time units. |
| **Parameter(s)** | |
| `PressDuration` | Output parameter (reference) containing the press duration in milliseconds |
| **Return** | `uint8_t` value: 0 = switch released, 1 = switch pressed |

### **getPeriodUpdateCount**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t getPeriodUpdateCount() const` |
| **Description** | Returns the counter of valid period updates since initialization. |
| **Parameter(s)** | None |
| **Return** | `uint32_t` value: number of valid periods between presses |

### **getPressCount**

| Element | Details |
|:---|:---|
| **Method** | `uint64_t getPressCount() const` |
| **Description** | Returns the total number of presses detected since initialization. |
| **Parameter(s)** | None |
| **Return** | `uint64_t` value: cumulative press counter |

### **getPressPeriod**

| Element | Details |
|:---|:---|
| **Method** | `float getPressPeriod() const` |
| **Description** | Calculates and returns the average press frequency in Hertz. Uses an exponential moving average (EMA) to smooth variations while maintaining responsiveness to rapid changes. Returns 0 if no valid period has been established. |
| **Parameter(s)** | None |
| **Return** | `float` value: press frequency in Hz (presses per second) |

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
| `m_GPIO_Port` | `GPIO_TypeDef*` | Reference to the hardware GPIO port configured for the switch input |
| `m_GPIO_Pin` | `uint16_t` | GPIO pin number for the switch input (PIN_MASK) |
| `m_DebounceRate` | `float` | Debounce rate in Hz, used to convert sample counters to actual time units |
| `m_UpdateInterval` | `uint32_t` | Debounce update interval in samples (delay before state change) |
| `m_DebouncePeriod` | `int32_t` | Debounce period counter (-1 = inactive, decreasing value during debounce delay) |
| `m_MinPeriod` | `uint32_t` | Minimum valid period between presses (in samples) to avoid accidental triggers |
| `m_MaxPeriod` | `uint32_t` | Maximum valid period between presses (in samples) to detect long interruptions |
| `m_AbordMaxPeriod` | `uint32_t` | Absolute maximum period before timeout and complete tracking reset |
| `m_Stop` | `uint8_t` | State flag (1 = stopped/timeout, 0 = active) |
| `m_SwitchState` | `uint8_t` | Current debounced switch state (0 = released, 1 = pressed) |
| `m_PressDuration` | `uint32_t` | Current press duration in samples since the start of the press |
| `m_CtPress` | `uint64_t` | Total press counter since initialization |
| `m_CurrentPeriod` | `uint32_t` | Time elapsed since the last valid press (in samples) |
| `m_AvgPeriod` | `float` | Exponential moving average (EMA) period between presses (smoothed with α=0.2, ±15% clamp) |
| `m_PeriodUpdateCount` | `uint32_t` | Counter of valid EMA period updates |

---

## 💡 Usage Example

```cpp
#include "DadDrivers/cSwitch.h"

using namespace DadDrivers;

int main()
{
    cSwitch switch1;

    // Switch configuration
    // GPIOA Pin0, rate 20kHz (50µs per sample)
    // Debounce interval: 300 samples (15ms)
    // Min/Max period: 50-150 samples (2.5-7.5ms)
    // Absolute timeout: 10000 samples (500ms)
    switch1.Init(GPIOA, GPIO_PIN_0,
                 20000.0f,      // DebounceRate = 20kHz
                 300u,          // UpdateInterval = 15ms
                 50u,           // MinPeriod = 2.5ms
                 150u,          // MaxPeriod = 7.5ms
                 10000u);       // AbordMaxPeriod = 500ms

    // Main loop with real-time processing
    while(1) {
        // Call Debounce regularly (e.g., from Timer ISR at 20kHz)
        switch1.Debounce();

        // Read current state
        uint8_t state = switch1.getState();
        
        // Read current state with press duration
        float pressDuration;
        uint8_t currentState = switch1.getState(pressDuration);
            
        if (pressDuration > 200.0f) { // Pressed for more than 200ms
            LongPressDetected();
        }

        float pressFrequency = switch1.getPressPeriod();
    }

    return 0;
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
