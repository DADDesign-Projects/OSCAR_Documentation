---
title: cParameterNumView  
parent: Parameter Views 
layout: default
nav_order: 3
---
# DadGUI::cParameterNumView

**Namespace :** DadGUI  
**Files :** ParameterViews.h / ParameterViews.cpp  
**Directory :** DAD_FORGE/GUI/Components  
**Inheritance:** `cParameterView`  
**Class :** DadGUI::cParameterNumView  

# cParameterNumView - Base for Numerical Parameters
`cParameterNumView` is the base class for displaying numerical parameters.
It handles all the processing and common logic of a cParameterView (encoder management, value updating, external change detection, etc.).
 
Graphical drawing is delegated to specialized derived classes.

## 📚 Public Methods
### **Init**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Init(cUIParameter* pParameter, const std::string& ShortName, const std::string& LongName, const std::string& ShortUnit, const std::string& LongUnit, uint8_t StringPrecision)` |
| **Description** | Initializes the member data of the class. |
| **Parameter(s)** | |
| `pParameter` | Pointer to the parameter (cUIParameter) associated with the view. |
| `ShortName` | Short name of the parameter.<BR>*Displayed on the static layer above the potentiometer.* |
| `LongName` | Full name of the parameter.<BR>*Used in the temporary info view during updates.* |
| `ShortUnit` | Short unit displayed next to the value (e.g., "dB", "%"). |
| `LongUnit` | Long unit for info banner (e.g., "Decibels", "Percent"). |
| `StringPrecision` | Decimal precision for formatting (default 3).<BR>*Controls the number of digits after the decimal point.* |
| **Return** | None (void) |

### **ValueToString**

| Element | Details |
|:---|:---|
| **Method** | `virtual std::string ValueToString() const` |
| **Description** | Converts the target parameter value into a formatted string, taking into account the precision requested during initialization. |
| **Parameter(s)** | None |
| **Return** | `std::string` - Formatted numerical value (e.g., "-5.00", "10.500", "0") |

### **getInfoValue**

| Element | Details |
|:---|:---|
| **Method** | `virtual const std::string getInfoValue()` |
| **Description** | Returns the current parameter value formatted as a string with its short unit.<BR>Example return: `"-12.3 dB"` |
| **Parameter(s)** | None |
| **Return** | `std::string` - Formatted parameter value with unit. |

## 📦 Data Members

| Member | Type | Description |
|:---|:---|:---|
| `m_ShortUnit` | `std::string` | Short unit displayed next to the value (e.g., "dB", "%"). |
| `m_LongUnit` | `std::string` | Long unit for info banner (e.g., "Decibels", "Percent"). |
| `m_StringPrecision` | `uint8_t` | Decimal precision for ValueToString() formatting (default 3).<BR>*Controls the number of digits after the decimal point.* |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.