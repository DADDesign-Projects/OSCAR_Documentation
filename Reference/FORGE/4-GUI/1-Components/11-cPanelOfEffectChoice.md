---
title: cPanelOfEffectChoice
parent: GUI Components
layout: default
nav_order: 12
---

# DadGUI::cPanelOfEffectChoice

**Namespace :** DadGUI::cPanelOfEffectChoice  
**Files :** cPanelOfEffectChoice.h / cPanelOfEffectChoice.cpp  
**Directory :** DAD_FORGE/GUI/Components/  
**Inheritance**: `cPanelOfParameterView`  
**Description :** User interface panel for effect selection within a multi-effect framework.

---

## 📋 Class Description
The `cPanelOfEffectChoice` class is a user interface component that lists and selects effects. It uses a discrete parameter view to display options and allows triggering an effect change via a callback when the user presses the encoder button.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"iUIComponent.h"` | `DAD_FORGE/GUI/Components` | Base interface for UI components |
| `"cUIParameter.h"` | `DAD_FORGE/GUI/Components` | UI parameter management |
| `"ParameterViews.h"` | `DAD_FORGE/GUI/Components` | Parameter views (Discrete view) |
| `"cPanelOfParameters.h"` | `DAD_FORGE/GUI/Components` | Base class for parameter panels |
| `"cDisplay.h"` | `DAD_FORGE/GUI/Components` | Display management |

### Global Variables and Driver Objects Used

| Variable / Object | Type | Role |
|:---|:---|:---|
| `__Display` | `Layer*` | Display management |
| `__pActivePalette` | `Palette*` | Retrieves active colors for rendering |
| `__Encoder0` | `Encoder*` | Hardware encoder |

### External Functions Called

| Function | Signature | Source | Usage |
|:---|:---|:---|:---|
| `__Encoder0.getSwitchState()` | `uint8_t getSwitchState()` | `__Encoder0` (Hardware/Global) | Check encoder button state to trigger callback |

---

## 🎯 Associated Enumerations and Structures

### EffectChangeCallback_t
Callback function type used to notify an effect change.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `EffectChangeCallback_t` | `void (*)(DadDSP::cParameter*, uint32_t)` | Callback signature: receives the parameter and a context. |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(uint32_t SerializeID, EffectChangeCallback_t Callback, uint32_t ContextCallback)` |
| **Description** | Initializes the panel, configures the callback system, attaches the display layer, and initializes the choice parameter. |
| **Parameter(s)** | |
| `SerializeID` | `uint32_t` The serialization identifier of the parameter. |
| `Callback` | `EffectChangeCallback_t` The callback function called upon selection. |
| `ContextCallback` | `uint32_t` The context passed to the callback. |

### **addEffect**

| Element | Details |
|:---|:---|
| **Method** | `void addEffect(const char* ShortName, const char* LongName)` |
| **Description** | Adds an option (effect) to the discrete selection list. |
| **Parameter(s)** | |
| `ShortName` | `const char*` Short name of the effect. |
| `LongName` | `const char*` Full name of the effect. |

### **getEffect**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getEffect()` |
| **Description** | Retrieves the index of the currently selected effect. |
| **Return** | `uint8_t` The index of the selected value. |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `void Activate() override` |
| **Description** | Activates the panel, brings it to the foreground (Z-order), and forces initial drawing. |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `void Deactivate() override` |
| **Description** | Deactivates the panel and sends it back to the background. |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `void Update() override` |
| **Description** | Updates the panel state, processes parameter changes, and checks encoder press to trigger the callback. |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `void Redraw() override` |
| **Description** | Forces the panel to redraw if it is active. |

---

## 🔒 Protected / Private Methods

### **Draw**

| Element | Details |
|:---|:---|
| **Method** | `void Draw()` |
| **Description** | Draws the panel. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_isActive` | `bool` | Indicates whether the interface is currently active. |
| `m_pLayer` | `DadGFX::cLayer*` | Pointer to the display layer dedicated to the panel. |
| `m_ParameterChoice` | `cUIParameter` | The parameter object managing the selection value. |
| `m_ParameterChoiceView` | `cParameterDiscretView` | The graphical view for displaying discrete choices. |
| `m_Callback` | `DadDSP::CallbackType` | The stored callback function. |
| `m_ContextCallback` | `uint32_t` | The context associated with the callback. |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.