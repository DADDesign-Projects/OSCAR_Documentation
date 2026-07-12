---
title: cMainGUI
parent: GUI Core
layout: default
nav_order: 1
---
# DadGUI::cMainGUI

**Namespace:** DadGUI::cMainGUI  
**Files:** MainGUI.h / MainGUI.cpp  
**Directory:** DAD_FORGE/GUI/Core/  
**Inheritance:** iGUI_EventListener  
**Description:** Main GUI controller class that manages components, event handling, and system-wide GUI operations.

---

## 📋 Class Description
The `cMainGUI` class acts as the central controller of the graphical user interface. It is responsible for managing the lifecycle of UI components, updating graphical elements, handling color palette changes, and propagating parameter change events. It also manages MIDI commands (byPass, On, Off).


## 🏗️ Display Architecture: Main vs Background Components
The graphical interface is organized into superimposed layers that separate user interaction from system information display. This distinction is ensured by the cMainGUI controller through two types of components, each responsible for a specific screen area.

![](../Interface2.png)

### 1. The Main Component
The Main Component manages the Menu Area (navigation tabs at the top) and the Panel Area (central parameters area).
In the OSCAR system, activeMainComponent() is used to activate a Menu. This Menu then handles the display logic: depending on the tab selected by the user, it instantiates and activates the appropriate UI component in the Panel Area (parameter adjustment view with encoders and numeric values, preset selection view, configuration menu, etc.).
The Main Component layer therefore has two levels:

cMainGUI manages which Menu is active.
The active Menu manages which UI component is displayed in the Panel Area.

When activeMainComponent() is called, the old component is deactivated (Deactivate()), the new one is activated (Activate()) and immediately takes control of the display.

### 2. The Background Component
The Background Component is dedicated to the Info Area (the bar at the bottom of the screen). Its role is to permanently display critical status information, regardless of the ongoing interaction in the main layer.
It displays data that must remain visible under all circumstances: the operating mode (BYPASS, ON, OFF), the number and name of the active preset (Memory).

---

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"Sections.h"` | `DAD_FORGE/GUI/Core/` | Interface section definitions |
| `"iUIComponent.h"` | `DAD_FORGE/GUI/Core/` | Base interface for UI components |
| `"cMemoryManager.h"` | `DAD_FORGE/GUI/Core/` | Memory preset manager |
| `"GUI_Event.h"` | `DAD_FORGE/GUI/Core/` | GUI event management system |
| `"cCallBackIterator.h"` | `DAD_FORGE/GUI/Core/` | Callback list management |
| `"cParameter.h"` | `DAD_FORGE/GUI/Core/` | Device parameter management |
| `"GFX.h"` | `DAD_FORGE/GUI/Core/` | Base graphics engine |
| `"HardwareAndCo.h"` | `DAD_FORGE/GUI/Core/` | Hardware access (MIDI, On/Off, ...) |
| `"cPaletteBuilder.h"` | `DAD_FORGE/GUI/Core/` | Dynamic color palette construction |

### **Global Variables**

| Variable / Object | Type | Role |
|:---|:---|:---|
| `__ColorPalette` | `sColorPalette[8]` | Array of available color palettes |
| `__pActivePalette` | `const sColorPalette*` | Pointer to the currently active palette |
| `__MempActivePalette` | `const sColorPalette*` | Pointer to the previous palette for change detection |
| `__MemoryManager` | `cMemoryManager` | Global instance of the memory manager |
| `__GUI_EventManager` | `cGUI_EventManager` | GUI event manager |
| `__Midi` | `cMIDI` | MIDI control interface |
| `__FlasherStorage` | `cFlasherStorage` | Flash memory access for resources (fonts) |
| `__OnOffCmd` | `eOnOffCmd` | Current system state (On, Off, ByPass) |

### **External Functions Called**

| Function | Signature | Source | Usage |
|:---|:---|:---|:---|
| `GetFilePtr` | `void* GetFilePtr(const char*)` | `cFlasherStorage` | Retrieval of pointers to font files in Flash |
| `addControlChangeCallback` | `void addControlChangeCallback(...)` | `cMIDI` | Registration of callbacks for MIDI CC messages |
| `addProgramChangeCallback` | `void addProgramChangeCallback(...)` | `cMIDI` | Registration of callbacks for MIDI Program Change messages |

---

## 🎯 Associated Enumerations and Structures

### **sColorPalette**
Structure defining a complete set of colors for the interface according to different visual contexts.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `MenuBack` | `DadGFX::sColor` | Menu background color |
| `MenuText` | `DadGFX::sColor` | Menu text color |
| `MenuActiveText` | `DadGFX::sColor` | Active menu item text color |
| `MenuActive` | `DadGFX::sColor` | Active menu item color |
| `MenuArrow` | `DadGFX::sColor` | Menu arrow color |
| `ParameterBack` | `DadGFX::sColor` | Parameter area background color |
| `ParameterName` | `DadGFX::sColor` | Parameter name color |
| `ParameterNum` | `DadGFX::sColor` | Parameter number color |
| `ParameterValue` | `DadGFX::sColor` | Parameter value color |
| `ParameterLines` | `DadGFX::sColor` | Parameter separator lines color |
| `ParameterCursor` | `DadGFX::sColor` | Parameter selection cursor color |
| `ParamInfoBack` | `DadGFX::sColor` | Parameter info background color |
| `ParamInfoName` | `DadGFX::sColor` | Name color in parameter info |
| `ParamInfoValue` | `DadGFX::sColor` | Value color in parameter info |
| `MainInfoBack` | `DadGFX::sColor` | Main info background color |
| `MainInfoMem` | `DadGFX::sColor` | Memory indicator color |
| `MainInfoDirty` | `DadGFX::sColor` | "Dirty" state indicator color |
| `MainInfoState` | `DadGFX::sColor` | System state color |
| `MemViewBack` | `DadGFX::sColor` | Memory view background color |
| `MemViewText` | `DadGFX::sColor` | Memory view text color |
| `MemViewLine` | `DadGFX::sColor` | Memory view lines color |
| `MemViewActive` | `DadGFX::sColor` | Active element color in memory view |
| `VuMeterBack` | `DadGFX::sColor` | VU Meter background color |
| `VuMeterText` | `DadGFX::sColor` | VU Meter text color |
| `VuMeterLine` | `DadGFX::sColor` | VU Meter bar color |
| `VuMeterCursor` | `DadGFX::sColor` | VU Meter cursor color |
| `VuMeterPeak` | `DadGFX::sColor` | Peak indicator color |
| `VuMeterClip` | `DadGFX::sColor` | Clip indicator color |
| `SplatchBack` | `DadGFX::sColor` | Splash screen background color |
| `SplatchText` | `DadGFX::sColor` | Splash screen text color |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize()` |
| **Description** | Prepares the GUI system by initializing fonts (loading from Flash), palettes, and events. |
| **Return** | `void` |

### **Start**

| Element | Details |
|:---|:---|
| **Method** | `void Start()` |
| **Description** | Initializes the memory manager and registers MIDI callbacks for system and preset control. |
| **Return** | `void` |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_Update()` |
| **Description** | Handles periodic updates of UI components (main and background) and detects palette changes to force a redraw. |
| **Return** | `void` |

### **activeMainComponent**

| Element | Details |
|:---|:---|
| **Method** | `void activeMainComponent(iUIComponent* pMainComponent)` |
| **Description** | Activates a new main component by deactivating the old one. |
| **Parameter(s)** | |
| `pMainComponent` | `iUIComponent*` The new main component to activate. |
| **Return** | `void` |

### **activeBackComponent**

| Element | Details |
|:---|:---|
| **Method** | `void activeBackComponent(iUIComponent* pBackComponent)` |
| **Description** | Activates a new background component by deactivating the old one. |
| **Parameter(s)** | |
| `pBackComponent` | `iUIComponent*` The new background component to activate. |
| **Return** | `void` |

### **RegisterParameterListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterParameterListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Adds a callback to be notified when a parameter changes. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t` The callback function to execute. |
| `ListenerContext` | `uint32_t` User context passed to the callback. |
| **Return** | `void` |

### **NotifyParamChange**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyParamChange(cParameterView* pParameter)` |
| **Description** | Notifies all registered listeners that a parameter has been modified. |
| **Parameter(s)** | |
| `pParameter` | `cParameterView*` Pointer to the modified parameter. |
| **Return** | `void` |

### **RegisterStartRestoreListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterStartRestoreListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Adds a listener to be notified at the start of a backup slot restore operation. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t` The callback function. |
| `ListenerContext` | `uint32_t` User context. |
| **Return** | `void` |

### **NotifyStartRestore**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyStartRestore(uint32_t Slot)` |
| **Description** | Notifies the start of a memory slot restore. |
| **Parameter(s)** | |
| `Slot` | `uint32_t` The ID of the slot being restored. |
| **Return** | `void` |

### **RegisterEndRestoreListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterEndRestoreListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Adds a listener to be notified at the end of a slot restore operation. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t` The callback function. |
| `ListenerContext` | `uint32_t` User context. |
| **Return** | `void` |

### **NotifyEndRestore**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyEndRestore(uint32_t ID)` |
| **Description** | Notifies the end of a slot restore operation. |
| **Parameter(s)** | |
| `ID` | `uint32_t` The ID of the slot whose restore is complete. |
| **Return** | `void` |

### **GetFont...**

| Element | Details |
|:---|:---|
| **Method** | `inline DadGFX::cFont* GetFont...() const` |
| **Description** | Returns a pointer to the ... size font. |
| **Return** | `DadGFX::cFont*` Pointer to the font. |

---

## 🔒 Protected / Private Methods

### **MIDI_On_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_On_CallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | MIDI callback triggered by the system ON command. |
| **Parameter(s)** | |
| `control` | `uint8_t` MIDI control code. |
| `value` | `uint8_t` Control value. |
| `userData` | `uint32_t` User data. |
| **Return** | `void` |

### **MIDI_Off_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_Off_CallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | MIDI callback triggered by the system OFF command. |
| **Parameter(s)** | |
| `control` | `uint8_t` MIDI control code. |
| `value` | `uint8_t` Control value. |
| `userData` | `uint32_t` User data. |
| **Return** | `void` |

### **MIDI_ByPass_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_ByPass_CallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | MIDI callback triggered by the system BYPASS command. |
| **Parameter(s)** | |
| `control` | `uint8_t` MIDI control code. |
| `value` | `uint8_t` Control value. |
| `userData` | `uint32_t` User data. |
| **Return** | `void` |

---

## 📦 Data Members

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pMainComponent` | `iUIComponent*` | Currently active main UI component |
| `m_pBackComponent` | `iUIComponent*` | Currently active background UI component |
| `m_updateID` | `uint32_t` | Current update family identifier |
| `m_fastUpdateID` | `uint32_t` | Current fast update family identifier |
| `m_ParameterCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for parameter listeners |
| `m_EndRestoreCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for end restore listeners |
| `m_StartRestoreCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for start restore listeners |
| `m_SerializeID` | `uint32_t` | Current serialization family identifier |
| `m_pFontXXS` | `DadGFX::cFont*` | Pointer to XXS font |
| `m_pFontXXSB` | `DadGFX::cFont*` | Pointer to XXSB font |
| `m_pFontXS` | `DadGFX::cFont*` | Pointer to XS font |
| `m_pFontXSB` | `DadGFX::cFont*` | Pointer to XSB font |
| `m_pFontS` | `DadGFX::cFont*` | Pointer to S font |
| `m_pFontSB` | `DadGFX::cFont*` | Pointer to SB font |
| `m_pFontM` | `DadGFX::cFont*` | Pointer to M font |
| `m_pFontMB` | `DadGFX::cFont*` | Pointer to MB font |
| `m_pFontL` | `DadGFX::cFont*` | Pointer to L font |
| `m_pFontLB` | `DadGFX::cFont*` | Pointer to LB font |
| `m_pFontXL` | `DadGFX::cFont*` | Pointer to XL font |
| `m_pFontXLB` | `DadGFX::cFont*` | Pointer to XLB font |
| `m_pFontXXL` | `DadGFX::cFont*` | Pointer to XXL font |
| `m_pFontXXLB` | `DadGFX::cFont*` | Pointer to XXLB font |
| `m_pFontXXXL` | `DadGFX::cFont*` | Pointer to XXXL font |
| `m_pFontXXXLB` | `DadGFX::cFont*` | Pointer to XXXLB font |

<br>

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.