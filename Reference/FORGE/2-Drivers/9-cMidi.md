---
title: cMidi
parent: Drivers
layout: default
nav_order: 9
---
# DadDrivers::cMidi

**Namespace:** DadDrivers::cMidi  
**Files:** cMidi.h / cMidi.cpp   
**Directory:** DAD_FORGE/Drivers/_MIDI   
**Description:** MIDI interface manager with callback system and message parsing

---

## 📋 Class Description

The `cMidi` class is a complete MIDI interface manager that operates **via UART (serial)**. It implements a double-buffering DMA reception system for optimal performance, with a ring buffer to store incoming MIDI messages. The class allows registration of callbacks for different types of MIDI messages (Note On/Off, Control Change, Program Change) and provides methods for processing and decomposing MIDI messages according to the MIDI 1.0 standard.

---

## 🎯 Enumerations and Associated Structures

The following structures are defined in the header for the callback system:

### CC_CallbackEntry

Structure storing information for a Control Change callback.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `control` | `uint8_t` | Control Change number (0-127) |
| `userData` | `uint32_t` | Custom user data passed to the callback |
| `callback` | `ControlChangeCallback` | Function to call when this CC is received |

### PC_CallbackEntry

Structure storing information for a Program Change callback.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `userData` | `uint32_t` | Custom user data passed to the callback |
| `callback` | `ProgramChangeCallback` | Function to call when this PC is received |

### Note_CallbackEntry

Structure storing information for a Note On/Off callback.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `userData` | `uint32_t` | Custom user data passed to the callback |
| `callback` | `NoteChangeCallback` | Function to call when Note On/Off is received |

---

## 📚 Public Methods

### **cMidi**

| Element | Details |
|:---|:---|
| **Method** | `cMidi()` |
| **Description** | Default constructor initializing the class with default values. Does not configure the MIDI interface, must be followed by a call to `Initialize()`. |

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(UART_HandleTypeDef* phuart, uint8_t Channel)` |
| **Description** | Initializes the MIDI interface by configuring the specified UART and starting DMA reception. Configures the double-buffering receive buffer for optimal performance. |
| **Parameter(s)** | |
| `phuart` | Pointer to the UART_HandleTypeDef structure of the UART peripheral used for MIDI communication. Must be a configured and operational UART. |
| `Channel` | MIDI channel number to listen to (0-15). Default value: `MULTI_CHANNEL` (0xFF) which listens to all channels. |
| **Return** | None (void) |

### **ChangeChannel**

| Element | Details |
|:---|:---|
| **Method** | `void ChangeChannel(uint8_t Channel)` |
| **Description** | Dynamically changes the MIDI channel on which the class listens for messages. Useful for changing channels during runtime without reinitializing. |
| **Parameter(s)** | |
| `Channel` | New MIDI channel number (0-15) to use for message filtering. |
| **Return** | None (void) |

### **ProcessBuffer**

| Element | Details |
|:---|:---|
| **Method** | `void ProcessBuffer()` |
| **Description** | Processes all available bytes in the MIDI buffer by decoding messages and triggering appropriate callbacks. Must be called regularly from the main loop or a timer to ensure continuous processing of incoming messages. |

### **addControlChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addControlChangeCallback(uint8_t control, uint32_t userData, ControlChangeCallback pCallback)` |
| **Description** | Registers a callback for a specific Control Change message. The callback will be called when the CC number matches the specified one on any channel (depending on channel configuration). |
| **Parameter(s)** | |
| `control` | Control Change number (0-127) for which to register the callback. Ex: 1 for modulation, 64 for volume. |
| `userData` | Custom user value (32 bits) that will be passed to the callback when called. Allows passing contextual information to the processing function. |
| `pCallback` | Pointer to a function of type `ControlChangeCallback` that will be called when the corresponding CC message is received. Required signature: `void (*)(uint8_t control, uint8_t value, uint32_t userData)` |
| **Return** | None (void) |

### **removeControlChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeControlChangeCallback(ControlChangeCallback pCallback)` |
| **Description** | Removes a previously registered Control Change callback. Allows freeing memory and avoiding calls to obsolete callbacks. |
| **Parameter(s)** | |
| `pCallback` | Pointer to the specific callback function to remove from the registration vector. |
| **Return** | None (void) |

### **addProgramChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addProgramChangeCallback(uint32_t userData, ProgramChangeCallback pCallback)` |
| **Description** | Registers a callback for all Program Change messages. The callback will be called for each program change received on the configured channel. |
| **Parameter(s)** | |
| `userData` | Custom user value (32 bits) passed to the callback when called. |
| `pCallback` | Pointer to a function of type `ProgramChangeCallback`. Required signature: `void (*)(uint8_t program, uint32_t userData)` |
| **Return** | None (void) |

### **removeProgramChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeProgramChangeCallback(ProgramChangeCallback pCallback)` |
| **Description** | Removes a previously registered Program Change callback. |
| **Parameter(s)** | |
| `pCallback` | Pointer to the specific callback function to remove from the registration vector. |
| **Return** | None (void) |

### **addNoteChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void addNoteChangeCallback(uint32_t userData, NoteChangeCallback pCallback)` |
| **Description** | Registers a callback for all Note On/Off messages. The callback will be called for each note played or stopped on the configured channel with state indication (On=1, Off=0). |
| **Parameter(s)** | |
| `userData` | Custom user value (32 bits) passed to the callback when called. |
| `pCallback` | Pointer to a function of type `NoteChangeCallback`. Required signature: `void (*)(uint8_t OnOff, uint8_t note, uint8_t velocity, uint32_t userData)` |
| **Return** | None (void) |

### **removeNoteChangeCallback**

| Element | Details |
|:---|:---|
| **Method** | `void removeNoteChangeCallback(NoteChangeCallback pCallback)` |
| **Description** | Removes a previously registered Note On/Off callback. |
| **Parameter(s)** | |
| `pCallback` | Pointer to the specific callback function to remove from the registration vector. |
| **Return** | None (void) |

### **OnNoteOn**

| Element | Details |
|:---|:---|
| **Method** | `void OnNoteOn(uint8_t channel, uint8_t note, uint8_t velocity) const` |
| **Description** | Internal processing method called for Note On messages. Triggers all registered Note callbacks with "On" state (1). Used by `parseMessage()` for MIDI message routing. |
| **Parameter(s)** | |
| `channel` | Source MIDI channel number (0-15) of the message. |
| `note` | MIDI note number (0-127). Ex: 60 = Middle C, 69 = A4. |
| `velocity` | Note attack velocity (0-127). Indicates how hard the key was pressed. |
| **Return** | None (void) |

### **OnNoteOff**

| Element | Details |
|:---|:---|
| **Method** | `void OnNoteOff(uint8_t channel, uint8_t note, uint8_t velocity) const` |
| **Description** | Internal processing method called for Note Off messages. Triggers all registered Note callbacks with "Off" state (0). Used by `parseMessage()` and for the velocity=0 case in Note On. |
| **Parameter(s)** | |
| `channel` | Source MIDI channel number (0-15) of the message. |
| `note` | MIDI note number (0-127). |
| `velocity` | Note release velocity (0-127). Indicates how hard the key was released. |
| **Return** | None (void) |

### **OnControlChange**

| Element | Details |
|:---|:---|
| **Method** | `void OnControlChange(uint8_t channel, uint8_t control, uint8_t value) const` |
| **Description** | Internal processing method called for Control Change messages. Triggers all registered CC callbacks that match the specified control number. Used by `parseMessage()` for routing. |
| **Parameter(s)** | |
| `channel` | Source MIDI channel number (0-15) of the message. |
| `control` | Control Change number (0-127). Ex: 7 = Volume, 10 = Pan, 64 = Modulation. |
| `value` | Control value (0-127). Indicates the position or intensity of the controlled parameter. |
| **Return** | None (void) |

### **OnProgramChange**

| Element | Details |
|:---|:---|
| **Method** | `void OnProgramChange(uint8_t channel, uint8_t program) const` |
| **Description** | Internal processing method called for Program Change messages. Triggers all registered PC callbacks with the specified program number. Used by `parseMessage()` for routing. |
| **Parameter(s)** | |
| `channel` | Source MIDI channel number (0-15) of the message. |
| `program` | Program number (0-127). Indicates the selected instrument or patch. |
| **Return** | None (void) |

### **getDataLength**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t getDataLength(uint8_t status) const` |
| **Description** | Determines the number of expected data bytes for a given MIDI status byte according to the MIDI 1.0 standard. Returns 1 for single-byte messages (Program Change, Channel Pressure) and 2 for others. |
| **Parameter(s)** | |
| `status` | MIDI status byte (MSB with bit 7 set to 1). Ex: 0x90 = Note On, 0xB0 = Control Change. |
| **Return** | Number of expected data bytes (1 or 2) |

### **parseMessage**

| Element | Details |
|:---|:---|
| **Method** | `void parseMessage(uint8_t status, uint8_t* data) const` |
| **Description** | Analyzes and routes a complete MIDI message to the appropriate handler according to its type. Extracts the channel number and message type from the status byte, then calls the corresponding method (OnNoteOn, OnNoteOff, OnControlChange, or OnProgramChange). |
| **Parameter(s)** | |
| `status` | MIDI status byte indicating the message type and channel. |
| `data` | Pointer to an array containing the message data bytes (1 or 2 bytes depending on type). |
| **Return** | None (void) |

---

## 🔒 Protected/Private Methods

No protected or private methods. All internal processing methods are implemented as `const` to allow their use on read-only objects.

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected/Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_phuart` | `UART_HandleTypeDef*` | Pointer to the HAL UART structure used for physical MIDI communication. |
| `m_Channel` | `uint8_t` | Current configured MIDI channel number (0-15 or MULTI_CHANNEL=0xFF). Used to filter incoming messages. |
| `m_status` | `uint8_t` | Temporary MIDI status byte storing the last received status byte during current parsing. |
| `m_data[2]` | `uint8_t[2]` | Buffer array for storing the data bytes of the MIDI message currently being processed. Maximum size of 2 bytes (sufficient for all standard MIDI messages). |
| `m_dataIndex` | `uint8_t` | Counter indicating the number of data bytes received for the current message. Used to verify message completeness before routing. |
| `m_ccCallbacks` | `std::vector<CC_CallbackEntry>` | Dynamic vector containing all registered Control Change callbacks. Each entry associates a CC number with its callback function and user data. |
| `m_pcCallbacks` | `std::vector<PC_CallbackEntry>` | Dynamic vector containing all registered Program Change callbacks. |
| `m_noteCallbacks` | `std::vector<Note_CallbackEntry>` | Dynamic vector containing all registered Note On/Off callbacks. |

---

## 💡 Usage Example

```cpp
#include "cMidi.h"

// User callback definitions
void onVolumeChange(uint8_t control, uint8_t value, uint32_t userData) {
    // Handle volume change (CC#7)
    if (control == 7) {
        float newVolume = value / 127.0f;
        // Apply new volume to the synthesizer
        Synth_SetVolume(newVolume);
    }
}

void onNoteOnHandler(uint8_t OnOff, uint8_t note, uint8_t velocity, uint32_t userData) {
    if (OnOff == 1) {
        // Note on - play sound
        PlayNote(note, velocity);
    } else {
        // Note off - stop sound
        StopNote(note);
    }
}

int main(void) {
    // System initialization
    HAL_Init();
    SystemClock_Config();
    
    // MIDI UART configuration (e.g., USART2 at 31250 baud)
    UART_HandleTypeDef huartMidi;
    MX_USART2_UART_Init(&huartMidi);
    
    // MIDI manager instantiation
    DadDrivers::cMidi midiInterface;
    
    // Interface initialization with channel 1 (default MULTI_CHANNEL)
    midiInterface.Initialize(&huartMidi, 1);
    
    // User callback registration
    midiInterface.addControlChangeCallback(7, 0, onVolumeChange);  // CC#7 = Volume
    midiInterface.addControlChangeCallback(10, 0, nullptr);       // CC#10 = Pan (to be implemented)
    midiInterface.addNoteChangeCallback(0, onNoteOnHandler);      // Notes On/Off
    
    // Main loop - continuous MIDI message processing
    while (1) {
        // Process other system tasks...
        
        // IMPORTANT: Process the MIDI buffer to decode messages
        midiInterface.ProcessBuffer();
        
        // Other processing...
        
        HAL_Delay(10);  // Avoid CPU overconsumption
    }
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.