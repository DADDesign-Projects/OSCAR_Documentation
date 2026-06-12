---
title: cGFX
parent: STM_GFX2
layout: default
nav_order: 1
---

# DadGFX::cGFX

**Namespace:** DadGFX::cGFX  
**Files:** GFX.h / GFX.cpp  
**Directory:** DAD_FORGE/STM_GFX2  
**Inheritance:** Abstract base class  
**Description:** Abstract base class providing a set of graphic drawing utilities (geometric shapes, text, bitmaps) for rendering on graphic displays.

---

## 📋 Class Description

The `cGFX` class defines the interface and basic functionality for any graphic rendering engine in the `DadGFX` library. It provides drawing algorithms for simple shapes (lines, rectangles, circles, arcs) and text management via a font class (`cFont`). As an abstract class, it requires the implementation of low-level methods (`setPixel`, `setRectangle`, `fillRectWithBitmap`) in derived classes (such as `cLayer`), thereby separating drawing logic from hardware implementation or memory management.

---

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"main.h"` | `STM32 HAL` | System definitions and basic types |
| `"GFXFont.h"` | `DAD_FORGE/GUI/Core/` | Font structure definitions (GFXCFont, GFXglyph) |

### **External Functions Called**

| Function | Signature | Source | Usage |
|:---|:---|:---|:---|
| `Error_Handler` | `void Error_Handler(void)` | `STM32 HAL` | Critical error handling |

---

## 🎯 Associated Enumerations and Structures

### **DAD_GFX_ERROR**
Error codes for graphics operations.

| Element | Description |
|:---|:---|
| `OK` | Operation successful |
| `DMA2D_Error` | Error during DMA2D hardware operation |
| `Size_Error` | Size exceeds limits or invalid dimensions |
| `Memory_Error` | Memory allocation or access error |

### **sColor**
Structure representing a color in A8R8G8B8 format (32 bits).

| Element / Variable | Type / Value | Description |
|:---|:---|:---|
| `m_ARGB` | `uint32_t` | 32-bit color representation |
| `m_R` | `uint8_t` | Red channel |
| `m_G` | `uint8_t` | Green channel |
| `m_B` | `uint8_t` | Blue channel |
| `m_A` | `uint8_t` | Alpha channel (Transparency) |

### **cFont**
Class managing character fonts.

| Element / Variable | Type / Value | Description |
|:---|:---|:---|
| `m_pFont` | `const GFXCFont*` | Pointer to the font descriptor |
| `m_pTable` | `GFXglyph*` | Array of glyph descriptors |
| `m_PosHeight` | `int8_t` | Font height above the baseline |
| `m_NegHeight` | `int8_t` | Font height below the baseline |

---

## 📚 Public Methods

### **cGFX**

| Element | Details |
|:---|:---|
| **Method** | `cGFX()` |
| **Description** | Default constructor. |

### **drawRect**

| Element | Details |
|:---|:---|
| **Method** | `void drawRect(uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, uint16_t strokeWidth, const sColor& Color)` |
| **Description** | Draws an empty rectangle with a given stroke width. |
| **Parameter(s)** | |
| `x, y` | `uint16_t` Top-left corner coordinates |
| `Width, Height` | `uint16_t` Rectangle dimensions |
| `strokeWidth` | `uint16_t` Stroke thickness |
| `Color` | `const sColor&` Stroke color |

### **drawFillRect**

| Element | Details |
|:---|:---|
| **Method** | `inline void drawFillRect(uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, const sColor& Color)` |
| **Description** | Draws a filled rectangle. |
| **Parameter(s)** | |
| `x, y, Width, Height` | `uint16_t` Position and dimensions |
| `Color` | `const sColor&` Fill color |

### **drawLine**

| Element | Details |
|:---|:---|
| **Method** | `void drawLine(uint16_t x0, uint16_t y0, uint16_t x1, uint16_t y1, const sColor& Color)` |
| **Description** | Draws a line between two points using Bresenham's algorithm. |
| **Parameter(s)** | |
| `x0, y0` | `uint16_t` Starting point |
| `x1, y1` | `uint16_t` Ending point |
| `Color` | `const sColor&` Line color |

### **drawCircle**

| Element | Details |
|:---|:---|
| **Method** | `void drawCircle(uint16_t centerX, uint16_t centerY, uint16_t radius, const sColor& Color)` |
| **Description** | Draws an empty circle (outline) using Bresenham's algorithm. |
| **Parameter(s)** | |
| `centerX, centerY` | `uint16_t` Circle center |
| `radius` | `uint16_t` Radius |
| `Color` | `const sColor&` Outline color |

### **drawFillCircle**

| Element | Details |
|:---|:---|
| **Method** | `void drawFillCircle(uint16_t centerX, uint16_t centerY, uint16_t radius, const sColor& Color)` |
| **Description** | Draws a filled circle. |
| **Parameter(s)** | |
| `centerX, centerY` | `uint16_t` Circle center |
| `radius` | `uint16_t` Radius |
| `Color` | `const sColor&` Fill color |

### **drawArc**

| Element | Details |
|:---|:---|
| **Method** | `void drawArc(uint16_t centerX, uint16_t centerY, uint16_t radius, uint16_t AlphaIn, uint16_t AlphaOut, const sColor& Color)` |
| **Description** | Draws a circular arc between two angles (in degrees). |
| **Parameter(s)** | |
| `centerX, centerY` | `uint16_t` Center |
| `radius` | `uint16_t` Radius |
| `AlphaIn` | `uint16_t` Start angle (0-360) |
| `AlphaOut` | `uint16_t` End angle (0-360) |
| `Color` | `const sColor&` Arc color |

### **setCursor**

| Element | Details |
|:---|:---|
| **Method** | `void setCursor(uint16_t x, uint16_t y)` |
| **Description** | Sets the current cursor position for text rendering. |
| **Parameter(s)** | |
| `x, y` | `uint16_t` Cursor coordinates |

### **setFont**

| Element | Details |
|:---|:---|
| **Method** | `void setFont(cFont *pFont)` |
| **Description** | Sets the font to be used for text rendering. |
| **Parameter(s)** | |
| `pFont` | `cFont*` Pointer to the font object |

### **drawChar**

| Element | Details |
|:---|:---|
| **Method** | `void drawChar(const char c)` |
| **Description** | Draws a single character at the cursor position. |
| **Parameter(s)** | |
| `c` | `char` The character to draw |

### **drawText**

| Element | Details |
|:---|:---|
| **Method** | `void drawText(const char *Text)` |
| **Description** | Draws a character string following the cursor position. |
| **Parameter(s)** | |
| `Text` | `const char*` The character string |

---

## 🔒 Protected / Private Methods

### **setPixel**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR setPixel(uint16_t x, uint16_t y, const sColor& Color) = 0` |
| **Description** | Pure virtual method: draws a pixel at a given position. Must be implemented by the derived class. |
| **Parameter(s)** | |
| `x, y` | `uint16_t` Pixel coordinates |
| `Color` | `const sColor&` Pixel color |

### **setRectangle**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR setRectangle(uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, const sColor& Color) = 0` |
| **Description** | Pure virtual method: fills a rectangular area. Must be implemented by the derived class. |
| **Parameter(s)** | |
| `x, y, Width, Height` | `uint16_t` Position and dimensions |
| `Color` | `const sColor&` Fill color |

### **fillRectWithBitmap**

| Element | Details |
|:---|:---|
| **Method** | `virtual DAD_GFX_ERROR fillRectWithBitmap(uint16_t x0, uint16_t y0, const uint8_t* pBitmap, uint16_t BitmapWidth, uint16_t BitmapBmpHeight, const sColor& ForegroundColor, const sColor& BackgroundColor) = 0` |
| **Description** | Pure virtual method: fills an area with a bitmap (1 bit per pixel). |
| **Parameter(s)** | |
| `x0, y0` | `uint16_t` Top-left corner |
| `pBitmap` | `const uint8_t*` Pointer to bitmap data |
| `BitmapWidth, BitmapBmpHeight` | `uint16_t` Bitmap dimensions |
| `ForegroundColor, BackgroundColor` | `const sColor&` Rendering colors |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.