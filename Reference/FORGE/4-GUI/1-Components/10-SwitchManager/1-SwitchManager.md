---
title: cSwitchOnOff
parent: SwitchManager
layout: default
nav_order: 1
---

# DadGUI::cSwitchOnOff
**Namespace :** DadGUI  
**Files:** SwitchManager.h / SwitchManager.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Inheritance:** `iGUI_EventListener`    
**Class :** DadGUI::cSwitchOnOff  

## 📚 Class: DadGUI::cSwitchOnOff

**Description:** Manages On/Off/Bypass toggles via footswitch interactions.

## **Public Methods**

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Init(DadDrivers::cSwitch* pFootSwitch, uint32_t EffectID)` |
| **Description** | Initializes the component with a reference to the physical switch and the effect ID, then subscribes to GUI updates. |
| **Parameter(s)** | |
| `pFootSwitch` | `DadDrivers::cSwitch*` Pointer to the footswitch driver |
| `EffectID` | `uint32_t` Identifier of the concerned effect |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Detects short presses (Toggle On/Off) and long presses (Bypass) to modify the `__OnOffCmd` command. |

## **Data**

### **Private Variables**

| Member | Type | Description |
|:---|:---|:---|
| `m_OldPressCount` | `uint32_t` | Tracks the previous press count for change detection. |
| `m_LastPressTime` | `uint32_t` | Timestamp of the last press. |
| `m_pFootSwitch` | `DadDrivers::cSwitch*` | Pointer to the footswitch hardware. |

<BR>

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.