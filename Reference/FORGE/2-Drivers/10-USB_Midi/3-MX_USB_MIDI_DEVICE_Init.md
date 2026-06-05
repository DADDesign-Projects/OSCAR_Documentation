---
title: MX_USB_MIDI_DEVICE_Init
parent: USB‑MIDI
layout: default
nav_order: 3
---
# MX_USB_MIDI_DEVICE_Init

**Files:** usb_midi_device.h / usb_midi_device.c   
**Directory:** DAD_FORGE/Drivers/_MIDI   
**Description:** Complete initialization function for the USB MIDI device with descriptor configuration and class registration

---

## 📋 Function Description

`MX_USB_MIDI_DEVICE_Init` is a high-level initialization function that fully configures the USB MIDI device for STM32. It orchestrates four critical steps: USB core initialization, MIDI class registration, user interface registration, and device startup. This function must be called once at the beginning of the program before the device is ready to communicate via USB.

---


## 📚 Function

### **MX_USB_MIDI_DEVICE_Init**

| Element | Details |
|:---|:---|
| **Method** | `void MX_USB_MIDI_DEVICE_Init(void)` |
| **Description** | Fully initializes the USB MIDI device by executing a sequence of 4 steps:<BR>(1) USB core initialization with MIDI-specific descriptors,<BR>(2) USB MIDI class registration (`USBD_MIDI`),<BR>(3) user interface registration (`USBD_MIDI_fops`),<BR>(4) USB device startup. Verifies the success of operations at each step and calls `Error_Handler()` on failure. |
| **Parameter(s)** | None |
| **Return** | None (void) - errors are handled by exception via `Error_Handler()` |

---

## 📦 Data

| Member | Type | Description |
|:---|:---|:---|
| `hUsbMidiDeviceFS` | `USBD_HandleTypeDef` | Global handle for the Full Speed USB MIDI device. Contains the device state, data buffers, and pointers to the registered MIDI class and interface. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.