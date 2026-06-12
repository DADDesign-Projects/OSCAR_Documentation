---
title: cUIMemory
parent: GUI Components
layout: default
nav_order: 5
---

# DadGUI::cUIMemory

**Namespace:** DadGUI::cUIMemory  
**Files:** cUIMemory.h / cUIMemory.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Inheritance:** `DadGUI::cPanelOfParameterView`, `DadGUI::iUIComponent`  
**Description:** Manages the user interface for preset management (Save, Load, Delete).

---

## 📋 Class Description
The `cUIMemory` class provides an interactive user interface allowing navigation between memory preset slots and selection of an operation (Save, Load, Delete).   
The class reacts to encoder inputs to trigger read, write, and erase operations of presets in memory via `__MemoryManager`.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"main.h"` | `DAD_FORGE/Core` | Global system definitions |
| `"iUIComponent.h"` | `DAD_FORGE/GUI` | Base interface for UI components |
| `"cDisplay.h"` | `DAD_FORGE/GUI` | Display management |
| `"HardwareAndCo.h"` | `DAD_FORGE/Hardware` | Hardware device definitions (Encoders, etc.) |

### External Functions Called

| Function | Signature | Source | Usage |
|:---|:---|:---|:---|
| `__MemoryManager.SaveSlot` | `void SaveSlot(int, uint32_t)` | `MemoryManager` | Executes slot save operation |
| `__MemoryManager.ErraseSlot` | `void ErraseSlot(int)` | `MemoryManager` | Executes slot erase operation |
| `__MemoryManager.RestoreSlot` | `void RestoreSlot(int)` | `MemoryManager` | Executes slot load operation |
| `__MemoryManager.isErasable` | `bool isErasable(int)` | `MemoryManager` | Checks if slot is erasable |
| `__MemoryManager.isLoadable` | `bool isLoadable(int)` | `MemoryManager` | Checks if slot is loadable |
| `__EncoderX.getIncrement` | `int8_t getIncrement()` | `Hardware/Encoder` | Retrieves encoder movement |
| `__Encoder0.getSwitchState` | `uint8_t getSwitchState()` | `Hardware/Encoder` | Retrieves encoder button state |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__MemoryManager` | `MemoryManager` | `Global` | Storage operations manager |
| `__Encoder0` | `Encoder` | `Global` | Confirmation encoder (button) |
| `__Encoder1` | `Encoder` | `Global` | Encoder for changing operation |
| `__Encoder2` | `Encoder` | `Global` | Encoder for changing slot |
| `__Encoder3` | `Encoder` | `Global` | Encoder for Yes/No selection |
| `__Display` | `Display` | `Global` | Graphic display manager |
| `__pActivePalette` | `Palette*` | `Global` | Active color palette |

---

## 🎯 Associated Enumerations and Structures

### eMemState
Defines the memory preset operation.

| Element | Value | Description |
|:---|:---|:---|
| `Save` | `0` | Saves data to the slot |
| `Load` | `1` | Loads data from the slot |
| `Delete` | `2` | Deletes data from the slot |

### eMemChoice
Defines the user confirmation state (Yes/No).

| Element | Value | Description |
|:---|:---|:---|
| `No` | `0` | User selected "No" |
| `Yes` | `1` | User selected "Yes" |
| `disabled` | `N/A` | Option temporarily disabled (e.g., protected slot) |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(uint32_t SerializeID)` |
| **Description** | Initializes the component state, allocates graphic layers, and sets the serialization ID. |
| **Parameter(s)** | |
| `SerializeID` | Identifier used for save/restore operations. |
| **Return** | `void` |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `void Activate()` |
| **Description** | Activates the component, brings layers to the foreground (Z-order), and forces initial rendering. |
| **Return** | `void` |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `void Deactivate()` |
| **Description** | Deactivates the component and sends layers to the background (Z-order 0). |
| **Return** | `void` |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `void Update()` |
| **Description** | Main update loop. Handles encoder inputs, changes states (operation, slot, choice), validates if the action is possible, and executes memory operation if confirmed. |
| **Return** | `void` |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `void Redraw()` |
| **Description** | Forces a complete redraw of the view if the component is active. |
| **Return** | `void` |

### **setSerializeID**

| Element | Details |
|:---|:---|
| **Method** | `void setSerializeID(uint32_t SerializeID)` |
| **Description** | Sets the serialization ID used during memory operations. |
| **Parameter(s)** | |
| `SerializeID` | New serialization identifier. |
| **Return** | `void` |

---

## 🔒 Protected / Private Methods

### **drawStatLayer**

| Element | Details |
|:---|:---|
| **Method** | `void drawStatLayer()` |
| **Description** | Draws permanent visual elements (Yes/No button frames, static texts). |
| **Return** | `void` |

### **drawDynLayer**

| Element | Details |
|:---|:---|
| **Method** | `void drawDynLayer()` |
| **Description** | Draws dynamic elements (operation text, slot number, Yes/No choice highlighting, and help messages). |
| **Return** | `void` |

---

## 📦 Data Members (Variables)

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_isActive` | `bool` | Indicates whether the interface is currently active. |
| `m_MemorySlot` | `int16_t` | Currently selected memory preset slot index. |
| `m_MemChoice` | `eMemChoice` | User selection state (Yes, No, disabled). |
| `m_MemState` | `eMemState` | Selected memory operation (Save, Load, Delete). |
| `m_ActionExec` | `uint8_t` | Flag to prevent multiple executions of the same action on a single press. |
| `m_SerializeID` | `uint32_t` | Serialization ID for operations. |
| `m_pDynMemoryLayer` | `DadGFX::cLayer*` | Pointer to the dynamic rendering layer. |
| `m_pStatMemoryLayer` | `DadGFX::cLayer*` | Pointer to the static rendering layer. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.