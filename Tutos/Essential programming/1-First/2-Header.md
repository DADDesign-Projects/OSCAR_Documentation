---
title: Header File Declaration
parent: How to Develop Your First Effect
layout: default
nav_order: 2
---

# Header File Declaration

## Declaration

Open `Software_OSCAR_P01A01/DAD_FORGE/Effects/MyFirstEffect/Inc/cMyFirstEffect.h` and enter the following declaration:

```cpp
#pragma once
#include "cEffectBase.h"

class cMyFirstEffect : public cEffectBase {
public:
    // -------------------------------------------------------------------------
    // Constructor - performs no initialization by itself
    // -------------------------------------------------------------------------
    cMyFirstEffect() = default;

    // -------------------------------------------------------------------------
    // Initializes DSP components and user interface parameters
    // -------------------------------------------------------------------------
    void onInitialize() override;

    // -------------------------------------------------------------------------
    // Returns the unique effect identifier
    // -------------------------------------------------------------------------
    uint32_t getEffectID() override;

    // -------------------------------------------------------------------------
    // Audio processing function - processes one input/output audio buffer
    // -------------------------------------------------------------------------
    void onProcess(AudioBuffer *pIn, AudioBuffer *pOut, eOnOff OnOff, bool Silence) override;

protected:
    // =========================================================================
    // Protected Member Variables
    // =========================================================================

    // -------------------------------------------------------------------------
    // Parameter declarations
    // -------------------------------------------------------------------------
    DadGUI::cUIParameter                m_ParameterGain;        // Gain control parameter

    // -------------------------------------------------------------------------
    // Parameter view declarations
    // -------------------------------------------------------------------------
    DadGUI::cParameterNumNormalView     m_ParameterGainView;    // GUI view for the gain parameter

    // -------------------------------------------------------------------------
    // Panel declarations
    // -------------------------------------------------------------------------
    DadGUI::cPanelOfParameterView       m_ParameterFirstPanel;   // Demo panel containing parameter views
};
```

---

## Class Overview

### **Inheritance**

The cMyFirstEffect class inherits from cEffectBase.
This base class manages all the low-level operations required to integrate your effect into the FORGE system. It defines the following virtual methods, which form the interface between your effect and the rest of the system:
* onInitialize
* getEffectID
* onProcess

Implementing these methods is mandatory.

---

### **cMyFirstEffect() — Constructor**

The constructor is not used here. Unless you have a specific need, **all initialization must be done in `onInitialize()`**, which guarantees that all FORGE library components are fully initialized before your code runs.

---

### **onInitialize()**

This method is called by the system when the effect starts up. You must perform all initialization here:

- Declare and initialize GUI components.
- Initialize signal processing components (e.g., filters).
- Set up your private variables.

---

### **getEffectID()**
This method is called by the system to retrieve the unique identifier of the effect. It returns a 32-bit value (unsigned integer) that allows the system to identify the effect, for example when saving or restoring its parameters. 

---

### **onProcess(AudioBuffer *pIn, AudioBuffer *pOut, eOnOff OnOff, bool Silence)**

This method is called for every audio sample.

| Parameter | Description |
|-----------|-------------|
| `pIn`     | Pointer to the input buffer, containing left and right channel samples. |
| `pOut`    | Pointer to the stereo output buffer. |
| `OnOff`   | Current state of the pedal: `ByPass`, `Off`, or `On`. |
| `Silence` | Indicates whether the input is silent. |

Your processed audio must be written here.
Inside this method you will typically:
* Read input samples
* Apply DSP algorithms
* Process filters
* Apply gain
* Mix signals
* Write the processed result to the output buffer

---

### ***m_ParameterGain***
In FORGE, parameters are managed by the `DadDSP::CParameter` class.  
This class stores the parameter value as a 32-bits floating-point number. It provides a large number of features such as normalized value conversion (from 0.0f to 1.0f), smooth state changes, state change interception, MIDI interface support, and more.  

The `m_ParameterGain` variable implements the `DadGUI::cUIParameter` class. This class derives from `DadDSP::CParameter` and extends its functionality by interfacing with the GUI classes. Specifically, within our effect, m_ParameterGain is responsible for storing the gain value of the effect."

### ***m_ParameterGainView***
The `DadGUI::cParameterView` class is responsible for displaying a parameter's value and modifying it according to input from the connected encoder.  

The `m_ParameterGainView` variable implements the `DadGUI::cParameterNumNormalView` class. This class extends `DadGUI::cParameterView` and is specifically designed to handle the display of basic numerical parameters. Within our effect, `m_ParameterGainView` is responsible for displaying and animating the gain value on the screen.

### ***m_ParametrerFirstPanel***
In FORGE, a panel serves as the display interface associated with a menu item; when a menu item is selected, the corresponding panel is displayed and activated.  
The `DadGUI::cPanelOfParameterView` class manages a panel that displays three `DadGUI::cParameterView` instances on the screen. This class handles the routing of GUI events (such as encoder movement) to the associated `DadGUI::cParameterView`.  
Specifically within our effect, `m_ParametrerFirstPanel` is responsible for displaying `m_ParameterGainView` when its corresponding menu item is selected.  



