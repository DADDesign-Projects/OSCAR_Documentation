---
title: cMemoryManager
parent: GUI Components
layout: default
nav_order: 10
---

# DadGUI::cMemoryManager

**Namespace:** DadGUI::cMemoryManager  
**Files:** cMemoryManager.h / cMemoryManager.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Description:** Memory manager for preset slots, supporting MIDI program changes and GUI state persistence.

---

## 📋 Class Description
The `cMemoryManager` class is responsible for managing the lifecycle of presets stored in non-volatile memory (Flash). It allows saving and restoring the user interface state. It can be controlled via the MIDI interface (Program Change, Preset Up/Down). It also ensures data integrity by verifying memory header validity and managing restore processes to notify the graphical interface.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Define.h"` | `DAD_FORGE/GUI` | Global GUI definitions |
| `"cCallBackIterator.h"` | `DAD_FORGE/Core` | Callback iterator management |
| `"ID.h"` | `DAD_FORGE/Core` | Identifier definitions |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `bool Load(...)` | `BlockStorageManager` | Loads data from Flash |
| `bool Save(...)` | `BlockStorageManager` | Saves data to Flash |
| `bool Delete(...)` | `BlockStorageManager` | Deletes a data block |
| `uint32_t getSize(...)` | `BlockStorageManager` | Retrieves a block's size |
| `void sendEvent_SerializeSave(...)` | `Global GUI Event Manager` | Triggers GUI state serialization |
| `void sendEvent_SerializeRestore(...)` | `Global GUI Event Manager` | Triggers GUI state deserialization |
| `void NotifyStartRestore(...)` | `Global GUI Manager` | Notifies the start of a restore process |
| `void NotifyEndRestore(...)` | `Global GUI Manager` | Notifies the end of a restore process |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__BlockStorageManager` | `BlockStorageManager` | `Global Driver` | Physical storage manager (Flash) |
| `__GUI_EventManager` | `EventManager` | `Global GUI Context` | Serialization event manager |
| `__GUI` | `GUI_Manager` | `Global GUI Context` | Global GUI manager |

---

## 🎯 Associated Enumerations and Structures

### **sMemoryHeader**
Structure stored in memory to maintain preset system integrity.

| Element | Type | Description |
|:---|:---|:---|
| `m_ActiveSlot` | `uint8_t` | Index of the currently loaded and active slot. |
| `m_SlotID[MAX_SLOT]` | `uint32_t` | Array of unique identifiers for each slot (0 = empty). |
| `m_SlotSize[MAX_SLOT]` | `uint32_t` | Size of stored data for each slot in bytes. |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init()` |
| **Description** | Loads the memory header. If the header is corrupted, resets all slots. Otherwise, automatically restores the last active slot. |
| **Return** | `void` |

### **MIDI_PresetUp_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_PresetUp_CallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | MIDI callback for "Preset Up" command. Increments the slot index to the next one. |
| **Parameter(s)** | |
| `control` | MIDI control code. |
| `value` | Control value. |
| `userData` | Pointer to the `cMemoryManager` instance. |
| **Return** | `void` |

### **MIDI_PresetDown_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_PresetDown_CallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | MIDI callback for "Preset Down" command. Decrements the slot index to the previous one. |
| **Parameter(s)** | |
| `control` | MIDI control code. |
| `value` | Control value. |
| `userData` | Pointer to the `cMemoryManager` instance. |
| **Return** | `void` |

### **MIDI_ProgramChange_CallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDI_ProgramChange_CallBack(uint8_t program, uint32_t userData)` |
| **Description** | MIDI callback for "Program Change" message. Directly loads the slot corresponding to the program number if valid. |
| **Parameter(s)** | |
| `program` | MIDI program number (slot index). |
| `userData` | Pointer to the `cMemoryManager` instance. |
| **Return** | `void` |

### **RestoreSlot**

| Element | Details |
|:---|:---|
| **Method** | `bool RestoreSlot(uint8_t Slot)` |
| **Description** | Retrieves slot data, deserializes it, and updates the graphical interface. |
| **Parameter(s)** | |
| `Slot` | Index of the slot to restore. |
| **Return** | `bool` (True if restore successful) |

### **SaveSlot**

| Element | Details |
|:---|:---|
| **Method** | `bool SaveSlot(uint8_t Slot, uint32_t SlotID)` |
| **Description** | Serializes the current GUI state and saves it to the specified slot with the provided ID. |
| **Parameter(s)** | |
| `Slot` | Destination slot index. |
| `SlotID` | Unique identifier for the preset. |
| **Return** | `bool` (True if save successful) |

### **EraseSlot**

| Element | Details |
|:---|:---|
| **Method** | `bool ErraseSlot(uint8_t Slot)` |
| **Description** | Deletes data from a slot if it is not the currently active slot. |
| **Parameter(s)** | |
| `Slot` | Index of the slot to erase. |
| **Return** | `bool` (True if erase successful) |

### **isLoadable**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t isLoadable(uint8_t Slot)` |
| **Description** | Checks if a slot contains valid data (non-zero ID). |
| **Parameter(s)** | |
| `Slot` | Slot index. |
| **Return** | `uint8_t` (1 if loadable, 0 otherwise) |

### **isErasable**

| Element | Details |
|:---|:---|
| **Method** | `bool isErasable(uint8_t Slot)` |
| **Description** | Checks if a slot can be erased (must contain data and not be the active slot). |
| **Parameter(s)** | |
| `Slot` | Slot index. |
| **Return** | `bool` (True if erasable) |

### **IncrementSlot**

| Element | Details |
|:---|:---|
| **Method** | `void IncrementSlot(int8_t Increment)` |
| **Description** | Navigates between slots (forward/backward) with wrap-around handling and automatic loading of the next valid slot. |
| **Parameter(s)** | |
| `Increment` | Increment value (+1 or -1). |
| **Return** | `void` |

### **getActiveSlot**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getActiveSlot()` |
| **Description** | Retrieves the index of the currently active slot. |
| **Return** | `uint8_t` |

### **IsInRestoreProcess**

| Element | Details |
|:---|:---|
| **Method** | `bool IsInRestoreProcess()` |
| **Description** | Indicates whether a restore operation is in progress (to block other actions). |
| **Return** | `bool` |

---

## 📦 Data Members (Variables)

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_MemoryHeader` | `sMemoryHeader` | Structure containing slot metadata (ID, Size, Active Slot). |
| `m_RestoreInProcess` | `bool` | Flag indicating whether a restore is in progress. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.