---
title: Code Implementation
parent: How to Develop Your First Effect
layout: default
nav_order: 3
---

# Code Implementation

Open `Software_OSCAR_P01A01/DAD_FORGE/Effects/MyFirstEffect/Src/cMyFirstEffect.cpp` and enter the following code:

```cpp
#include "EffectsConfig.h"

#ifdef MY_FIRST_EFFECT
#include "cMyFirstEffect.h"

// Unique effect identifier (32-bit)
constexpr uint32_t MY_FIRST_EFFECT_ID = BUILD_ID('M', 'F', 'E', '1');

// -----------------------------------------------------------------------------
// Method: onInitialize
// Description: Called once when the effect is loaded. Used to initialize 
//              parameters, GUI elements, and internal DSP objects.
// -----------------------------------------------------------------------------
void cMyFirstEffect::onInitialize()
{
    // Initialize the Gain parameter
    m_ParameterGain.Init(MY_FIRST_EFFECT_ID,   // Serialize ID
                         50.0f,                // Default value
                         0.0f,                 // Minimum value
                         100.0f,               // Maximum value
                         5.0f,                 // Fast increment
                         1.0f,                 // Slow increment
                         nullptr,              // Callback function
                         0,                    // Callback user data
                         0.5f,                 // Transition time (seconds)
                         20);                  // MIDI CC number

    // Initialize the GUI view for the parameter
    m_ParameterGainView.Init(&m_ParameterGain,   // Linked parameter
                             "Gain",             // Short name
                             "Gain",             // Long name
                             "%",                // Unit (short)
                             "percent");         // Unit (long)

    // Create a parameter panel for the user interface
    m_ParameterFirstPanel.Init(&m_ParameterGainView,   // Parameter View 1
                              nullptr,                // Parameter View 2
                              nullptr);               // Parameter View 3

    // Add the panel to the effect's menu
    m_Menu.addMenuItem(&m_ParameterDemoPanel, "First");
}

// -----------------------------------------------------------------------------
// Method: getEffectID
// Description: Returns the unique 32-bit identifier of the effect.
// -----------------------------------------------------------------------------
uint32_t cMyFirstEffect::getEffectID()
{
    return MY_FIRST_EFFECT_ID;
}

// -----------------------------------------------------------------------------
// Method: onProcess
// Description: Main audio processing function. Called continuously for 
//              every audio buffer.
// -----------------------------------------------------------------------------
void cMyFirstEffect::onProcess(AudioBuffer *pIn, AudioBuffer *pOut, eOnOff OnOff, bool Silence)
{
    // Get current gain value (converted from percentage to multiplier)
    float gainValue = m_ParameterGain.getValue() / 100.0f;

    // Apply gain to both channels
    pOut->Left  = pIn->Left  * gainValue;
    pOut->Right = pIn->Right * gainValue;
}
#endif
```
## Header Section
`EffectsConfig.h`: This configuration file allows for the enabling or disabling of various effects.  
The `#ifdef MY_FIRST_EFFECT` preprocessor directive ensures that this effect is compiled only if it is activated in the configuration settings.

## Global Variables
`constexpr uint32_t MY_FIRST_EFFECT_ID = BUILD_ID('M', 'F', 'E', '1');`:  
This defines the unique 32-bit identifier for the effect.  

>`BUILD_ID()` is a macro that converts four characters into a numerical ID (e.g., 'M','F','E','1').
{: .tip}

## onInitialize()
This function is called exactly once when the effect is loaded. Its purpose is to initialize all objects managed by the class.


***m_ParameterGain***

`m_ParameterGain.Init(...)`: Initializes a parameter specifically for our effect's gain control.

| Value | Parameter Name (Role) | Explanation |
| :---: | :--- | :--- |
| `MY_FIRST_EFFECT_ID` | **Serialize ID** | The unique identifier of the effect, used for serialization and internal management. |
| `50.0f` | **Default Value** | The default value of the Gain parameter upon initialization (here, 50%). |
| `0.0f` | **Minimum Value (Min)** | The lowest possible value the parameter can reach (here, 0%). |
| `100.0f` | **Maximum Value (Max)** | The highest possible value the parameter can reach (here, 100%). |
| `5.0f` | **Rapid Increment** | The step size used when rotating the encoder quickly. |
| `1.0f` | **Slow Increment** | The step size used when clicking and holding the encoder while turning it slowly. |
| `nullptr` | **Callback Function** | A function to be called whenever a parameter change occurs (here, no function is provided). |
| `0` | **Callback User Data** | User data passed as an argument to the callback function (`Callback`). |
| `0.5f` | **Slope Time** | The slope of the gradual state transition ramp. It determines the time (in seconds) required for the parameter to move from its minimum value to its maximum value. |
| `20` | **MIDI CC Number** | The MIDI Control Change message number associated with this parameter, allowing external control via a keyboard or controller. |


***m_ParameterGainView***
The variable `m_ParameterGainView` is initialized to create the visual view corresponding to the gain parameter.

| Value | Parameter Name (Role) | Explanation |
| :---: | :--- | :--- |
| `&m_ParameterGain` | **Parameter Instance** (`pParameter`) | A pointer to our gain parameter; it will be displayed and incremented by the view. |
| `"Gain"` | **Short Name** (`ShortName`) | The short name of the parameter for visualization in the Parameters Area. |
| `"Gain"` | **Long Name** (`LongName`) | The full name (identical to the short name here) for display in the Info Area. |
| `"%"` | **Short Unit** (`ShortUnit`) | The short unit symbol used to represent the value in the Parameters Area. |
| `"percent"` | **Long Unit** (`LongUnit`) | The long unit symbol used to represent the value in the Info Area. |

***m_ParametrerDemoPanel***
The variable `m_ParametrerDemoPanel` is initialized by passing a pointer to `m_ParameterGainView` at position 1. The other two positions are left empty (a null pointer).

***m_Menu.addMenuItem(&m_ParameterDemoPanel, "First")***
The `m_Menu` variable is declared and managed by our parent class, `DadEffect::cEffectBase`. We add our panel to the menu by passing a pointer to `m_ParameterGainView`, indicating that its corresponding menu item should display "First".

## getEffectID()
`uint32_t cTemplateEffect::getEffectID()`: This is a mandatory function that returns the unique identifier of the effect. It is used by the system for saving, loading, and other persistent operations. We return our specific ID here.

## onProcess()
This function is called continuously at the sampling frequency to process the audio signal.  
`float gainValue = m_ParameterGain.getValue() / 100.0f`: We retrieve the gain value, which ranges from 0 to 100. It is then divided by 100 to normalize it into a usable range of 0 to 1.

`pOut->Left = pIn->Left * gainValue;` `pOut->Right = pIn->Right * gainValue;` : For both the left and right channels, we multiply the input sample value by the calculated gain factor and record the result in the output buffer.

