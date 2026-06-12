---
title: cParameterInfoView
parent: GUI Components
layout: default
nav_order: 4
---

# DadGUI::cParameterInfoView

**Namespace:** DadGUI::cParameterInfoView  
**Files:** cParameterInfoView.h / cParameterInfoView.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Inheritance:** `cPanelOfParameterView`, `iGUI_EventListener`   
**Description:** Manages the temporary display of parameter information (name and value) as a popup window superimposed on the InfoView Area.

---

## 📋 Class Description
The `cParameterInfoView` class is responsible for displaying an ephemeral information banner on the InfoView area of the user interface.
![](../Interface2.png)  

When a parameter is modified, the class displays its name and value on a dedicated layer, then automatically hides this layer after a predefined delay (`PARAMETER_INFO_VIEW_DISPLAY_TIME_MS`). It acts as an observer of parameter changes via the `iGUI_EventListener` interface.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Include.h"` | `DAD_FORGE/GUI` | Access to base types, graphic components, constants, and global objects used by the GUI |
| `"GUI_Event.h"` | `DAD_FORGE/GUI` | Event management and listener interfaces |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `void RegisterParameterListener(...)` | `Global GUI Object` | Registers the parameter change callback |
| `void Subscribe_Update(...)` | `Global Event Manager` | Subscribes the object to periodic GUI updates |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__pActivePalette` | `Palette*` | `Global GUI Context` | Access to colors for rendering the chosen theme colors |
| `__GUI` | `GUI_Manager` | `Global GUI Context` | GUI manager, synchronization, ... |
| `__GUI_EventManager` | `EventManager` | `Global GUI Context` | GUI event manager |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init()` |
| **Description** | Initializes the view, allocates the overlay layer, resets the time counter, and subscribes to parameter change events and GUI update events. |
| **Return** | `void` |

### **ShowParamView**

| Element | Details |
|:---|:---|
| **Method** | `void ShowParamView(std::string Name, std::string Value)` |
| **Description** | Displays the parameter name and value on the dedicated layer by centering the text and using the appropriate fonts and colors. |
| **Parameter(s)** | |
| `Name` | The name of the parameter to display. |
| `Value` | The value of the parameter to display. |
| **Return** | `void` |

### **HideParamView**

| Element | Details |
|:---|:---|
| **Method** | `void HideParamView()` |
| **Description** | Hides the information view by sending the overlay layer to the background (Z-order 0). |
| **Return** | `void` |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_Update()` |
| **Description** | Manages the display time countdown. If the time counter is active, it is decremented; when it reaches zero, the view is hidden. |
| **Return** | `void` |

### **ParameterChange**

| Element | Details |
|:---|:---|
| **Method** | `static void ParameterChange(void* pParameter, uint32_t Context)` |
| **Description** | Static callback triggered when a parameter changes. Retrieves the class instance via the context to display the new information. |
| **Parameter(s)** | |
| `pParameter` | Pointer to the parameter object that changed. |
| `Context` | Pointer to the `cParameterInfoView` instance. |
| **Return** | `void` |

---

## 📦 Data Members

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pParamInfoLayer` | `DadGFX::cLayer*` | Pointer to the graphic layer dedicated to displaying information. |
| `m_InfoViewTimeCounter` | `int32_t` | Internal counter in milliseconds to manage the display duration. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.