---
title: cThemesManager
parent: Themes
layout: default
nav_order: 1
---

# DadGUI::cThemesManager

**Namespace:** DadGUI::cThemesManager  
**Files:** cThemesManager.h / cThemesManager.cpp  
**Directory:** DAD_FORGE/GUI/Themes/  
**Inheritance:** iGUI_EventListener  
**Description:** Manages the active color palette and notifies listeners when theme changes occur.

## 📋 Class Description
The `cThemesManager` class is responsible for managing the visual state (theme) of the user interface. It maintains a reference to the currently used color palette and notifies the various interface components when a theme change is made. It can also handle dynamic palette changes via a `cPaletteBuilder`.

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"@Options.h"` | `DAD_FORGE/` | Project configuration |

### **Global Variables**

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__ColorPalette` | `sColorPalette[]` | `extern` in `cThemesManager.cpp` | Array containing the various available palettes |
| `__GUI_EventManager` | `GUI_EventManager` | `extern` in `cThemesManager.cpp` | GUI event manager for subscribing to updates |

### **External Functions Called**

| Function | Source | Usage |
|:---|:---|:---|
| `__GUI_EventManager.Subscribe_Update(this)` | `GUI_EventManager` | Subscribes the class to GUI update events |

## 📚 Public Methods

### **cThemesManager**

| Element | Details |
|:---|:---|
| **Method** | `cThemesManager()` |
| **Description** | Default constructor. |

### **~cThemesManager**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~cThemesManager()` |
| **Description** | Virtual destructor. |

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize()` |
| **Description** | Initializes the theme manager with the first available palette by default, initializes the palette builder, and subscribes to GUI update events. |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_Update()` |
| **Description** | Updates the theme based on palette changes (if the palette builder is active) and triggers the update processes. |

### **getActivePalette**

| Element | Details |
|:---|:---|
| **Method** | `sColorPalette* getActivePalette()` |
| **Description** | Retrieves a pointer to the currently active color palette. |
| **Return** | `sColorPalette*` : Pointer to the active palette. |

### **operator->**

| Element | Details |
|:---|:---|
| **Method** | `sColorPalette* operator->()` |
| **Description** | Allows direct access to the active palette's members via the arrow operator (e.g., `themeManager->Color`). |
| **Return** | `sColorPalette*` : Pointer to the active palette. |

### **setActivePalette**

| Element | Details |
|:---|:---|
| **Method** | `void setActivePalette(sColorPalette* pColorPalette)` |
| **Description** | Sets the active palette. If the pointer is null, the first palette in the system is used by default. Notifies listeners of the change. |
| **Parameter(s)** | |
| `pColorPalette` | `sColorPalette*` : Pointer to the new palette. |

### **setActivePalette**

| Element | Details |
|:---|:---|
| **Method** | `void setActivePalette(uint8_t PaletteIndex)` |
| **Description** | Sets the active palette using an index. The index is capped to the last available palette if it is out of bounds. Notifies listeners of the change. |
| **Parameter(s)** | |
| `PaletteIndex` | `uint8_t` : Index of the desired palette. |

### **RegisterThemeChangeListener**

| Element | Details |
|:---|:---|
| **Method** | `void RegisterThemeChangeListener(DadUtilities::IteratorCallback_t callback, uint32_t listenerContext, uint8_t priority)` |
| **Description** | Registers a callback to be notified when a theme change occurs. |
| **Parameter(s)** | |
| `callback` | `DadUtilities::IteratorCallback_t` : Callback function. |
| `listenerContext` | `uint32_t` : User context passed to the callback. |
| `priority` | `uint8_t` : Listener priority (default 127). |

### **UnregisterThemeChangeListener**

| Element | Details |
|:---|:---|
| **Method** | `void UnregisterThemeChangeListener(DadUtilities::IteratorCallback_t callback)` |
| **Description** | Unregisters a previously registered theme change listener. |
| **Parameter(s)** | |
| `callback` | `DadUtilities::IteratorCallback_t` : Callback function to unregister. |


## 📦 Data Members

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pActivePalette` | `sColorPalette*` | Pointer to the currently active color palette. |
| `m_PaletteBuilder` | `cPaletteBuilder` | Palette builder for dynamic modifications. |
| `m_ThemeChangeListener` | `DadUtilities::cCallBackIterator` | Callback iterator for theme change notifications. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.