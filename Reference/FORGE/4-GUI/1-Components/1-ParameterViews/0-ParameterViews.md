---
title: Parameter Views  
parent: GUI Components 
layout: default
nav_order: 1
---
# Parameter Views vvvff

**Namespace :** DadGUI  
**Files :** ParameterViews.h / ParameterViews.cpp  
**Directory :** DAD_FORGE/GUI/Components  
**Class :** DadGUI::cParameterViews  
**Class :** DadGUI::cParameterNumView  
**Class :** DadGUI::cParameterNumNormalView  
**Class :** DadGUI::cParameterNumLeftRightView  
**Class :** DadGUI::cParameterDiscretView  
**Description :**  
The **ParameterViews** module groups together all graphical components dedicated to displaying and editing parameters in the OSCAR/FORGE interface. It ensures the link between DSP parameters, user encoders and their on-screen visual representation, while offering several types of views adapted to numerical and discrete parameters.

---

## 📋 General Description
The ParameterViews module constitutes the presentation layer of parameters within the OSCAR/FORGE framework's graphical interface. Its role is to ensure the link between DSP parameter objects and their on-screen visual representation.  

Each view instance is associated with a user parameter and with one of the three parameter zones displayed on the interface.  
![](../../Interface2.png) 

It is responsible for:

* displaying the parameter name and value;
* graphically representing its state;
* managing interactions with the associated encoder;
* detecting changes coming from the user or other sources (MIDI, preset loading, etc.);
* dynamically updating the display when the value changes.

The system thus allows complete separation of the business logic of parameters from their graphical representation.

**Architecture**
The module is based on an object-oriented architecture based on inheritance.

Each parameter type has its own specialized display class, while sharing a common base provided by the cParameterView base class.

```
cParameterView (abstract)
├── cParameterNumView (numerical base with units)
│ └── cParameterNumNormalView (standard centered layout)
│ └── cParameterNumLeftRightView (left/right stereo-type layout)
└── cParameterDiscretView (discrete enumerated values)
```
---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Include.h"` | `DAD_FORGE/GUI/Core/Inc` | Global headers for GUI (constant definitions, types, palettes, __GUI) |
| `"cUIParameter.h"` | `DAD_FORGE/GUI/Core/Inc` | Parameter management class |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__Encoder1` | `DadDrivers::cEncoder*` | `extern in Drivers` | Tactile encoder 1 for parameter zone&nbsp;1. |
| `__Encoder2` | `DadDrivers::cEncoder*` | `extern in Drivers` | Tactile encoder 2 for parameter zone&nbsp;2. |
| `__Encoder3` | `DadDrivers::cEncoder*` | `extern in Drivers` | Tactile encoder 3 for parameter zone&nbsp;3. |
| `__GUI` | `cGUI*` | `extern in GUI/Core` | Main GUI interface.<BR>*Used to access GUI resources (e.g.: fonts)* |
| `__pActivePalette` | `eColorPalette_t*` | `extern in GUI/Core` | Pointer to the active color palette.<BR>*Used to draw graphical objects with theme colors.* |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.