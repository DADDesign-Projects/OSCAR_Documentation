---
title: cPaletteBuilder
parent: Themes
layout: default
nav_order: 2
---

# DadGUI::cPaletteBuilder

**Namespace:** DadGUI::cPaletteBuilder  
**Files:** cPaletteBuilder.h / cPaletteBuilder.cpp  
**Directory:** DAD_FORGE/GUI/Themes/  
**Inheritance:** N/A  
**Description:** Parses and processes color palette definitions received via a USB serial connection (ASCII format) to update a palette structure in real-time.

## 📋 Class Description
The `cPaletteBuilder` class implements a lightweight incremental parser based on a state machine. It is designed to process ASCII data streams arriving character by character (via a FIFO buffer). The expected format is `ColorName=#RRGGBB`. When a valid command is detected, the corresponding color in the local palette is updated, enabling dynamic customization of the user interface.

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"main.h"` | `System` | Basic project definitions |
| `"cBuff.h"` | `DAD_FORGE/Utils` | FIFO buffer management for serial data |
| `"GUI_Defines.h"` | `DAD_FORGE/GUI` | Graphics type definitions (e.g., `sColor`) |
| `"@Options.h"` | `DAD_FORGE/` | Project configuration |

### **Global Variables**

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__DataBuff` | `DadUtilities::cBuff` | `extern` in `cPaletteBuilder.cpp` | Global FIFO buffer receiving USB data |

### **External Functions Called**

| Function | Source | Usage |
|:---|:---|:---|
| `UsbCallback` | `USB Driver` | Callback function called by the USB driver to fill the `__DataBuff` buffer |

## 🎯 Enumerations and Associated Structures

### **State (Enum)**

Represents the current state of the parser's state machine.
| Element | Value | Description |
|:---|:---|:---|
| `WAITING_NAME` | - | Waiting for the first valid character of the color name |
| `READING_NAME` | - | Reading the color name until the '=' character |
| `WAITING_HASH` | - | Waiting for the '#' character preceding the hex code |
| `READING_COLOR` | - | Reading the 6-character hex sequence (RRGGBB) |

### **sColorPalette (Structure)**

Structure containing all the interface color properties (Menu, Settings, Info, VU Meter, etc.).

## 📚 Public Methods

### **cPaletteBuilder**

| Element | Details |
|:---|:---|
| **Method** | `cPaletteBuilder()` |
| **Description** | Constructor. Initializes the parser and resets the change flags. |

### **InitPalette**

| Element | Details |
|:---|:---|
| **Method** | `void InitPalette(const DadGUI::sColorPalette* pPalette)` |
| **Description** | Initializes the internal working palette by copying the provided reference structure. |
| **Parameter(s)** | |
| `pPalette` | `const sColorPalette*` : Pointer to the source palette. |

### **ParseBuffer**

| Element | Details |
|:---|:---|
| **Method** | `uint16_t ParseBuffer()` |
| **Description** | Iterates through the entire current FIFO buffer and processes each byte. Resets the buffer after processing. |
| **Return** | `uint16_t` : Number of validated and applied color definitions. |

### **ProcessByte**

| Element | Details |
|:---|:---|
| **Method** | `bool ProcessByte(uint8_t byte)` |
| **Description** | Feeds the state machine with a single byte. |
| **Parameter(s)** | |
| `byte` | `uint8_t` : The received byte. |
| **Return** | `bool` : `true` if a complete color was successfully extracted and stored. |

### **Clear**

| Element | Details |
|:---|:---|
| **Method** | `void Clear()` |
| **Description** | Resets temporary buffers and returns the state machine to its initial state. |

### **Reset**

| Element | Details |
|:---|:---|
| **Method** | `void Reset()` |
| **Description** | Resets the parser state without modifying palette data. |

### **HexToValue**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t HexToValue(char hex)` |
| **Description** | Converts a hexadecimal character to its numeric value (0-15). |
| **Parameter(s)** | |
| `hex` | `char` : Hexadecimal character. |
| **Return** | `uint8_t` : Numeric value or 0 if invalid character. |

### **ParseHexColor**

| Element | Details |
|:---|:---|
| **Method** | `DadGFX::sColor ParseHexColor()` |
| **Description** | Converts the 6-character hex buffer into an `sColor` color structure with an opacity of 255 (0xFF). |
| **Return** | `DadGFX::sColor` : The decoded color. |

### **StoreColor**

| Element | Details |
|:---|:---|
| **Method** | `bool StoreColor()` |
| **Description** | Compares the name stored in the buffer with the fields of the `m_Palette` structure and applies the color if a match is found. |
| **Return** | `bool` : `true` if a color was successfully associated with a palette member. |

### **getPalette**

| Element | Details |
|:---|:---|
| **Method** | `DadGUI::sColorPalette* getPalette()` |
| **Description** | Returns a pointer to the internal working palette. |
| **Return** | `DadGUI::sColorPalette*` : Pointer to the palette. |

### **IsChangedPalette**

| Element | Details |
|:---|:---|
| **Method** | `bool IsChangedPalette()` |
| **Description** | Checks if at least one color has been modified since the last read. Resets the internal flag after the call. |
| **Return** | `bool` : `true` if a change was detected. |

## 🔒 Private Methods

### **HexToValue**

## 📦 Data Members

### Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_Palette` | `DadGUI::sColorPalette` | Local color palette being modified. |
| `m_State` | `State` | Current state of the parser's state machine. |
| `m_NameBuffer` | `char[32]` | Temporary buffer storing the color name. |
| `m_NameLen` | `uint8_t` | Current length of the name in the buffer. |
| `m_ColorBuffer` | `char[6]` | Buffer storing the 6 hexadecimal characters. |
| `m_ColorLen` | `uint8_t` | Number of hexadecimal characters read. |
| `m_ChangedPalette` | `bool` | Flag indicating whether the palette has been modified. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.