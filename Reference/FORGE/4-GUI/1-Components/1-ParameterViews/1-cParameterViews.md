---
title: cParameterView  
parent: Parameter Views 
layout: default
nav_order: 2
---
# DadGUI::cParameterView

**Namespace :** DadGUI  
**Files :** ParameterViews.h / ParameterViews.cpp  
**Directory :** DAD_FORGE/GUI/Components  
**Class :** DadGUI::cParameterView

# cParameterView - Abstract Base Class
cParameterView is the abstract base class of the system.

It provides common services for all views:
* association with a cUIParameter object;
* communication with the GUI system;
* encoder management;
* value change detection;
* management of information displayed in help banners;
* common interface used by the rest of the framework.

All specialized views inherit from this class.

**Graphical Performance Optimization**  
To optimize performance, each parameter uses a two-layer graphical system:

- **Static Layer**:  
  Contains all fixed visual elements that do not change while the panel is displayed.  
  *This layer is rendered once when the panel is displayed*.

- **Dynamic Layer**:  
  Contains only elements that need to be updated in real time (value cursor, numerical value, etc.).  
  *Only this layer is redrawn when the parameter state changes*.

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Init(cUIParameter* pParameter, const std::string& ShortName, const std::string& LongName)` |
| **Description** | Initializes the member data of the class. |
| **Parameter(s)** | |
| `pParameter` | Pointer to the parameter (cUIParameter) associated with the view. |
| `ShortName` | Short name of the parameter.<BR>*Displayed on the static layer above the potentiometer.* |
| `LongName` | Full name of the parameter.<BR>*Used in the temporary info view during updates.* |
| **Return** | None (void) |

### **Draw**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Draw(uint8_t NumParameterArea, DadGFX::cLayer* pStaticLayer, DadGFX::cLayer* pDynamicLayer)` |
| **Description** | Completely draws the parameter:<BR>* The static parts of the parameter (name, decorations, area number).<BR>* The dynamic part of the parameter (cursor and value)*<BR><BR>*Pure virtual method to be implemented in each derived class.* |
| **Parameter(s)** | |
| `NumParameterArea` | Parameter area identifier (1, 2 or 3). *Used to determine the linked encoder*. |
| `pStaticLayer` | Pointer to the static graphical layer.<BR> *Used for rendering permanent parameter elements.* |
| `pDynamicLayer` | Pointer to the dynamic graphical layer.<BR> *Used for rendering permanent parameter elements.* |
| **Return** | None (void) |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `virtual bool Update(uint8_t NumParameterArea, DadGFX::cLayer* pDynamicLayer)` |
| **Description** | Updates the parameter:<BR>* Retrieves the rotation (increment) and switch state of the appropriate encoder,<BR>* Increments the associated parameter according to the switch state (fast or slow increment).<BR>* Detects external changes (MIDI, preset restore).<BR>* Redraws the dynamic parameter cursor if the value changed.<BR><BR>Returns `true` if the value changed, `false` otherwise. | |
| **Parameter(s)** | |
| `NumParameterArea` | Area identifier (1-3). Determines which encoder to use for tactile interaction. |
| `pDynamicLayer` | Pointer to the graphical layer used to redraw the cursor if a change is detected. |
| **Return** | `bool` - true if the parameter value changed (redraw needed), false otherwise (no modification). |

### **getInfoName**

| Element | Details |
|:---|:---|
| **Method** | `virtual const std::string getInfoName()` |
| **Description** | Returns the long name of the parameter.<BR> *Default implementation returns m_LongName. Can be overridden by specialized views to return more detailed descriptions.* |
| **Parameter(s)** | None |
| **Return** | `std::string` - Full name/description of the parameter |

### **getInfoValue**

| Element | Details |
|:---|:---|
| **Method** | `virtual const std::string getInfoValue()` |
| **Description** | Pure virtual method to be implemented in each derived class.<BR>Returns the current parameter value formatted.<BR>*Each specialized view returns a format adapted to its type (numerical with units, or complete discrete label).* |
| **Parameter(s)** | None |
| **Return** | `std::string` - Formatted parameter value. |

### **DrawDynView**

| Element | Details |
|:---|:---|
| **Method** | `virtual void DrawDynView(uint8_t NumParameterArea, DadGFX::cLayer* pLayer)` |
| **Description** | Draws the dynamic part of the parameter (current value, cursor).<BR>*Pure virtual method to be implemented in each derived class.* |
| **Parameter(s)** | |
| `NumParameterArea` | Area identifier (1-3). |
| `pLayer` | Pointer to the dynamic graphical layer. |
| **Return** | None (void) |

## 📦 Data Members

| Member | Type | Description |
|:---|:---|:---|
| `m_ShortName` | `std::string` | Short name of the parameter. |
| `m_LongName` | `std::string` | Full name. |
| `m_pParameter` | `cUIParameter*` | Pointer to the parameter object. |
| `m_MemParameterValue` | `float` | Cache of the last known value. |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.