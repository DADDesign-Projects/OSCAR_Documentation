---
title: Basic Delay
parent: Delay Effect
layout: default
nav_order: 1
---

# Basic Delay

{: .text-blue-300 }
> In this tutorial, we will extend the code from the previous tutorial [Working With Parameters](../2-Parameters/0-Parameter.html) to create a simple stereo delay effect.

## New Parameters

We first add three new parameters and their associated GUI views:

* `m_DelayTime` / `m_DelayTimeView`  
  Controls the delay time, which defines the spacing between the original sound and each echo repetition.

* `m_DelayFeedback` / `m_DelayFeedbackView`  
  Controls the amount of feedback, and therefore the number of echo repetitions.

* `m_DelayMix` / `m_DelayMixView`  
  Controls the balance between the original (*Dry*) signal and the processed (*Wet*) delayed signal.

In `cMyFirstEffect.h`, declare the new parameters:

```cpp
DadGUI::cUIParameter                m_DelayTime;        // Time parameter
DadGUI::cUIParameter                m_DelayFeedback;    // Feedback parameter
DadGUI::cUIParameter                m_DelayMix;         // Mix parameter
...
...
DadGUI::cParameterNumNormalView     m_DelayTimeView;        // GUI view for Time parameter
DadGUI::cParameterNumNormalView     m_DelayFeedbackView;    // GUI view for Feedback parameter
DadGUI::cParameterNumNormalView     m_DelayMixView;         // GUI view for Mix parameter
```

In `cMyFirstEffect.cpp`, define the minimum and maximum delay times:

```cpp
// Delay
constexpr float DELAY_MAX_TIME = 2.0f;  // Maximum delay time in seconds
constexpr float DELAY_MIN_TIME = 0.1f;  // Minimum delay time in seconds
```

Then initialize the parameters and their GUI views inside onInitialize():
```cpp
    // Initialize the Time parameter
    m_DelayTime.Init(MY_FIRST_EFFECT_ID,    // Serialize ID
                         0.35f,             // Default value 350ms
                         DELAY_MIN_TIME,    // Minimum value 100ms
                         DELAY_MAX_TIME,    // Maximum value 2 s
                         0.15f,             // Fast increment 150ms
                         0.05f,             // Slow increment 50ms
                         nullptr,           // Callback function
                         0,                 // Callback user data
                         2.0f,              // Transition time (seconds)
                         23);               // MIDI CC number

    // Initialize the Feedback parameter
    m_DelayFeedback.Init(MY_FIRST_EFFECT_ID,// Serialize ID
                         35.0f,             // Default value
                         0.0f,              // Minimum value
                         100.0f,            // Maximum value
                         10.0f,             // Fast increment 150ms
                         1.0f,              // Slow increment 50ms
                         nullptr,           // Callback function
                         0,                 // Callback user data
                         1.0f,              // Transition time (seconds)
                         24);               // MIDI CC number

    // Initialize the Mix parameter
    m_DelayMix.Init(MY_FIRST_EFFECT_ID,     // Serialize ID
                         45.0f,             // Default value
                         0.0f,              // Minimum value
                         100.0f,            // Maximum value
                         10.0f,             // Fast increment 150ms
                         1.0f,              // Slow increment 50ms
                         nullptr,           // Callback function
                         0,                 // Callback user data
                         1.0f,              // Transition time (seconds)
                         25);               // MIDI CC number

    // Initialize the GUI view for the parameter
    m_DelayTimeView.Init(& m_DelayTime,     // Linked parameter
                             "Time",        // Short name
                             "Time",        // Long name
                             "s",           // Unit (short)
                             "second");     // Unit (long)

    // Initialize the GUI view for the parameter
    m_DelayFeedbackView.Init(&m_DelayFeedback,  // Linked parameter
                             "Feed.",       // Short name
                             "Feedback",    // Long name
                             "%",           // Unit (short)
                             "percent");    // Unit (long)

    // Initialize the GUI view for the parameter
    m_DelayMixView.Init(&m_DelayMix,        // Linked parameter
                             "Mix",         // Short name
                             "Mix",         // Long name
                             "%",           // Unit (short)
                             "percent");    // Unit (long)
```

## New Panel

To group these parameters together, we create a dedicated Delay panel.

In cMyFirstEffect.h:

```cpp
DadGUI::cPanelOfParameterView   m_DelayPanel;   // Delay panel
```

In cMyFirstEffect.cpp, inside onInitialize initialize the panel and add it to the menu:

```cpp
m_DelayPanel.Init(  &m_DelayTimeView,             // Parameter View 1
                    &m_DelayFeedbackView,         // Parameter View 2
                    &m_DelayMixView);             // Parameter View 3

// Add the panel to the effect's menu
m_Menu.addMenuItem(&m_DelayPanel, "Delay");
m_Menu.addMenuItem(&m_ParameterFirstPanel, "First");
```

## Delay Lines

To create the echo effect, we use the DadDSP::cDelayLine class from the Forge DadDSP library.
Its declaration can be found in cDelayLine.h.

Because the effect processes stereo audio, two delay lines are required:
one for the left channel and one for the right channel.

In cMyFirstEffect.h, include the class definition:
```cpp
#include "cDelayLine.h"
```
Then declare the two delay lines:

```cpp
// -------------------------------------------------------------------------
// Delay lines
// -------------------------------------------------------------------------
DadDSP::cDelayLine  m_LeftDelayLine;    // Left delay line
DadDSP::cDelayLine  m_RightDelayLine;   // Right delay line
```

## Delay Buffers

A delay line requires memory buffers to store audio samples.

The buffer size depends on:
* the maximum delay time
* the audio sampling rate

We allocate these buffers in SDRAM using the SDRAM_SECTION attribute.
This is important because SDRAM provides much more memory space than the STM32 internal RAM.

```cpp
// Calculate buffer size based on sampling rate and max delay time
constexpr uint32_t DELAY_BUFFER_SIZE =
    static_cast<uint32_t>((SAMPLING_RATE * DELAY_MAX_TIME) + 0.999f);

// Allocate delay buffers in SDRAM
SDRAM_SECTION float __DelayBufferLeft[DELAY_BUFFER_SIZE + 100];
SDRAM_SECTION float __DelayBufferRight[DELAY_BUFFER_SIZE + 100];
```
<BR>

{: .tip}
>**SAMPLING_RATE
>Defines the audio sampling rate in Hz.
>By default, Forge uses 48000 Hz.
>
>**Memory Sections
>Sections allow data or code to be placed into specific memory regions.
>The available memory sections are defined in Sections.h.

<BR>
Initialize the delay lines inside onInitialize():

```cpp
// Delay lines initialization
m_LeftDelayLine.Initialize(__DelayBufferLeft, DELAY_BUFFER_SIZE);
m_RightDelayLine.Initialize(__DelayBufferRight, DELAY_BUFFER_SIZE);
```

## Audio Processing

Inside onProcess(), we first read the delayed signal from the delay lines:

```cpp
    // Convert delay time to samples
    float DelaySample = m_DelayTime * SAMPLING_RATE;

    // Reading the delayed line with a DelaySample delay
    float DelayLeftOut = m_LeftDelayLine.Pull(DelaySample);
    float DelayRightOut = m_RightDelayLine.Pull(DelaySample);
```
<BR>

{: .tip}
> cParameter (and therefore cUIParameter) overloads the float cast operator.
> This means you can write either:  
> * m_DelayTime.getValue()  
> or simply  
> * m_DelayTime    
> when no ambiguity exists.

At each audio sample, we mix the current input signal with the delayed output.
This result is then re-injected into the delay line after being attenuated by the feedback parameter.

The higher the feedback value, the more echo repetitions are produced.

When the effect is bypassed, the input signal is no longer injected into the delay line.
However, the existing delayed signal continues to play normally, preserving the natural echo tail and avoiding abrupt audio cuts.

```cpp
// Retrieve the Feedback parameter value and scale it (multiplied by 0.008f)
// to get a suitable attenuation factor and prevent the delay line from
// saturating or exploding too quickly
float FeedBack = m_DelayFeedback.getValue() * 0.008f;

float RightFeedbackInput;
float LeftFeedbackInput;
// Compute the feedback signal depending on the effect state
if (OnOff == eOnOff::On){
    // Effect ON → Loop back the input signal + echoes read from the delay line
    RightFeedbackInput = Right + DelayRightOut;
    LeftFeedbackInput  = Left  + DelayLeftOut;
}else{
    // Effect OFF → Loop back only the echoes for a natural decay
    RightFeedbackInput = DelayRightOut;
    LeftFeedbackInput  = DelayLeftOut;
}

// Inject the feedback back into the delay lines with the user-defined attenuation
m_RightDelayLine.Push(RightFeedbackInput * FeedBack);
m_LeftDelayLine.Push(LeftFeedbackInput  * FeedBack);
```
## Dry / Wet Mix

Finally, we mix the original (Dry) signal with the delayed (Wet) signal.

The implementation below uses a simple linear mix.
While easy to understand, this approach is not always ideal from a perceptual point of view, because the overall volume may appear to change during the mix transition.

>Later, we will see how to apply more natural audio taper curves to obtain smoother and more musical Dry/Wet behavior.

```cpp
float MixDry = 1.0f - (m_DelayMix * 0.01f);
float MixWet = m_DelayMix * 0.01f;

pOut->Left  = ((DelayLeftOut * MixWet) + (Left * MixDry)) * m_LeftGain;
pOut->Right = ((DelayRightOut * MixWet) + (Right * MixDry)) * m_RightGain;
```
