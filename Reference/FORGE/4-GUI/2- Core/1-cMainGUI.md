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
**Inheritance:** N/A  
**Description:** Main graphical user interface (GUI) controller managing components, event handling, fonts, and global system operations.

## 📋 Class Description
The `cMainGUI` class acts as the conductor of the user interface. It is responsible for initializing the graphics system (loading fonts from flash memory, initializing themes), managing the lifecycle of components (main component and background component), and broadcasting events (parameter changes, restore operations). It also contains the main loop (`MainLoop`) which orchestrates graphic updates and performance monitoring.

## 🏗️ Display Architecture: Main vs Background Components
The graphical interface is organized into superimposed layers that separate user interaction from system information display. This distinction is ensured by the cMainGUI controller through two types of components, each responsible for a specific screen area.

![](../Interface2.png)

### **1. The Main Component**
The Main Component manages the Menu Area (navigation tabs at the top) and the Panel Area (central parameters area).
In the OSCAR system, activeMainComponent() is used to activate a Menu. This Menu then handles the display logic: depending on the tab selected by the user, it instantiates and activates the appropriate UI component in the Panel Area (parameter adjustment view with encoders and numeric values, preset selection view, configuration menu, etc.).
The Main Component layer therefore has two levels:

cMainGUI manages which Menu is active.
The active Menu manages which UI component is displayed in the Panel Area.

When activeMainComponent() is called, the old component is deactivated (Deactivate()), the new one is activated (Activate()) and immediately takes control of the display.

### **2. The Background Component**
The Background Component is dedicated to the Info Area (the bar at the bottom of the screen). Its role is to permanently display critical status information, regardless of the ongoing interaction in the main layer.
It displays data that must remain visible under all circumstances: the operating mode (BYPASS, ON, OFF), the number and name of the active preset (Memory).

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"cPaletteBuilder.h"` | `DAD_FORGE/GUI/Themes` | Color palette management |
| `"cThemesManager.h"` | `DAD_FORGE/GUI/Themes` | Theme management |
| `"cMemoryManager.h"` | `DAD_FORGE/Core` | System memory management |
| `"cDisplay.h"` | `DadGFX` | Display driver |
| `"cFlasherStorage.h"` | `DadPersistentStorage` | Flash memory access |
| `"iUIComponent.h"` | `DAD_FORGE/GUI/Core` | Base UI component interface |

### **Global Variables**

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__ThemesManager` | `cThemesManager` | `extern` | Theme manager |
| `__GUI_EventManager` | `GUI_EventManager` | `extern` | GUI event manager |
| `__MemoryManager` | `cMemoryManager` | `extern` | Global memory manager |
| `__Display` | `DadGFX::cDisplay` | `extern` | Main display driver |
| `__FlasherStorage` | `cFlasherStorage` | `extern` | Flash storage access |

### **External Functions Called**

| Function | Source | Usage |
|:---|:---|:---|
| `TogglePIN(LED)` | `GPIO.h` | Toggles LED state for activity indication |

## 📚 Public Methods

### **cMainGUI**

| Element | Details |
|:---|:---|
| **Method** | `cMainGUI()` |
| **Description** | Default constructor. |

### **~cMainGUI**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~cMainGUI()` |
| **Description** | Virtual destructor. |

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize()` |
| **Description** | Prepares the GUI system by loading fonts from flash memory, initializes themes, and resets event managers. |

### **Start**

| Element | Details |
|:---|:---|
| **Method** | `void Start()` |
| **Description** | Initializes the memory management system. |

### **activeMainComponent**

| Element | Details |
|:---|:---|
| **Method** | `void activeMainComponent(iUIComponent* pMainComponent)` |
| **Description** | Activates a new main component and deactivates the old one if it existed. |
| **Parameter(s)** | |
| `pMainComponent` | `iUIComponent*`: Pointer to the new main component. |

### **activeBackComponent**

| Element | Details |
|:---|:---|
| **Method** | `void activeBackComponent(iUIComponent* pBackComponent)` |
| **Description** | Activates a new background component and deactivates the old one if it existed. |
| **Parameter(s)** | |
| `pBackComponent` | `iUIComponent*`: Pointer to the new background component. |

### **RegisterParameterListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterParameterListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Registers a callback to be notified when a parameter changes. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t`: Callback function. |
| `ListenerContext` | `uint32_t`: User context. |

### **NotifyParamChange**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyParamChange(cParameterView* pParameter)` |
| **Description** | Broadcasts a parameter change to all registered listeners. |
| **Parameter(s)** | |
| `pParameter` | `cParameterView*`: Pointer to the modified parameter. |

### **RegisterStartRestoreListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterStartRestoreListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Registers a callback to be notified at the start of a restore operation. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t`: Callback function. |
| `ListenerContext` | `uint32_t`: User context. |

### **NotifyStartRestore**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyStartRestore(uint32_t Slot)` |
| **Description** | Notifies the start of a restore for a specific slot. |
| **Parameter(s)** | |
| `Slot` | `uint32_t`: Backup slot index. |

### **RegisterEndRestoreListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterEndRestoreListener(DadUtilities::IteratorCallback_t Callback, uint32_t ListenerContext)` |
| **Description** | Registers a callback to be notified at the end of a restore operation. |
| **Parameter(s)** | |
| `Callback` | `DadUtilities::IteratorCallback_t`: Callback function. |
| `ListenerContext` | `uint32_t`: User context. |

### **NotifyEndRestore**

| Element | Details |
|:---|:---|
| **Method** | `void NotifyEndRestore(uint32_t ID)` |
| **Description** | Notifies the end of a restore with the operation ID. |
| **Parameter(s)** | |
| `ID` | `uint32_t`: Identifier of the completed operation. |

### **GetFontXXS** (and other font accessors)

| Element | Details |
|:---|:---|
| **Method** | `inline DadGFX::cFont* GetFontXXS() const` |
| **Description** | Returns a pointer to the "Extra Extra Small" font. |
| **Return** | `DadGFX::cFont*`: Pointer to the requested font. |

### **MainLoop**

| Element | Details |
|:---|:---|
| **Method** | `void MainLoop()` |
| **Description** | Main GUI system loop. Handles fast updates, standard updates, display refresh, and activity indicators (LED). |

### **notifyRTActivity**

| Element | Details |
|:---|:---|
| **Method** | `inline void notifyRTActivity()` |
| **Description** | Increments the real-time activity counter used for LED blinking. |

## 🔒 Protected / Private Methods

### **ThemeChange_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void ThemeChange_CallBack(void* parameter, uint32_t contextValue)` |
| **Description** | Static callback called when a theme changes to force redrawing of active components. |

## 📦 Data Members

### Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pMainComponent` | `iUIComponent*` | Pointer to the active main component. |
| `m_pBackComponent` | `iUIComponent*` | Pointer to the active background component. |
| `m_updateID` | `uint32_t` | Current update family ID. |
| `m_fastUpdateID` | `uint32_t` | Current fast update family ID. |
| `m_ParameterCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for parameter listeners. |
| `m_EndRestoreCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for restore end listeners. |
| `m_StartRestoreCallBackIterator` | `DadUtilities::cCallBackIterator` | Iterator for restore start listeners. |
| `m_SerializeID` | `uint32_t` | Current serialization ID. |
| `m_pFontXXS` ... `m_pFontXXXLB` | `DadGFX::cFont*` | List of pointers to fonts loaded in memory. |
| `m_CtRTActivity` | `uint32_t` | Real-time activity counter. |

**MONITOR**

| Member | Type | Description |
|:---|:---|:---|
| `m_Monitor` | `DadUtilities::cMonitor` | Performance monitor instance. |
| `m_CPULoad` | `float` | Current CPU load (%). |
| `m_EffectTime` | `float` | Average effect execution time (µs). |
| `m_Frequency` | `float` | Average processing frequency (Hz). |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.