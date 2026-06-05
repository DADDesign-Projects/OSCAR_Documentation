---
title: USBD_MIDI_If
parent: USB‑MIDI
layout: default
nav_order: 1
---

# USBD_MIDI_If

**Files:** usbd_midi_if.h / usbd_midi_if.c   
**Directory:** DAD_FORGE/Drivers/_MIDI   
**Description:** User interface structure for the USB MIDI class with physical management callbacks

---

## 📋 Module Description

`USBD_MIDI_If` is a structure containing interface callbacks that allow the user application to manage the physical transport of MIDI data. This interface acts as a bridge between the USB layer (`USBD_MIDI` class) and the middleware/application, enabling complete control over initialization, deinitialization, and processing of MIDI data received via USB.

---

## 🎯 Enumerations and Associated Structures

No local enumerations or structures associated with this interface.

---

## 📚 Functions

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `int8_t Init(void)` |
| **Description** | Initialization callback called when the USB MIDI class is activated. This function must configure the physical transport (UART, SPI, etc.) to enable reception of MIDI data from the USB host. It is the main entry point for initializing the audio engine or connected peripherals. |
| **Parameter(s)** | None |
| **Return** | `0` on success, `-1` on initialization failure |

### **DeInit**

| Element | Details |
|:---|:---|
| **Method** | `int8_t DeInit(void)` |
| **Description** | Deinitialization callback called when the USB MIDI class is deactivated. This function must release all physical resources used for MIDI transport (close UART, disable SPI, stop DMA, etc.). Useful for sleep mode or device shutdown. |
| **Parameter(s)** | None |
| **Return** | `0` on success, `-1` on deinitialization failure |

### **Receive**

| Element | Details |
|:---|:---|
| **Method** | `int8_t Receive(uint8_t *Buf, uint32_t Len)` |
| **Description** | Reception callback called each time MIDI data is available in the USB buffer. The host sends data as 4-byte packets (MIDI CIN format) and this function allows interpreting and processing these messages according to the application context (synthesizer, sequencer, etc.). |
| **Parameter(s)** | |
| `Buf` | Pointer to an array containing the received MIDI bytes. Data is organized in 4-byte blocks with CIN (Code Index Number) as the first byte. |
| `Len` | Total number of bytes received in the buffer. Must be a multiple of 4 for standard MIDI messages. |
| **Return** | Number of bytes processed, or `-1` on processing error |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.