---
title: cPanelOfParameterView
parent: GUI Components
layout: default
nav_order: 13
---

# DadGUI::cPanelOfParameterView

**Namespace :** DadGUI::cPanelOfParameterView
**Files :** cPanelOfParameters.h / cPanelOfParameters.cpp  
**Directory :** DAD_FORGE/GUI/Components/  
**Inheritance**: iUIComponent  
**Description :** Manages a panel composed of three parameter views and their respective rendering layers (static and dynamic).

---

## 📋 Class Description
The `cPanelOfParameterView` class acts as a container for three parameter views (`cParameterView`). It manages the allocation of graphic layers for each parameter, allowing separation between static rendering (background/frame) and dynamic rendering (changing values/information). It orchestrates the activation, update, and redrawing of these components.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"iUIComponent.h"` | `DAD_FORGE/GUI/Components` | Base interface for UI components |
| `"cParameterView.h"` | `DAD_FORGE/GUI/Components` | Base class for parameter views |

### Global Variables Used

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__Display` | `Layer*` | `Global/Macro` | Display manager |
| `__pActivePalette` | `Palette*` | `Global/Macro` | Pointer to the active palette |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Init(cParameterView* pParameter1, cParameterView* pParameter2, cParameterView* pParameter3)` |
| **Description** | Initializes the three parameter view pointers and allocates the associated graphic layers (static and dynamic). |
| **Parameter(s)** | |
| `pParameter1` | `cParameterView*` First parameter to manage. |
| `pParameter2` | `cParameterView*` Second parameter to manage. |
| `pParameter3` | `cParameterView*` Third parameter to manage. |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `void Activate() override` |
| **Description** | Activates the panel, initializes the activity state, and performs the initial rendering of parameters if present. |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `void Deactivate() override` |
| **Description** | Deactivates the panel and stops updates. |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `void Update() override` |
| **Description** | Periodically updates the parameter views (handles dynamic rendering) if the panel is active. |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `void Redraw() override` |
| **Description** | Forces a complete redraw of all layers (static and dynamic) for active parameters. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pParameter1` | `cParameterView*` | Pointer to the first parameter view. |
| `m_pParameter2` | `cParameterView*` | Pointer to the second parameter view. |
| `m_pParameter3` | `cParameterView*` | Pointer to the third parameter view. |
| `m_pParameter1LayerDyn` | `DadGFX::cLayer*` | Dynamic layer for parameter 1. |
| `m_pParameter1LayerStat` | `DadGFX::cLayer*` | Static layer for parameter 1. |
| `m_pParameter2LayerDyn` | `DadGFX::cLayer*` | Dynamic layer for parameter 2. |
| `m_pParameter2LayerStat` | `DadGFX::cLayer*` | Static layer for parameter 2. |
| `m_pParameter3LayerDyn` | `DadGFX::cLayer*` | Dynamic layer for parameter 3. |
| `m_pParameter3LayerStat` | `DadGFX::cLayer*` | Static layer for parameter 3. |
| `m_isActive` | `bool` | Panel activation state. |

<BR>

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.