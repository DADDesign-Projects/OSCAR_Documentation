---
title: cBypassOnOffManager
parent: GUI Components
layout: default
nav_order: 9
---

# DadGUI::cBypassOnOffManager

**Namespace:** DadGUI::cBypassOnOffManager  
**Files:** cBypassOnOffManager.h / cBypassOnOffManager.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Inheritance**: `iGUI_EventListener`  
**Description:** Manages state transitions between Bypass, Off, and On for audio effects, including fade-in/fade-out management.

---

## 📋 Class Description
The `cBypassOnOffManager` class ensures smooth transitions between different operational modes of an audio effect: **Bypass**, **Off**, and **On**. It plays a crucial role in user experience by managing volume transitions via the `DadDrivers::cDryWet` component to avoid abrupt signal jumps (audio clicks) during state changes. It synchronizes the interface state with the actual effect state via a pointer to a global state variable.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"HardwareAndCo.h"` | `DAD_FORGE/Hardware` | Access to hardware definitions (GPIO, Pins) |
| `"GUI_Event.h"` | `DAD_FORGE/GUI` | GUI event management |
| `"cDryWet.h"` | `DAD_FORGE/Drivers` | Dry/Wet mixing management for fades |
| `"GPIO.h"` | `DAD_FORGE/Drivers` | GPIO pin control (Bypass Relay) |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `void FadeToOn()` | `DadDrivers/cDryWet` | Triggers a fade to the "On" state |
| `void FadeToBypass()` | `DadDrivers/cDryWet` | Triggers a fade to the "Bypass" state |
| `void FadeToOff()` | `DadDrivers/cDryWet` | Triggers a fade to the "Off" state |
| `void SetPIN(Pin)` | `Utilities` | Hardware control of the bypass relay |
| `void ResetPIN(Pin)` | `Utilities` | Resets the bypass relay |
| `void SetPIN(Pin)` | `Utilities` | Audio mute control |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__DryWet` | `DadDrivers::cDryWet` | `Global Driver` | Object controlling signal mixing |
| `__GUI_EventManager` | `DadUtilities::EventManager` | `Global GUI Context` | Event subscription manager |

---

## 🎯 Associated Enumerations and Structures

### **eInternalEffectState_t**
Represents the internal transition states of the effect.

| Element | Value | Description |
|:---|:---|:---|
| `bypass` | `0` | Signal passes directly without the effect. |
| `off` | `1` | Effect is disabled (silence or dry signal depending on configuration). |
| `on` | `2` | Effect is active. |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(volatile uint8_t* pEffectState)` |
| **Description** | Initializes the manager with a pointer to the effect state, resets internal states to 'bypass', and configures initial GPIO outputs. |
| **Parameter(s)** | |
| `pEffectState` | Pointer to the global variable representing the effect state. |
| **Return** | `void` |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Checks if the target state has changed compared to the global state. If a change is detected, it triggers the appropriate transition (Fade). It also monitors the completion of fade processes. |
| **Return** | `void` |

### **getState**

| Element | Details |
|:---|:---|
| **Method** | `eInternalEffectState_t getState() const` |
| **Description** | Returns the current internal state of the effect (used for GUI synchronization). |
| **Return** | `eInternalEffectState_t` |

---

## 🔒 Protected / Private Methods

### **BypassToOn**

| Element | Details |
|:---|:---|
| **Method** | `inline void BypassToOn()` |
| **Description** | Transitions from Bypass mode to On mode: activates the bypass relay and starts the fade to the "On" state. |
| **Return** | `void` |

### **OnToBypass**

| Element | Details |
|:---|:---|
| **Method** | `inline void OnToBypass()` |
| **Description** | Transitions from On mode to Bypass mode: starts the fade to Bypass. |
| **Return** | `void` |

### **OffToBypass**

| Element | Details |
|:---|:---|
| **Method** | `inline void OffToBypass()` |
| **Description** | Transitions from Off mode to Bypass mode: starts the fade to Bypass. |
| **Return** | `void` |

### **OnToOff**

| Element | Details |
|:---|:---|
| **Method** | `inline void OnToOff()` |
| **Description** | Transitions from On mode to Off mode: starts the fade to the "Off" state. |
| **Return** | `void` |

### **OffToOn**

| Element | Details |
|:---|:---|
| **Method** | `inline void OffToOn()` |
| **Description** | Transitions from Off mode to On mode: starts the fade to the "On" state. |
| **Return** | `void` |

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected / Private Variables
 
| Member | Type | Description |
|:---|:---|:---|
| `m_TargetState` | `eInternalEffectState_t` | Target state desired by the user. |
| `m_InternalState` | `eInternalEffectState_t` | Current state of the effect (after fade completion). |
| `m_pEffectState` | `volatile uint8_t*` | Pointer to the global effect state variable. |
| `m_OldEffectState` | `eInternalEffectState_t` | Stores the previous state to detect changes. |
| `m_FadeInProcess` | `bool` | Flag indicating whether a fade is in progress. |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.