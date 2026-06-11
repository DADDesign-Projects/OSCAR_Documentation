---
title: cParameterNumNormalView  
parent: Parameter Views 
layout: default
nav_order: 4
---
# DadGUI::cParameterNumNormalView

**Namespace :** DadGUI  
**Files :** ParameterViews.h / ParameterViews.cpp  
**Directory :** DAD_FORGE/GUI/Components  
**Class :** DadGUI::cParameterNumNormalView  

# cParameterNumNormalView - Standard Centered Layout
This class specializes the `cParameterNumView` class for displaying a potentiometer that varies from left to right.

## 📚 Public Methods

### **Draw**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Draw(uint8_t NumParameterArea, DadGFX::cLayer* pStaticLayer, DadGFX::cLayer* pDynamicLayer)` |
| **Description** | Completely draws the parameter:<BR>* The static parts of the parameter (name, decorations, area number).<BR>* The dynamic part of the parameter (cursor and value). The circular cursor varies from left to right. |
| **Parameter(s)** | |
| `NumParameterArea` | Parameter area identifier (1, 2 or 3). *Used to determine the linked encoder*. |
| `pStaticLayer` | Pointer to the static graphical layer.<BR> *Used for rendering permanent parameter elements.* |
| `pDynamicLayer` | Pointer to the dynamic graphical layer.<BR> *Used for rendering dynamic parameter elements.* |
| **Return** | None (void) |

### **DrawDynView**

| Element | Details |
|:---|:---|
| **Method** | `virtual void DrawDynView(uint8_t NumParameterArea, DadGFX::cLayer* pLayer)` |
| **Description** | Draws the dynamic part of the parameter (current value, cursor). |
| **Parameter(s)** | |
| `NumParameterArea` | Area identifier (1-3). |
| `pLayer` | Pointer to the dynamic graphical layer. |
| **Return** | None (void) |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.