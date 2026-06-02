---
title: cDryWet
parent: Drivers
layout: default
nav_order: 2
---
# DadDrivers::cDryWet

**Namespace:** DadDrivers::cDryWet
**Files:** cDryWet.h / cDryWet.cpp
**Directory:** DAD_FORGE/Drivers
**Description:** Dry/wet mix control class with smooth crossfade for audio effects

---

## 📋 Class Description

The `cDryWet` class manages the dry/wet ratio control in audio processing, enabling smooth transitions between BYPASS, OFF, and ON states. The dry channel is controlled by a hardware component (PGA2310/PGA2311) via SPI, while the wet channel is managed by the user application. The class implements an equal-power crossfade function (sin/cos) to maintain constant perceived volume during transitions.

{: .note}
> The class inherits from `DadGUI::iGUI_EventListener`, an interface for intercepting GUI events. It implements the virtual methods `on_GUI_FastUpdate()` for fast redundant processing and `on_GUI_RT_Process()` for real-time audio loop operations.

---

## 🎯 Enumerations and Associated Structures

### eDryWetState_t

| Element / Variable | Value | Description |
|:---|:---|:---|
| `bypass` | 0 | BYPASS state: complete silent signal |
| `off` | 1 | OFF state: effect disabled, dry signal at maximum volume |
| `on` | 2 | ON state: effect active with configured dry/wet mix |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(float MinDry, float MaxDry, float TimeChange)` |
| **Description** | Initializes dry/wet mix parameters and defines gain ranges for the hardware channel. Configures transition duration for fades between states. |
| **Parameter(s)** | |
| `MinDry` | Minimum dry channel gain in dB (hardware range: -95.5 to 31.5 dB) |
| `MaxDry` | Maximum dry channel gain in dB (hardware range: -95.5 to 31.5 dB) |
| `TimeChange` | Complete transition duration for a fade between two states, in seconds |
| **Return** | None |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Updates the target mix state based on ON/OFF/BYPASS status. Determines whether the fade should go to 0% wet (OFF), user mix (ON), or complete silence (BYPASS). |
| **Parameter(s)** | None |
| **Return** | None |

### **on_GUI_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_RT_Process()` |
| **Description** | Performs smooth crossfading between dry and wet signals by progressively adjusting the mix value. Calculates dry and wet gains using a sin/cos function to maintain constant perceived volume. Automatically transmits the new gain to the hardware channel via SPI when necessary. |
| **Parameter(s)** | None |
| **Return** | None |

### **setMix**

| Element | Details |
|:---|:---|
| **Method** | `void setMix(float Mix)` |
| **Description** | Sets the target mix level as a percentage (0.0 = fully dry, 100.0 = fully wet). Automatically converts the percentage to a normalized value (0-1) and updates the target state if the effect is ON. |
| **Parameter(s)** | |
| `Mix` | Mix level as a percentage (0.0 to 100.0). 0% = dry only signal, 50% = equal mix, 100% = wet only signal |
| **Return** | None |

### **setNormalizedMix**

| Element | Details |
|:---|:---|
| **Method** | `void setNormalizedMix(float Mix)` |
| **Description** | Sets the target mix level as a normalized value (0.0 = fully dry, 1.0 = fully wet). Used directly by the application for precise ratio control. |
| **Parameter(s)** | |
| `Mix` | Normalized mix level (0.0 to 1.0). 0.0 = dry only signal, 0.5 = equal mix, 1.0 = wet only signal |
| **Return** | None |

### **FadeToOn**

| Element | Details |
|:---|:---|
| **Method** | `void FadeToOn()` |
| **Description** | Initiates fade to ON state (effect active). |
| **Parameter(s)** | None |
| **Return** | None |

### **FadeToOff**

| Element | Details |
|:---|:---|
| **Method** | `void FadeToOff()` |
| **Description** | Initiates fade to OFF state (effect disabled). The mix will return to 0% wet (fully dry) with progressive fading. |
| **Parameter(s)** | None |
| **Return** | None |

### **FadeToBypass**

| Element | Details |
|:---|:---|
| **Method** | `void FadeToBypass()` |
| **Description** | Initiates fade to BYPASS state (complete silence). All channels are reduced to zero during the transition. |
| **Parameter(s)** | None |
| **Return** | None |

### **getState**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getState()` |
| **Description** | Returns the current state of the class after all fades are completed. Useful for checking if dry/wet processing is active or disabled. |
| **Parameter(s)** | None |
| **Return** | `uint8_t` value corresponding to the `eDryWetState_t` enumeration (0=bypass, 1=off, 2=on) |

### **getGainWet**

| Element | Details |
|:---|:---|
| **Method** | `float getGainWet()` |
| **Description** | Returns the current wet gain value (0.0 to 1.0). Useful for the user application that needs to apply this gain to the wet signal before final mixing. |
| **Parameter(s)** | None |
| **Return** | `float` value representing the current wet gain (0.0 = silent, 1.0 = full volume) |

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
| `m_OldComputedDryGain` | `float` | Previous computed dry gain for change detection (avoids unnecessary hardware updates) |
| `m_CurrentWetGain` | `float` | Current wet gain (0.0 to 1.0), calculated from the current mix value |
| `m_CurrentDryGain` | `float` | Current dry gain (0.0 to 1.0), calculated from the current mix value |
| `m_CurrentMix` | `float` | Current mix value during fade (progresses toward target) |
| `m_TargetMix` | `float` | Target mix value (determined by ON/OFF/BYPASS state or setMix) |
| `m_UserMix` | `float` | User-defined mix parameter (used only when effect is in ON state) |
| `m_PreviousMix` | `float` | Previous mix value for change detection in real-time processing |
| `m_MixIncrement` | `float` | Mix increment per sample for smooth transitions (RT_TIME / TimeChange) |
| `m_MinDry` | `float` | Minimum dry gain (PGA2310/11 hardware index, range -95.5 to 31.5 dB) |
| `m_MaxDry` | `float` | Maximum dry gain (PGA2310/11 hardware index, range -95.5 to 31.5 dB) |
| `m_OldDryVolumeIndex` | `uint32_t` | Previous hardware volume index to avoid unnecessary SPI transmissions |
| `m_State` | `eDryWetState_t` | Target state (bypass, off, or on) defined by the GUI application |
| `m_CurrentState` | `eDryWetState_t` | Actual current state after all fades are completed |

---

## 💡 Usage Example

```cpp

#include "DadDrivers/cDryWet.h"

using namespace DadDrivers;

int main()
{
    // cDryWet class initialization
    // Range: -60.0dB to 0.0dB, transition in 2 seconds
    cDryWet dryWet;
    dryWet.Init(-60.0f, 0.0f, 2.0f);

    dryWet.setMix(50.0f);   // Set mix to 50% (equal)
    
    // Based on effect state change, the GUI calls:
    
    dryWet.FadeToOn();      // Activate effect with fade
    // Wait for fade to complete
    while(dryWet.getState() != eDryWetState_t::on);

    // or 

    dryWet.FadeToOff();     // Deactivate effect with fade
    // Wait for fade to complete
    while(dryWet.getState() != eDryWetState_t::off);
    // 100% Dry, 0% Wet
    
    // or

    dryWet.FadeToBypass();  // Mute
    // Wait for fade to complete
    while(dryWet.getState() != eDryWetState_t::bypass);
    // 0% Dry, 0% Wet
    
    ...

    // In main audio loop 
    float in, out;
    while(1) {
        ...
        ...
        float gainWet = dryWet.getGainWet();
        out = in * gainWet;
    }

    return 0;
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
