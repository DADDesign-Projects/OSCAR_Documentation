---
title: cTapTempoMemChange
parent: SwitchManager
layout: default
nav_order: 2
---

# DadGUI::cTapTempoMemChange
**Namespace :** DadGUI  
**Files:** SwitchManager.h / SwitchManager.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Class :** DadGUI::cTapTempoMemChange  

---
# 📚 Class: DadGUI::cTapTempoMemChange
**Description:** Manages Tap-Tempo detection and memory slot changes via a footswitch.

## 🎯 Associated Enumerations and Structures
### eTempoType
Defines the output type for Tap-Tempo control.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `period` | `eTempoType` | Defines tap-tempo as a duration in milliseconds |
| `frequency` | `eTempoType` | Defines tap-tempo as a frequency in Hertz |
| `none` | `eTempoType` | Disables tap-tempo control |

---

## **Public Methods**

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Init(DadDrivers::cSwitch* pFootSwitch, DadDSP::cParameter* pParameter, uint32_t EffectID, eTempoType TempoType)` |
| **Description** | Initializes the component with the switch, target DSP parameter, effect ID, and tempo type. |
| **Parameter(s)** | |
| `pFootSwitch` | `DadDrivers::cSwitch*` Pointer to the footswitch driver |
| `pParameter` | `DadDSP::cParameter*` DSP parameter to control for tempo |
| `EffectID` | `uint32_t` Effect identifier |
| `TempoType` | `eTempoType` Tempo calculation type (period, frequency, none) |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Handles memory slot increments (long press ≥ 500ms) and tempo parameter updates (based on frequency or period). |

### **setParameter**

| Element | Details |
|:---|:---|
| **Method** | `void setParameter(DadDSP::cParameter* pParameter)` |
| **Description** | Sets the DSP parameter to control for tempo. |
| **Parameter(s)** | |
| `pParameter` | `DadDSP::cParameter*` The target parameter |

### **setTempoType**

| Element | Details |
|:---|:---|
| **Method** | `void setTempoType(eTempoType TempoType)` |
| **Description** | Sets the tempo output type (period or frequency). |
| **Parameter(s)** | |
| `TempoType` | `eTempoType` The desired calculation type |

## **Data**

### Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_OldPressCount` | `uint32_t` | Tracks the previous press count. |
| `m_PeriodUpdateCount` | `uint32_t` | Tempo period update counter. |
| `m_pFootSwitch` | `DadDrivers::cSwitch*` | Pointer to the footswitch driver. |
| `m_TempoType` | `eTempoType` | Current tempo calculation type. |
| `m_pParameter` | `DadDSP::cParameter*` | Pointer to the linked DSP parameter. |

<BR>

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.