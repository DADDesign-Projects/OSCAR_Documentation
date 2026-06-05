---
title: USBD_MIDI
parent: USB‑MIDI
layout: default
nav_order: 2
---

# USBD_MIDI

**Files:** usbd_midi.h / usbd_midi.c   
**Directory:** DAD_FORGE/Drivers/_MIDI   
**Description:** Implementation of the USB MIDI Device class for STM32 with bidirectional bulk endpoints

---

## 📋 Class Description

The `USBD_MIDI` module is a complete implementation of the USB Audio/MIDI Device class according to the USB Audio 1.0 class driver from the STM32 USB Standard Device Library (USBD). It manages bidirectional bulk endpoints for transmitting and receiving MIDI messages via USB, with a complete configuration descriptor including jack descriptors for MIDI IN/OUT (embedded and external). The class interfaces with a callback handler (`USBD_MIDI_ItfTypeDef`) allowing the application to manage the physical transport of MIDI data.

---

## 🎯 Enumerations and Associated Structures

### USBD_MIDI_HandleTypeDef
Main management structure for the USB MIDI Device class.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `buffer` | `uint8_t[64]` | Data buffer for MIDI messages (maximum size 64 bytes). Used for reception and transmission. |
| `rxLength` | `uint32_t` | Length of data currently received in the buffer. Indicates the number of valid bytes available. |
| `txState` | `uint8_t` | Transmission state (0 = ready, other values for intermediate states). Indicates if a transmission is in progress. |
| `rxState` | `uint8_t` | Reception state (0 = ready, other values for intermediate states). Indicates if reception is pending. |

### USBD_MIDI_ItfTypeDef
Structure containing interface callbacks for the user application.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `Init` | `int8_t (*)(void)` | Initialization callback: function called when the MIDI class is activated. Returns 0 on success, -1 on failure. |
| `DeInit` | `int8_t (*)(void)` | Deinitialization callback: function called when the MIDI class is deactivated. Returns 0 on success, -1 on failure. |
| `Receive` | `int8_t (*)(uint8_t *Buf, uint32_t Len)` | Reception callback: function called when MIDI data is available in the buffer. Returns the number of bytes read or -1 on error. |

---

## 📚 Functions

### **USBD_MIDI_Init**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t USBD_MIDI_Init(USBD_HandleTypeDef *pdev, uint8_t cfgidx)` |
| **Description** | Initializes the USB MIDI Device class: allocates memory for the handle, opens IN/OUT bulk endpoints (0x81 and 0x01), configures transmission/reception state, and calls the `Init` callback of the user interface if registered. Prepares the OUT endpoint to receive the first packet. |
| **Parameter(s)** | |
| `pdev` | Pointer to the USBD_HandleTypeDef structure of the USB device. Contains global information about the USB device state. |
| `cfgidx` | Configuration index (generally 1 for the first configuration). Indicates which USB configuration is active. |
| **Return** | Status code: `USBD_OK` (0) on success, `USBD_EMEM` on memory allocation failure, other USBD error codes depending on HAL/LL operation results. |

### **USBD_MIDI_DeInit**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t USBD_MIDI_DeInit(USBD_HandleTypeDef *pdev, uint8_t cfgidx)` |
| **Description** | Deinitializes the USB MIDI Device class: closes IN/OUT bulk endpoints, calls the `DeInit` callback of the user interface to release physical resources, and frees the memory allocated for the handle. Resets endpoint usage flags to 0. |
| **Parameter(s)** | |
| `pdev` | Pointer to the USBD_HandleTypeDef structure of the USB device. |
| `cfgidx` | Configuration index (generally 1). |
| **Return** | Status code: `USBD_OK` (0) on success, other error codes on failure. |

### **USBD_MIDI_Setup**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t USBD_MIDI_Setup(USBD_HandleTypeDef *pdev, USBD_SetupReqTypedef *req)` |
| **Description** | Handles USB SETUP requests specific to the MIDI class. Currently does not implement class-specific requests (standard Audio MIDI classes typically do not require additional requests). Routes standard requests to the corresponding USBD handlers. |
| **Parameter(s)** | |
| `pdev` | Pointer to the USBD_HandleTypeDef structure of the USB device. |
| `req` | Pointer to the USBD_SetupReqTypedef structure containing SETUP request details (type, request, value, index, data length). |
| **Return** | Status code: `USBD_OK` (0) on success, other error codes on failure. |

### **USBD_MIDI_DataIn**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t USBD_MIDI_DataIn(USBD_HandleTypeDef *pdev, uint8_t epnum)` |
| **Description** | Callback called when the IN endpoint (0x81) is ready to transmit data. Prepares MIDI data from the buffer (`buffer`) for sending to the USB host. The effective transmission length is determined by `rxLength` or the buffer size depending on the current state. |
| **Parameter(s)** | |
| `pdev` | Pointer to the USBD_HandleTypeDef structure of the USB device. |
| `epnum` | Endpoint number (1 for MIDI_IN_EP = 0x81). |
| **Return** | Status code: `USBD_OK` (0) on success, other error codes on data preparation failure. |

### **USBD_MIDI_DataOut**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t USBD_MIDI_DataOut(USBD_HandleTypeDef *pdev, uint8_t epnum)` |
| **Description** | Callback called when the OUT endpoint (0x01) has received data from the USB host. Stores the data in the handle buffer and triggers the `Receive` callback of the user interface for application processing. Updates the reception state and prepares the next packet by calling `USBD_LL_PrepareReceive`. |
| **Parameter(s)** | |
| `pdev` | Pointer to the USBD_HandleTypeDef structure of the USB device. |
| `epnum` | Endpoint number (1 for MIDI_OUT_EP = 0x01). |
| **Return** | Status code: `USBD_OK` (0) on success, other error codes on failure. |

### **USBD_MIDI_GetFSCfgDesc**

| Element | Details |
|:---|:---|
| **Method** | `static uint8_t *USBD_MIDI_GetFSCfgDesc(uint16_t *length)` |
| **Description** | Returns the USB configuration descriptor for Full Speed (12 Mbps). The descriptor defines two interfaces: Interface 0 (Audio Control) and Interface 1 (MIDI Streaming with 2 bulk endpoints). Includes CS_INTERFACE descriptors for HEAD, MIDI_IN_JACK (embedded/external), and MIDI_OUT_JACK (embedded/external). |
| **Parameter(s)** | |
| `length` | Pointer to a variable where the length of the returned descriptor will be stored. The descriptor is 101 bytes total. |
| **Return** | Pointer to the descriptor array: `USBD_MIDI_CfgFSDesc`. |

---


## 📦 Data

| Member | Type | Description |
|:---|:---|:---|
| `pMidiItf` | `USBD_MIDI_ItfTypeDef *` | Pointer to the user callback structure registered via `USBD_MIDI_RegisterInterface()`. Allows calling Init/DeInit/Receive functions from the USB class. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.