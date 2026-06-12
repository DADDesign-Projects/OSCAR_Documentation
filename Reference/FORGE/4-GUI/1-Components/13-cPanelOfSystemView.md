---
title: cPanelOfSystemView
parent: GUI Components
layout: default
nav_order: 14
---
# DadGUI::cPanelOfSystemView

**Namespace :** DadGUI::cPanelOfSystemView  
**Files :** cPanelOfSystemView.h / cPanelOfSystemView.cpp  
**Directory :** DAD_FORGE/GUI/Components/  
**Inheritance**: `cPanelOfParameterView`  
**Description :** Panel for managing system parameters including color themes and MIDI channels.  

---

## 📋 Class Description
The `cPanelOfSystemView` class is a user interface (GUI) component responsible for managing global system parameters. It allows selecting the interface color theme and configuring the active MIDI channel.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Include.h"` | `DAD_FORGE/GUI` | Contains basic graphical interface definitions |
| `"cPanelOfParameters.h"` | `DAD_FORGE/GUI` | Defines the base class and parameter types |

### Global Variables

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__pActivePalette` | `PaletteType*` | `extern` | Pointer to the color palette currently used by the interface |
| `__ColorPalette` | `PaletteType[]` | `extern` | Array containing the different available color palettes |
| `__Midi` | `cMidiDriver*` | `extern` | Driver object managing MIDI communications |
| `NB_PALETTE` | `const uint8_t` | `extern` | Total number of available palettes |

### External Functions Called

| Function | Signature | Source | Usage |
|:---|:---|:---|:---|
| `ChangeChannel` | `void ChangeChannel(uint8_t)` | `__Midi` | Called to change the active MIDI channel |

---

## 📚 Public Methods

### **~cPanelOfSystemView**

| Element | Details |
|:---|:---|
| **Method** | `~cPanelOfSystemView()` |
| **Description** | Virtual destructor of the class. |

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(uint32_t SerializeID)` |
| **Description** | Initializes the color theme and MIDI channel parameters, configures their respective views with the available discrete values, and initializes the parent panel view. |
| **Parameter(s)** | |
| `SerializeID` | `uint32_t` Serialization identifier for parameter persistence. |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `void Update()` |
| **Description** | Updates the panel state if it is active, by calling the parent class update logic. |

### **ColorCallback**

| Element | Details |
|:---|:---|
| **Method** | `static void ColorCallback(DadDSP::cParameter* pParameter, uint32_t Context)` |
| **Description** | Static callback triggered when the color theme parameter changes. |
| **Parameter(s)** | |
| `pParameter` | `DadDSP::cParameter*` Pointer to the parameter that changed. |
| `Context` | `uint32_t` Call context (generally the class instance). |

### **MIDICallback**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDICallback(DadDSP::cParameter* pParameter, uint32_t Context)` |
| **Description** | Static callback triggered when the MIDI channel parameter changes. |
| **Parameter(s)** | |
| `pParameter` | `DadDSP::cParameter*` Pointer to the parameter that changed. |
| `Context` | `uint32_t` Call context. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_ColorTheme` | `DadGUI::cUIParameter` | Parameter representing the color theme. |
| `m_MidiChannel` | `DadGUI::cUIParameter` | Parameter representing the MIDI channel. |
| `m_ColorThemeView` | `DadGUI::cParameterDiscretView` | View component for the color theme parameter. |
| `m_MidiChannelView` | `DadGUI::cParameterDiscretView` | View component for the MIDI channel parameter. |

<BR>

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.