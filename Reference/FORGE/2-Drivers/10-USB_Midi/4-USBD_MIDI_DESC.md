---
title: USBD_Midi_Desc
parent: USB‑MIDI
layout: default
nav_order: 4
---
# USBD_Midi_Desc

**Files:** usbd_midi_desc.c   
**Directory:** DAD_FORGE/Drivers/_MIDI
**Description:** USB descriptor management for Full Speed MIDI device

---

## 📋 Class Description
USB MIDI descriptors implementation module (USB MIDI Device Class). This module provides all necessary functions to generate and return USB descriptors required by the USB Host Controller system, including device descriptor, string descriptors (manufacturer, product, serial, configuration, interface), and language ID. It is specifically designed for Full Speed (FS) operation with a MIDI device named "OSCAR MIDI".

---

## 🎯 Enumerations and Associated Structures
The class uses the external `USBD_DescriptorsTypeDef` structure defined in the standard USB framework.

---

## 📚 Mandatory Functions

### **USBD_Midi_FS_DeviceDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_DeviceDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the USB device descriptor containing basic MIDI device information (vendor ID, product ID, version number, maximum packet size) |
| **Parameter(s)** | |
| `speed` | USB speed type (Full Speed or High Speed). Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the device descriptor (`USBD_Midi_FS_DeviceDesc`). |

---

### **USBD_Midi_FS_LangIDStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_LangIDStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the Language ID string descriptor indicating the USB device's language (French - code 1036). |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the Language ID descriptor (`USBD_Midi_LangIDDesc`). |

---

### **USBD_Midi_FS_ManufacturerStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_ManufacturerStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the string descriptor containing the manufacturer name ("STMicroelectronics"). The string is converted to USB Unicode format. |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the manufacturer string descriptor (`USBD_Midi_StrDesc`). |

---

### **USBD_Midi_FS_ProductStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_ProductStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the string descriptor containing the product name ("OSCAR MIDI"). The string is converted to USB Unicode format. |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the product string descriptor (`USBD_Midi_StrDesc`). |

---

### **USBD_Midi_FS_SerialStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_SerialStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the string descriptor containing a unique serial number generated from the microcontroller's hardware identifiers (DEVICE_ID1, DEVICE_ID2, DEVICE_ID3). |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the serial number (`USBD_Midi_StringSerial`). |

---

### **USBD_Midi_FS_ConfigStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_ConfigStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the string descriptor containing the configuration description ("MIDI Config"). The string is converted to USB Unicode format. |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the configuration string descriptor (`USBD_Midi_StrDesc`). |

---

### **USBD_Midi_FS_InterfaceStrDescriptor**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t * USBD_Midi_FS_InterfaceStrDescriptor(USBD_SpeedTypeDef speed, uint16_t *length)` |
| **Description** | Returns the string descriptor containing the interface description ("MIDI Interface"). The string is converted to USB Unicode format. |
| **Parameter(s)** | |
| `speed` | USB speed type. Parameter ignored for this Full Speed device. |
| `length` | Pointer to a variable that will receive the length of the returned descriptor in bytes. |
| **Return** | Pointer to the buffer containing the interface string descriptor (`USBD_Midi_StrDesc`). |

## 📚 Utility Functions

### **Get_SerialNum**

| Element | Details |
|:---|:---|
| **Method** | `static void Get_SerialNum(void)` |
| **Description** | Generates a unique serial number by combining the microcontroller's hardware identifiers (DEVICE_ID1, DEVICE_ID2, DEVICE_ID3) and converts the result to Unicode format for storage in the serial string buffer. |
| **Parameter(s)** | No parameters. Static method with no arguments. |
| **Return** | No return value (void). Directly modifies the `USBD_Midi_StringSerial` buffer. |

---

### **IntToUnicode**

| Element | Details |
|:---|:---|
| **Method** | `static void IntToUnicode(uint32_t value, uint8_t * pbuf, uint8_t len)` |
| **Description** | Converts a 32-bit integer value to a hexadecimal Unicode character string (high-speed USB ASCII format). Each nibble is converted to a '0'-'9' or 'A'-'F' character. |
| **Parameter(s)** | |
| `value` | 32-bit integer value to convert. |
| `pbuf` | Pointer to the destination buffer for the Unicode string (must be aligned). |
| `len` | Maximum conversion length in bytes (number of nibbles × 2). |
| **Return** | No return value (void). |

---

## 📦 Data

| Member | Type | Description |
|:---|:---|:---|
| `USBD_Midi_FS_DeviceDesc` | `uint8_t[USB_LEN_DEV_DESC]` | Buffer containing the USB device descriptor (type, version, vendor/product ID, maximum packet size, etc.). Memory aligned. |
| `USBD_Midi_LangIDDesc` | `uint8_t[USB_LEN_LANGID_STR_DESC]` | Buffer containing the Language ID descriptor (code 1036 = French). Memory aligned. |
| `USBD_Midi_StrDesc` | `uint8_t[USBD_MAX_STR_DESC_SIZ]` | Generic buffer for Unicode string descriptors (manufacturer, product, configuration, interface). Reused by multiple functions. |
| `USBD_Midi_StringSerial` | `uint8_t[USB_SIZ_STRING_SERIAL]` | Buffer containing the device's unique serial number, generated from hardware identifiers. Memory aligned. |
| `FS_Midi_Desc` | `USBD_DescriptorsTypeDef` | Structure aggregating callbacks for all USB MIDI descriptors (pointer to each descriptor function). |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.