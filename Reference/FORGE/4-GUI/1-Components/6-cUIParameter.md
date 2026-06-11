---
title: cUIParameter
parent: GUI Components
layout: default
nav_order: 7
---

# DadGUI::cUIParameter

**Namespace:** DadGUI::cUIParameter  
**Files:** cUIParameter.h / cUIParameter.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Description:** Integration of the `DadDSP::cUIParameter` class into the GUI system.

---

## 📋 Class Description
The `cUIParameter` class extends the `DadDSP::cParameter` class to integrate it into the GUI.  
It adds the following features: 
1. **Persistence:** Automatic save and restore of values via `DadPersistentStorage`.
2. **Real-Time (RT):** Smooth value interpolation to avoid audio artifacts during abrupt changes.
3. **GUI Synchronization:** Automatic notification of value changes (via MIDI or restore) to parent views to update the display.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Event.h"` | `DAD_FORGE/GUI` | Event management and notifications |
| `"Serialize.h"` | `DAD_FORGE/Core` | Serialization interface for persistence |
| `"cParameter.h"` | `DAD_FORGE/DSP` | Base class for DSP parameters |
| `"HardwareAndCo.h"` | `DAD_FORGE/Hardware` | Access to global variables, system constants (SAMPLING_RATE, etc.) |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `void Subscribe_AllSerializeEvents(...)` | `Global GUI Event Manager` | Registers the parameter for save/restore events |
| `void Subscribe_Update(...)` | `Global GUI Event Manager` | Subscribes the parameter to interface updates |
| `void Subscribe_RT_Process(...)` | `Global GUI Event Manager` | Subscribes the parameter to the real-time (audio) processing cycle |
| `void Subscribe_FastUpdate(...)` | `Global GUI Event Manager` | Subscribes the parameter to the fast update (GUI) cycle |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__GUI_EventManager` | `EventManager` | `Global GUI Context` | Event subscription manager |
| `SAMPLING_RATE` | `uint32_t` | `Global/Hardware` | Sampling rate for slope calculation |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(uint32_t SerializeID, float InitValue, float Min, float Max, float RapidIncrement, float SlowIncrement, DadDSP::CallbackType Callback, uint32_t CallbackUserData, float SlopeTime, uint8_t Control, bool RTProcess)` |
| **Description** | Initializes the parameter with its limits, increments, and callback functions. Also configures interpolation (slope) and subscription to GUI/RT events. |
| **Parameter(s)** | |
| `SerializeID` | Unique identifier for serialization. |
| `InitValue` | Initial parameter value. |
| `Min` / `Max` | Value range limits. |
| `RapidIncrement` | Fast increment step size. Used for fast adjustments. |
| `SlowIncrement` | Slow increment step size. Used for fine adjustments. |
| `Callback` | Pointer to a callback function called when the value changes. Signature: `void(*)(cParameter*, uint32_t)`. Can be nullptr to disable. |
| `CallbackUserData` | User data passed to the callback. |
| `Slope` | Smoothing factor. Determines the speed of transition toward the target value. Gives the value in seconds to go from Min to Max value. |
| `Control` | MIDI control number (CC number, 0-127). Value 0xFF disables MIDI control. The MIDI callback will be registered if a valid value is provided. |
| `RTProcess` | If `true`, the parameter is processed in the audio thread. |
| **Return** | `void` |

### **Save**

| Element | Details |
|:---|:---|
| **Method** | `void Save(DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Serializes the parameter's target value to persistent storage. |
| **Parameter(s)** | |
| `pSerializer` | Pointer to the serialization object. |
| **Return** | `void` |

### **Restore**

| Element | Details |
|:---|:---|
| **Method** | `void Restore(DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Restores the value from storage and applies it to the parameter. |
| **Parameter(s)** | |
| `pSerializer` | Pointer to the serialization object. |
| **Return** | `void` |

### **isDirty**

| Element | Details |
|:---|:---|
| **Method** | `bool isDirty()` |
| **Description** | Checks if the parameter has been modified since the last restore. |
| **Return** | `bool` (True if modified) |

### **setDrawInfoView** / **resetDrawInfoView**

| Element | Details |
|:---|:---|
| **Method** | `void setDrawInfoView()` / `void resetDrawInfoView()` |
| **Description** | Enables or disables the flag that notifies the parent view of a value change. |
| **Return** | `void` |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Called in the fast loop: Executes parameter processing including smoothing and callback invocation on value change. |
| **Return** | `void` |

### **on_GUI_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_RT_Process()` |
| **Description** | Called in the audio thread: Executes parameter processing including smoothing and callback invocation on value change. |
| **Return** | `void` |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_Update()` |
| **Description** | Detects value changes (e.g., via MIDI) and notifies the parent view to update the graphic display. |
| **Return** | `void` |

### **setParentView**

| Element | Details |
|:---|:---|
| **Method** | `void setParentView(cParameterView * pParentView)` |
| **Description** | Sets the parent graphic view that should be notified of value changes. |
| **Parameter(s)** | |
| `pParentView` | Pointer to the parent view. |
| **Return** | `void` |

---

## 📦 Data Members (Variables)

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pParentView` | `cParameterView*` | Pointer to the parent view for GUI notifications. |
| `m_MemUIParameterValue` | `float` | Stores the last parameter value.<BR>*Used to detect value changes.* |
| `m_DrawInfoView` | `bool` | Flag indicating whether the view should be notified of the change. |
| `m_IsRTProcess` | `bool` | Indicates whether the parameter is processed in the audio thread. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.