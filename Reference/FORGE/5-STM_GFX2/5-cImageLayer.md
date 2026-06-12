---
title: cImageLayer
parent: STM_GFX2
layout: default
nav_order: 5
---

# DadGFX::cImageLayer

**Namespace:** DadGFX::cImageLayer  
**Files:** cDisplay.h / cDisplay.cpp  
**Directory:** DAD_FORGE/STM_GFX2  
**Inheritance:** cLayerBase  
**Description:** Class managing an image layer dedicated to bitmap display.

---

## 📋 Class Description

The `cImageLayer` class is a specialization of `cLayerBase` designed for displaying static or animated content stored in memory (typically in Flash or SDRAM). Unlike a traditional drawing layer, it is optimized for manipulating precomputed image buffers. It supports multiple image frames to enable simple animations by simply changing the active frame index.

---

## 📚 Public Methods

### **cImageLayer**

| Element | Details |
|:---|:---|
| **Method** | `cImageLayer()` |
| **Description** | Default constructor. |

### **~cImageLayer**

| Element | Details |
|:---|:---|
| **Method** | `~cImageLayer()` |
| **Description** | Destructor. |

### **setFrame**

| Element | Details |
|:---|:---|
| **Method** | `void setFrame(uint8_t Frame)` |
| **Description** | Updates the active frame index. Calculates the new pointer to the image memory and invalidates the layer area to force a refresh. |
| **Parameter(s)** | |
| `Frame` | `uint8_t`: The index of the frame to display (0 to `m_NbFrame - 1`). |

---

## 🔒 Protected / Private Methods

### **init**

| Element | Details |
|:---|:---|
| **Method** | `void init(cDisplay* pDisplay, const uint8_t* pLayerFrame, uint16_t y, uint16_t x, uint16_t Width, uint16_t Height, uint8_t zPos, uint8_t NbFrame = 1)` |
| **Description** | Initializes the image layer attributes, including the pointer to the start of the frame sequence and the number of frames. |
| **Parameter(s)** | |
| `pDisplay` | `cDisplay*`: Pointer to the display manager. |
| `pLayerFrame` | `const uint8_t*`: Pointer to the start of the image sequence in memory. |
| `x, y` | `uint16_t`: Layer position. |
| `Width, Height` | `uint16_t`: Layer dimensions. |
| `zPos` | `uint8_t`: Z-order. |
| `NbFrame` | `uint8_t`: Total number of frames in the sequence. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_NbFrame` | `uint8_t` | Total number of frames available in the sequence. |
| `m_pLayerFrameStart` | `const uint8_t*` | Pointer to the starting memory address of the image sequence. |
| `m_FrameSize` | `uint32_t` | Size in bytes of a single frame. |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.