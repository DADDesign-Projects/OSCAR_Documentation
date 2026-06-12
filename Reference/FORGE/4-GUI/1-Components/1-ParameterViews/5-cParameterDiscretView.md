---
title: cParameterDiscretView  
parent: Parameter Views 
layout: default
nav_order: 6
---
# DadGUI::cParameterDiscretView

**Namespace :** DadGUI  
**Files :** ParameterViews.h / ParameterViews.cpp  
**Directory :** DAD_FORGE/GUI/Components  
**Inheritance:** `cParameterView`  
**Class :** DadGUI::cParameterDiscretView  

---
# cParameterDiscretView - Discrete Enumerated Values

## 🎯 Associated Enumerations and Structures

### sDiscretValues
Internal structure storing the short/long label pairs for each discrete value available in an enumerated parameter.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `m_ShortValue` | `std::string` | Compact label to display on the graphical interface (e.g., "Low", "Med", "High"). |
| `m_LongValue` | `std::string` | Complete description used in the temporary info banner. |

---

## 📚 Public Methods

### **AddDiscreteValue**

| Element | Details |
|:---|:---|
| **Method** | `virtual void AddDiscreteValue(const std::string& ShortDiscretValue, const std::string& LongDiscretValue)` |
| **Description** | Adds a new discrete value to the selector. |
| **Parameter(s)** | |
| `ShortDiscretValue` | Short label of the discrete value. |
| `LongDiscretValue` | Long label of the discrete value. |
| **Return** | None (void) |

### **Draw**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Draw(uint8_t NumParameterArea, DadGFX::cLayer* pStaticLayer, DadGFX::cLayer* pDynamicLayer)` |
| **Description** | Completely draws the parameter:<BR>* The static parts of the parameter (name, decorations, area number).<BR>* The dynamic part of the parameter (cursor and value). |
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

### **getInfoValue**

| Element | Details |
|:---|:---|
| **Method** | `virtual const std::string getInfoValue()` |
| **Description** | Returns the discrete value of the parameter (long label). |
| **Parameter(s)** | None |
| **Return** | `std::string` - Long label of the selected discrete value. |

---

## 📦 Data Members

| Member | Type | Description |
|:---|:---|:---|
| `m_TabDiscretValues` | `std::vector<sDiscretValues>` | Dynamic array containing all available discrete values. Each element stores the pair (ShortValue, LongValue). |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.