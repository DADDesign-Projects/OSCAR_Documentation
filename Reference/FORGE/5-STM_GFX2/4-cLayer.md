---
title: cLayer
parent: STM_GFX2
layout: default
nav_order: 4
---

# DadGFX::cLayer

**Namespace:** DadGFX::cLayer  
**Files:** cDisplay.h / cDisplay.cpp  
**Directory:** DAD_FORGE/STM_GFX2  
**Inheritance:** `cLayerBase`, `cGFX`  
**Description:** Class managing an active graphical layer. It allows drawing primitives (pixels, rectangles) and bitmaps on its own framebuffer.

---

## 📋 Class Description

The `cLayer` class is the main drawing element used by the user to modify the display. It acts as an intermediate buffer that receives drawing commands (lines, circles, rectangles, etc.).

It has two operating modes for color rendering:

1. **Blend Mode**: Calculates the blending between the source color and the existing framebuffer color using the Alpha component.
2. **Overwrite Mode**: Directly overwrites the framebuffer data with the new color.

---

## 📚 Public Methods

### **eraseLayer**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR eraseLayer(const sColor& Color = sColor(0,0,0,0))` |
| **Description** | Erases the entire layer by replacing each pixel with the specified color. |
| **Parameter(s)** | |
| `Color` | `const sColor&`: The fill color (default is transparent black). |
| **Return** | `DAD_GFX_ERROR` | `OK` if the operation succeeds, otherwise an error code. |

### **setMode**

| Element | Details |
|:---|:---|
| **Method** | `inline void setMode(DRAW_MODE Mode)` |
| **Description** | Sets the rendering mode for subsequent drawing operations (Blend or Overwrite). |
| **Parameter(s)** | |
| `Mode` | `DRAW_MODE`: `Blend` for transparency, `Overwrite` for overwriting. |

---

## 🔒 Protected / Private Methods

### **init**

| Element | Details |
|:---|:---|
| **Method** | `void init(cDisplay* pDisplay, sColor* pLayerFrame, uint16_t y, uint16_t x, uint16_t Width, uint16_t Height, uint8_t zPos)` |
| **Description** | Initializes the layer parameters (position, size, framebuffer, Z-order) and clears the framebuffer. |
| **Parameter(s)** | |
| `pDisplay` | `cDisplay*`: Pointer to the display manager. |
| `pLayerFrame` | `sColor*`: Pointer to the layer's pixel buffer. |
| `x, y` | `uint16_t`: Layer coordinates. |
| `Width, Height` | `uint16_t`: Layer dimensions. |
| `zPos` | `uint8_t`: Z-order. |

### **setPixel**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR setPixel(uint16_t x, uint16_t y, const sColor& Color)` |
| **Description** | Draws a single pixel. Handles alpha blending (software) or direct writing. |
| **Parameter(s)** | |
| `x, y` | `uint16_t`: Pixel coordinates. |
| `Color` | `const sColor&`: The pixel color. |
| **Return** | `DAD_GFX_ERROR` | `OK` or `Size_Error` if out of bounds. |

### **setRectangle**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR setRectangle(uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, const sColor& Color)` |
| **Description** | Draws a rectangle. If `USE_DMA2D` is defined, uses the hardware engine for filling and blending. Otherwise, uses a software algorithm. |
| **Parameter(s)** | |
| `x, y` | `uint16_t`: Top-left corner coordinates. |
| `Width, Height` | `uint16_t`: Dimensions. |
| `Color` | `const sColor&`: Fill color. |
| **Return** | `DAD_GFX_ERROR` | `OK` or `Size_Error`. |

### **fillRectWithBitmap**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR fillRectWithBitmap(uint16_t x0, uint16_t y0, const uint8_t* pBitmap, uint16_t BitmapWidth, uint16_t BitmapBmpHeight, const sColor& ForegroundColor, const sColor& BackgroundColor)` |
| **Description** | Fills a rectangular area using a bitmap (1 bit per pixel) to determine the color (Foreground/Background). |
| **Parameter(s)** | |
| `x0, y0` | `uint16_t`: Top-left corner. |
| `pBitmap` | `const uint8_t*`: Pointer to compressed bitmap data. |
| `BitmapWidth, BitmapBmpHeight` | `uint16_t`: Bitmap dimensions. |
| `ForegroundColor` | `const sColor&`: Color for bits set to 1. |
| `BackgroundColor` | `const sColor&`: Color for bits set to 0. |
| **Return** | `DAD_GFX_ERROR` | `OK` or `Size_Error`. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_Mode` | `DRAW_MODE` | Current rendering mode (Blend or Overwrite). |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.