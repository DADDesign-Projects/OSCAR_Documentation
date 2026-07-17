---
title: cMidi
parent: _MIDI
layout: default
nav_order: 1
---
# DadDrivers::cMidi

**Namespace:** DadDrivers::cMidi  
**Files:** cMidi.h / cMidi.cpp  
**Directory:** DAD_FORGE/Drivers/_MIDI/  
**Inheritance:** DadGUI::iGUI_EventListener  
**Description:** MIDI event parsing and management (via UART and USB) with support for custom callbacks.

## 📋 Class Description
The `cMidi` class acts as a MIDI message parser. It receives bytes via the UART interface (through DMA) or via USB events, analyzes them to identify the message type (Note On, CC, Program Change, etc.) and distributes them to the callback functions registered by the user. It is integrated into the GUI event system for fast update processing.

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|
| `"GUI_Event.h"` | `DadGUI` | Interface for subscribing to GUI events |
| `"MainGUI.h"` | `DadGUI` | Access to the global event manager |
| `<vector>` | `STL` | Callback list management |

### **Global Variables**

| Variable / Object | Type | Source | Role |  
|:---|:---|:---|:---|  
| `__RxData` | `uint8_t[2]` | `extern` (NO_CACHE_RAM) | UART reception buffer (double buffering) |  
| `__MidiBuffer` | `uint8_t[128]` | `global` | Ring buffer storing MIDI bytes received via UART |  
| `__MidiFifo` | `cMidiFifo` | `global` | FIFO for MIDI events originating from USB |  
| `DadGUI::__GUI_EventManager` | `GUI_EventManager` | `Singleton DadGUI` | GUI event manager |  

## Callback Functions

| Function | Description |
|:---|:---|
| HAL_UART_RxCpltCallback | Called when UART DMA has filled the second half of __RxData |
| HAL_UART_RxHalfCpltCallback | Called when UART DMA has filled the first half of __RxData |
| UsbMidiCallback | Callback function for handling incoming MIDI events originating from the USB bus. |

## 🎯 Associated Enumerations and Structures

### **stMidiEvent_t**
Structure representing a complete MIDI event received via the USB bus.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `code` | `uint8_t` | Event type (Code Index Number) |
| `channel` | `uint8_t` | MIDI channel (0-15) |
| `data1` | `uint8_t` | First data byte (e.g., Note or CC) |
| `data2` | `uint8_t` | Second data byte (e.g., Velocity) |

### **CC_CallbackEntry**
Storage for Control Change callbacks.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `control` | `uint8_t` | CC control number (0-127) |
| `userData` | `uint32_t` | User data passed to the callback |
| `callback` | `ControlChangeCallback` | Pointer to the callback function |

### **PC_CallbackEntry**
Storage for Program Change callbacks.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `userData` | `uint32_t` | User data passed to the callback |
| `callback` | `ProgramChangeCallback` | Pointer to the callback function |

### **Note_CallbackEntry**
Storage for Note message callbacks (On/Off).

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `userData` | `uint32_t` | User data passed to the callback |
| `callback` | `NoteChangeCallback` | Pointer to the callback function |

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(UART_HandleTypeDef* phuart, uint8_t Channel = MULTI_CHANNEL)` |
| **Description** | Initializes the MIDI interface, configures the listening channel, and enables DMA reception. |
| **Parameter(s)** | |
| `phuart` | `UART_HandleTypeDef*` : Handle of the UART used for communication. |
| `Channel` | `uint8_t` : MIDI channel to listen to (default: all channels). |

### **ChangeChannel**

| Element | Details |
|:---|:---|
| **Method** | `void ChangeChannel(uint8_t Channel)` |
| **Description** | Changes the MIDI channel on which the class should listen. |
| **Parameter(s)** | |
| `Channel` | `uint8_t` : The new MIDI channel. |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_FastUpdate()` |
| **Description** | Processes pending bytes in the UART buffer and pending events in the USB FIFO. |

### **addControlChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addControlChangeCallback(uint8_t control, uint32_t userData, ControlChangeCallback pCallback)` |
| **Description** | Registers a callback function for a specific CC control number. |
| **Parameter(s)** | |
| `control` | `uint8_t` : CC number. |
| `userData` | `uint32_t` : User data. |
| `pCallback` | `ControlChangeCallback` : Callback function. |

### **removeControlChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeControlChangeCallback(ControlChangeCallback pCallback)` |
| **Description** | Removes a previously registered Control Change callback. |
| **Parameter(s)** | |
| `pCallback` | `ControlChangeCallback` : The callback function to remove. |

### **addProgramChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addProgramChangeCallback(uint32_t userData, ProgramChangeCallback pCallback)` |
| **Description** | Registers a callback function for program changes. |
| **Parameter(s)** | |
| `userData` | `uint32_t` : User data. |
| `pCallback` | `ProgramChangeCallback` : Callback function. |

### **removeProgramChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeProgramChangeCallback(ProgramChangeCallback pCallback)` |
| **Description** | Removes a Program Change callback. |
| **Parameter(s)** | |
| `pCallback` | `ProgramChangeCallback` : The callback function to remove. |

### **addNoteChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addNoteChangeCallback(uint32_t userData, NoteChangeCallback pCallback)` |
| **Description** | Registers a callback function for note events (On/Off). |
| **Parameter(s)** | |
| `userData` | `uint32_t` : User data. |
| `pCallback` | `NoteChangeCallback` : Callback function. |

### **removeNoteChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeNoteChangeCallback(NoteChangeCallback pCallback)` |
| **Description** | Removes a note callback. |
| **Parameter(s)** | |
| `pCallback` | `NoteChangeCallback` : The callback function to remove. |

### **OnNoteOn**

| Element | Details |
|:---|:---|
| **Method** | `void OnNoteOn(uint8_t channel, uint8_t note, uint8_t velocity) const` |
| **Description** | Processes a Note On event and triggers the associated callbacks. |
| **Parameter(s)** | |
| `channel` | `uint8_t` : Note channel. |
| `note` | `uint8_t` : Note number. |
| `velocity` | `uint8_t` : Note velocity. |
| **Return** | `void` |

### **OnNoteOff**

| Element | Details |
|:---|:---|
| **Method** | `void OnNoteOff(uint8_t channel, uint8_t note, uint8_t velocity) const` |
| **Description** | Processes a Note Off event and triggers the associated callbacks. |
| **Parameter(s)** | |
| `channel` | `uint8_t` : Note channel. |
| `note` | `uint8_t` : Note number. |
| `velocity` | `uint8_t` : Note velocity. |
| **Return** | `void` |

### **OnControlChange**

| Element | Details |
|:---|:---|
| **Method** | `void OnControlChange(uint8_t channel, uint8_t control, uint8_t value) const` |
| **Description** | Processes a Control Change event and triggers the associated callbacks. |
| **Parameter(s)** | |
| `channel` | `uint8_t` : CC channel. |
| `control` | `uint8_t` : CC number. |
| `value` | `uint8_t` : CC value. |
| **Return** | `void` |

### **OnProgramChange**

| Element | Details |
|:---|:---|
| **Method** | `void OnProgramChange(uint8_t channel, uint8_t program) const` |
| **Description** | Processes a program change and triggers the associated callbacks. |
| **Parameter(s)** | |
| `channel` | `uint8_t` : PC channel. |
| `program` | `uint8_t` : Program number. |
| **Return** | `void` |

### **getDataLength**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getDataLength(uint8_t status) const` |
| **Description** | Determines the number of expected data bytes for a given status byte. |
| **Parameter(s)** | |
| `status` | `uint8_t` : The MIDI status byte. |
| **Return** | `uint8_t` : Number of bytes (1 or 2). |

### **parseMessage**

| Element | Details |
|:---|:---|
| **Method** | `void parseMessage(uint8_t status, uint8_t* data) const` |
| **Description** | Analyzes the status byte and data to dispatch to the appropriate handler. |
| **Parameter(s)** | |
| `status` | `uint8_t` : The status byte. |
| `data` | `uint8_t*` : Array containing the message data. |


## 🔒 Protected / Private Methods

### Data

#### Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_phuart` | `UART_HandleTypeDef*` | Handle to the UART interface. |
| `m_Channel` | `uint8_t` | Current MIDI channel (0-15 or MULTI_CHANNEL). |
| `m_status` | `uint8_t` | Last received status byte. |
| `m_data` | `uint8_t[2]` | Storage buffer for the current message data. |
| `m_dataIndex` | `uint8_t` | Current data byte index. |
| `m_ccCallbacks` | `std::vector<CC_CallbackEntry>` | List of Control Change callbacks. |
| `m_pcCallbacks` | `std::vector<PC_CallbackEntry>` | List of Program Change callbacks. |
| `m_noteCallbacks` | `std::vector<Note_CallbackEntry>` | List of Note On/Off callbacks. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.