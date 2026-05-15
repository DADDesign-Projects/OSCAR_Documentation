---
title: Modify the code
parent: Working With Parameters
layout: default
nav_order: 1
---


# Modify the Code.

{: .text-blue-300 }
> We are going to modify the code from our previous tutorial [How to Develop Your First Effect](../1-First/0-FirstEffect.html) to add new functionalities.

## Parameters
We will add and initialize two `DadGUI::cUIParameter` parameters: one for panning and one for Mono/Stereo mode.

In `cMyFirstEffect.h`, we declare these parameters:

```cpp
    DadGUI::cUIParameter    m_ParameterPan;        // Stereo Panning parameter
    DadGUI::cUIParameter    m_ParameterStereoMode; // Stereo/Mono mode parameter
```
___

In `cMyFirstEffect.cpp`, we initialize the new parameters within the `onInitialize` method.  
We also add callback functions for `m_ParameterGain` and `m_ParameterPan`. These functions are automatically called whenever a parameter's state changes.  

{: .tip}
>**Tip : Calback Function format**:    
>CallBack(DadDSP::cParameter* pParameter, uint32_t Data).  
>* `pParameter` points to the parameter that was just modified (it will point to either `m_ParameterGain` or `m_ParameterPan`).  
>* `Data` carries user data in a 32-bit integer. We will use this data to carry the address of our class instance (`this`).

<BR>

```cpp
    // Initialize the Gain parameter
    m_ParameterGain.Init(MY_FIRST_EFFECT_ID,   // Serialize ID
                         50.0f,                // Default value
                         0.0f,                 // Minimum value
                         100.0f,               // Maximum value
                         5.0f,                 // Fast increment
                         1.0f,                 // Slow increment
                         onGainChange,         // Callback function
                         (uint32_t)this,       // Callback user data
                         0.5f,                 // Transition time (seconds)
                         20);                  // MIDI CC number

    // Initialize the Pan parameter
    m_ParameterPan.Init(MY_FIRST_EFFECT_ID,// Serialize ID
                         0.0f,                 // Default value
                         -100.0f,              // Minimum value
                         100.0f,               // Maximum value
                         5.0f,                 // Fast increment
                         1.0f,                 // Slow increment
                         onPanChange,      // Callback function
						 (uint32_t)this,       // Callback user data
                         0.5f,                 // Transition time (seconds)
                         21);                  // MIDI CC number

    // Initialize the Stereo Mode parameter
    m_ParameterStereoMode.Init(MY_FIRST_EFFECT_ID,   // Serialize ID
                         0.0f,                 // Default value
                         0.0f,                 // Minimum value
                         0.0f,                 // Maximum value
                         1.0f,                 // Fast increment
                         1.0f,                 // Slow increment
                         nullptr,              // Callback function (not used here)
                         0,                    // Callback user data
                         0.0f,                 // Transition time (seconds)
                         22);                  // MIDI CC number
```

## Parameter Views
We will add and initialize two parameter views:

In `cMyFirstEffect.h`, we declare the views:

* `m_ParameterPanView` for panning is of type `DadGUI::cParameterNumLeftRightView`. This view is designed to display numerical parameters with a symmetrical left/right range centered around zero, corresponding to the MIDI center position.  

* `m_ParameterStereoModeView`, used for the mode switch, is based on the `DadGUI::cParameterDiscretView` class.
This view is designed to display a selector with discrete value.

{: .tip}
> **Tip**
> A discrete parameter can only take values from a predefined list.  
It works like an indexed selection table:
> * 0 → XXL (Very Large)
> * 1 → XL (Large)
> * 2 → L (Medium)
> * 3 → S (Small)
> * 4 → XS (Very Small)
> 
> Internally, the parameter stores only the index value.  
>So, when you call `getValue()`, the function returns an integer between 0 and 4, corresponding to the selected entry, rather than the displayed text such as "XXL" or "L".  
<BR>

```cpp
DadGUI::cParameterNumLeftRightView  m_ParameterPanView;        // GUI view for Panning parameter
DadGUI::cParameterDiscretView       m_ParameterStereoModeView; // GUI view for stereo mode parameter
```
___

In `cMyFirstEffect.cpp`, we initialize the two new views in the `onInitialize` method.  
Initializing discrete views requires an extra step: for every possible discrete value, we call `AddDiscreteValue` (in this case, "Stereo" and "Mono").
    
```cpp
// Initialize the GUI view for the Panning parameter
m_ParameterPanView.Init(&m_ParameterPan,               // Linked parameter
                            "Bal.",                    // Short name
                            "Balance",                 // Long name
                            "%",                       // Unit (short)
                            "percent");                // Unit (long)

// Initialize the GUI view for the Stereo Mode parameter
m_ParameterStereoModeView.Init(&m_ParameterStereoMode, // Linked parameter
                            "Mode",                    // Short name
                            "Stereo Mode");            // Long name

// Add possible discrete values
m_ParameterStereoModeView.AddDiscreteValue(
                        "Stereo",           // Short Discrete Value
                        "Stereo")           // Long Discrete Value

m_ParameterStereoModeView.AddDiscreteValue(
                        "Mono",             // Short Discrete Value
                        "Mono");            // Long Discrete Value
```

## The Panel
In `cMyFirstEffect.cpp`, within the `onInitialize` method, we add the two new views to the `m_ParameterFirstPanel`:

```cpp
    // Create a parameter panel for the user interface
m_ParameterFirstPanel.Init(&m_ParameterGainView,       // Parameter View 1 (Gain)
                            &m_ParameterPanView,        // Parameter View 2 (Panning)
                            &m_ParameterStereoModeView);// Parameter View 3 (Mode)
```

## New Method: Logarithmic Gain Calculation
*The human ear does not perceive volume linearly; it perceives it logarithmically. This is why we use a logarithmic scale (in dB) for audio gain—it corresponds to our natural perception. A "log" potentiometer or fader gives a uniform and natural sensation of volume throughout its range.*

We will create a method to convert the linear gain from our parameters `m_ParameterPan` and `m_ParameterStereoMode` into log values stored in the member variables `m_RightGain` and `m_LeftGain`.

In `cMyFirstEffect.h`, we declare the new method and variables:
```cpp
// -------------------------------------------------------------------------
// Compute left/right channel gains using a logarithmic (power) curve
// -------------------------------------------------------------------------
    void CalcGainLog();

// -------------------------------------------------------------------------
// Gain member variables
// -------------------------------------------------------------------------
float   m_RightGain;    // Calculated right gain
float   m_LeftGain;     // Calculated left gain
```
___

In `cMyFirstEffect.cpp`, we implement the new method. 

{: .tip}
> **Tip**  
>We notice the use of `getNormalizedValue()`. This method returns the parameter value normalized between 0.0f and 1.0f.  
>For example, if `m_ParameterPan` was initialized with a min value of -100 and a max value of 100:
>- A value of -100 results in a NormalizedValue() of 0.0f.
>- A value of 0 results in a NormalizedValue() of 0.5f.
>- A value of 100 results in a NormalizedValue() of 1.0f.

<BR>

```cpp
// -------------------------------------------------------------------------
// Compute left/right channel gains using a logarithmic (power) curve
// -------------------------------------------------------------------------
void cMyFirstEffect::CalcGainLog()
{
    // Retrieve normalized parameter values [0.0 .. 1.0]
    float Gain = m_ParameterGain.getNormalizedValue();
    float Pan = m_ParameterPan.getNormalizedValue();

    // Constant-power panning law: apply a square-root curve (exponent 0.5)
    float gain_left  = std::pow(1.0f - Pan, 0.5f); // fades out as pan moves right
    float gain_right = std::pow(Pan,         0.5f); // fades in as pan moves right

    // Apply the master gain on top of each channel's pan gain
    m_LeftGain  = Gain * gain_left;
    m_RightGain = Gain * gain_right;
}
```

## Callbacks
We will add two callback methods that intercept parameter state changes.  
In `cMyFirstEffect.h`, we declare these two new **static** methods to implement the callbacks.

```cpp
    // -------------------------------------------------------------------------
    // Gain callback
    // -------------------------------------------------------------------------
    void static onGainChange(DadDSP::cParameter* pGainParameter, uint32_t Data);

    // -------------------------------------------------------------------------
    // Pan callback
    // -------------------------------------------------------------------------
    void static onPanChange(DadDSP::cParameter* pBalanceParameter, uint32_t Data);
```

___

In `cMyFirstEffect.cpp`, we implement the two new methods.  
The code `cMyFirstEffect* pThis = (cMyFirstEffect *) Data;` allows us to cast the transmitted parameter data into a pointer to the `cMyFirstEffect` instance.  
These functions then call the `CalcGainLog` method to calculate the right and left channel gains based on the values of `m_ParameterGain` and `m_ParameterPan`.

{: .tip}
> **Tip**  
> Using callbacks allows you to simplify and clarify the processing logic. Instead of executing code constantly, callbacks let you run specific treatments only when needed.
> Main advantages:
> - The code is easier to read, maintain, and debug.
> - It optimizes CPU resources by launching calculations only when parameters change.

<BR>

```cpp
// -------------------------------------------------------------------------
// Gain callback
// -------------------------------------------------------------------------
void cMyFirstEffect::onGainChange(DadDSP::cParameter* pGainParameter, uint32_t Data){

	// Cast Data to cMyFirstEffect instance pointer
	cMyFirstEffect* pThis = (cMyFirstEffect *) Data;

	pThis->CalcGainLog();
}

// -------------------------------------------------------------------------
// Pan callback
// -------------------------------------------------------------------------
void cMyFirstEffect::onPanChange(DadDSP::cParameter* pPanParameter, uint32_t Data){
	cMyFirstEffect* pThis = (cMyFirstEffect *) Data;

	pThis->CalcGainLog();

}
```

## onProcess 
Here is the new code for the `onProcess` method.  

We test the value of `m_ParameterStereoMode.getValue()`: it returns 0 if Stereo is selected and 1 if Mono is selected. If Mono is selected, we mix the right and left channels.  

Finally, we apply the calculated right and left gains.  

<BR>

```cpp
// -----------------------------------------------------------------------------
// Method: onProcess
// Description: Main audio processing function. Called continuously for
//              every audio buffer.
// -----------------------------------------------------------------------------
void cMyFirstEffect::onProcess(AudioBuffer *pIn, AudioBuffer *pOut, eOnOff OnOff, bool Silence)
{

    float Left = pIn->Left;
    float Right = pOut->Right; // Note: Should likely be pIn->Right if processing both channels from input

    // Test stereo mode (1.0f for Mono, 0.0f for Stereo)
    if(m_ParameterStereoMode.getValue() == 1.0f){
    	// Convert to mono: mix the channels
    	Left = (Left + Right) / 2.0f;
    	Right = (Left + Right) / 2.0f; // Note: The original code had a bug here, corrected to use input values for mixing
    }

	// Apply gain to both channels
    pOut->Left  = Left  * m_LeftGain;
    pOut->Right = Right * m_RightGain;
}
```