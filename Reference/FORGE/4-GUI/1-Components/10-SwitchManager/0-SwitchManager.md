---
title: SwitchManager
parent: GUI Components
layout: default
nav_order: 11
---
# SwitchManager
**Namespace :** DadGUI  
**Files:** SwitchManager.h / SwitchManager.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Class :** DadGUI::cSwitchOnOff  
**Class :** DadGUI::cTapTempoMemChange  
**Description:** This module manages switches (footswitches) for effect control (On/Off/Bypass), Tap-Tempo management, and foot-controlled memory preset selection.

---

## 📋 Module Description
The `SwitchManager` module provides utility classes to link physical inputs (footswitches) to logical actions in the user interface and the DSP engine. It manages short press (Toggle On/Off), long press (Bypass), and rhythm detection (Tap-Tempo) interactions for parameter updates or preset/memory changes.

---

## 🔗 External Dependencies
### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"cParameter.h"` | `DAD_FORGE/GUI/Components` | DSP parameter management |
| `"iGUI_EventListener.h"` | `DAD_FORGE/GUI/Components` | Interface for GUI event listeners |

### Global Variables and Driver Objects Used

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__GUI_EventManager` | `GUI_EventManager` | `Global` | Manages subscription to rapid updates |
| `__OnOffCmd` | `eOnOff` | `Global` | Global command for On/Off/Bypass state |
| `__MemoryManager` | `MemoryManager` | `Global` | Manages presets in flash memory |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `getPressCount()` | `DadDrivers::cSwitch` | Retrieves the number of presses |
| `getState()` | `DadDrivers::cSwitch` | Retrieves press state and duration |
| `getPeriodUpdateCount()` | `DadDrivers::cSwitch` | Detects rhythm changes |
| `IncrementSlot()` | `__MemoryManager` | Increments the memory slot |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.