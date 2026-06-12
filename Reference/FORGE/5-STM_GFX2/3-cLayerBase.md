---
title: cLayerBase
parent: STM_GFX2
layout: default
nav_order: 3
---

# DadGFX::cLayerBase

**Namespace:** DadGFX::cLayerBase  
**Files:** cDisplay.h  
**Directory:** DAD_FORGE/STM_GFX2  
**Inheritance:** None (Root base class for graphical layers)  
**Description:** Abstract base class that defines the fundamental properties of all graphical layers (`cLayer`, `cImageLayer`). It manages spatial position, dimensions, Z-order, and maintains the link with the `cDisplay` display manager object.

---

## 📋 Class Description

`cLayerBase` provides the necessary infrastructure for any layer to exist in the rendering system. It is responsible for notifying the display system of movements or Z-order changes in order to trigger the necessary refreshes.

---

## 📚 Public Methods

### **cLayerBase**

| Element | Details |
|:---|:---|
| **Method** | `cLayerBase()` |
| **Description** | Default constructor. |

### **~cLayerBase**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~cLayerBase()` |
| **Description** | Virtual destructor to ensure proper destruction of derived classes. |

### **moveLayer**

| Element | Details |
|:---|:---|
| **Method** | `DAD_GFX_ERROR moveLayer(uint16_t x, uint16_t y)` |
| **Description** | Moves the layer to a new position. The method invalidates both the old layer area and the new one to force screen refresh. |
| **Parameter(s)** | |
| `x, y` | `uint16_t`: New layer coordinates. |
| **Return** | `DAD_GFX_ERROR` | `OK` if the move is validated. |

### **changeZOrder**

| Element | Details |
|:---|:---|
| **Method** | `void changeZOrder(uint8_t z)` |
| **Description** | Changes the Z-order of the layer. Notifies the display manager for potential layer re-sorting and invalidates the layer area. |
| **Parameter(s)** | |
| `z` | `uint8_t`: New Z-order. |

### **Accessors (Getters)**

| Element | Details |
|:---|:---|
| **Method** | `getX()`, `getY()`, `getZ()`, `getWidth()`, `getHeight()` |
| **Description** | Returns respectively the X position, Y position, Z-order, width, and height of the layer. |

---

## 🔒 Protected / Private Methods

### **getFrame**

| Element | Details |
|:---|:---|
| **Method** | `sColor* getFrame()` |
| **Description** | Retrieves the pointer to the layer's pixel buffer (used by drawing classes). |
| **Return** | `sColor*`: Pointer to the color buffer. |

---

## 📦 Data

### Protected Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pDisplay` | `cDisplay*` | Pointer to the display manager instance. |
| `m_X` | `uint16_t` | Horizontal position of the layer. |
| `m_Y` | `uint16_t` | Vertical position of the layer. |
| `m_Width` | `uint16_t` | Width of the layer. |
| `m_Height` | `uint16_t` | Height of the layer. |
| `m_Z` | `uint8_t` | Z-order. |
| `m_pLayerFrame` | `union` | Pointer to layer data: |
| | `sColor*` | (For `cLayer`) Pointer to the modifiable framebuffer. |
| | `const uint8_t*` | (For `cImageLayer`) Pointer to constant image data. |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.