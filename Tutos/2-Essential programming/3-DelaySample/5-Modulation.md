---
title: How to Add Modulation
parent: Delay Effect
layout: default
nav_order: 5
---

# How to Add Modulation

{: .text-blue-300 }
> This chapter explains how to add modulation to our delay effect.  
> To achieve this, we will use a Low Frequency Oscillator (LFO) that continuously and subtly changes the delay time.

## Principle of Modulation in a Delay

One of the most interesting effects you can create with a delay is modulation.

The idea is simple: instead of keeping the delay time fixed, we continuously vary it over time.  
Changing the delay time also changes the pitch of the delayed sound.

For musicians familiar with analog equipment, this is exactly the same phenomenon that occurs when speeding up or slowing down a vinyl record or a magnetic tape:
* when the sound plays faster, the pitch becomes higher and brighter,
* when it plays slower, the pitch becomes lower and darker.

By continuously modulating the delay time, we naturally obtain effects such as:
* Vibrato
* Chorus

## The Code

The main component of the modulation effect is the `DadDSP::cDCO` digital oscillator, which generates a waveform used to modulate the delay time.

To control this oscillator, we will add two new parameters:
* modulation speed
* modulation depth

These parameters will be grouped inside a new panel **Modulation**.

### **In cMyFirstEffect.h**

First, include the declaration of the `DadDSP::cDCO` class.

```cpp
#include "cDCO.h"
````
As you probably guessed at this point in the tutorial, we are going to declare:

* two parameters,
* their associated GUI views,
* a new panel to contain them,
* and a callback function for the speed parameter.

```cpp
// -------------------------------------------------------------------------
// Modulation speed callback
// -------------------------------------------------------------------------
void static onModSpeedChange(DadDSP::cParameter* pModSpeedParameter, uint32_t Data);

...
...

DadGUI::cUIParameter                m_ModulationSpeed;      // Modulation speed parameter
DadGUI::cUIParameter                m_ModulationDepth;      // Modulation depth parameter

...
...

DadGUI::cParameterNumNormalView     m_ModulationSpeedView;  // Modulation speed view
DadGUI::cParameterNumNormalView     m_ModulationDepthView;  // Modulation depth view

...
...

DadGUI::cPanelOfParameterView       m_ModulationPanel;      // Modulation panel
```

Finally, we declare two DCOs in the member variables.

{: .tip}
> Normally a single DCO would be enough, but here we use two oscillators running at slightly different frequencies in order to create a stereo modulation effect.

<BR>

```cpp
// -------------------------------------------------------------------------
// Modulation DCO
// -------------------------------------------------------------------------
DadDSP::cDCO    m_DCOLeft;      // Left modulation oscillator
DadDSP::cDCO    m_DCORight;     // Right modulation oscillator
```

### **In cMyFirstEffect.cpp**

We begin by defining the minimum and maximum modulation frequencies.

```cpp
// Modulation
constexpr float MIN_MODULATION_SPEED = 0.25f;
constexpr float MAX_MODULATION_SPEED = 8.0f;
```

**Inside onInitialize()**

We initialize:

* the new parameters,
* their associated GUI views,
* and the modulation panel.

Then we add the panel to the menu.

```cpp
// Initialize the modulation speed parameter
m_ModulationSpeed.Init(MY_FIRST_EFFECT_ID,      // Serialize ID
                        2.5f,                   // Default value
                        MIN_MODULATION_SPEED,   // Minimum value
                        MAX_MODULATION_SPEED,   // Maximum value
                        0.5f,                   // Fast increment
                        0.1f,                   // Slow increment
                        onModSpeedChange,       // Callback function
                        (uint32_t)this,         // Callback user data
                        1.0f,                   // Transition time (seconds)
                        28);                    // MIDI CC number

// Initialize the modulation depth parameter
m_ModulationDepth.Init(MY_FIRST_EFFECT_ID,      // Serialize ID
                        40.0f,                  // Default value
                        0.0f,                   // Minimum value
                        100.0f,                 // Maximum value
                        5.0f,                   // Fast increment
                        1.0f,                   // Slow increment
                        nullptr,                // Callback function
                        0,                      // Callback user data
                        1.0f,                   // Transition time (seconds)
                        29);                    // MIDI CC number

...
...

// Initialize the GUI view for the parameter
m_ModulationSpeedView.Init(&m_ModulationSpeed,  // Linked parameter
                            "Speed",            // Short name
                            "Speed",            // Long name
                            "Hz",               // Unit (short)
                            "Hz");              // Unit (long)

// Initialize the GUI view for the parameter
m_ModulationDepthView.Init(&m_ModulationDeep,    // Linked parameter
                            "Depth",            // Short name
                            "Depth",            // Long name
                            "%",                // Unit (short)
                            "percent");         // Unit (long)

...
...

m_ModulationPanel.Init(&m_ModulationDepthView,   // Parameter View 1
                        nullptr,                // Parameter View 2
                        &m_ModulationSpeedView);// Parameter View 3

...
...

m_Menu.addMenuItem(&m_ModulationPanel, "Mod.");
```

Finally, we initialize the two DCOs.

```cpp
// Initialize LFOs
m_DCOLeft.Initialize(SAMPLING_RATE,
                        0.5f,
                        MIN_MODULATION_SPEED,
                        MAX_MODULATION_SPEED,
                        0.5f);

m_DCORight.Initialize(SAMPLING_RATE,
                        0.5f,
                        MIN_MODULATION_SPEED,
                        MAX_MODULATION_SPEED,
                        0.5f);
```

**Inside onProcess()**

On fait avancer d'un pas les oscilateur basse frequence.
```cpp
    // DCO step
    m_DCOLeft.Step();
    m_DCORight.Step();
```

We apply the modulation by continuously changing the delay time according to the oscillator values.

```cpp
// Convert delay time to samples
float DelaySample = m_DelayTime * SAMPLING_RATE;

// Slew rate limitation of the delay time in number of samples
float Delta = DelaySample - m_PrevDelaySample;

if (Delta > MAX_CHANGE_DELAY_SAMPLE) {
    DelaySample = m_PrevDelaySample + MAX_CHANGE_DELAY_SAMPLE;
} else if (Delta < -MAX_CHANGE_DELAY_SAMPLE) {
    DelaySample = m_PrevDelaySample - MAX_CHANGE_DELAY_SAMPLE;
}
m_PrevDelaySample = DelaySample;

// Apply modulation
float DelayL = DelaySample - (m_DCOLeft.getTriangleValue() * m_ModulationDeep);
float DelayR = DelaySample - (m_DCORight.getTriangleValue() * m_ModulationDeep);

// Read delayed samples
float DelayLeftOut = m_LeftDelayLine.Pull(DelayL);
float DelayRightOut = m_RightDelayLine.Pull(DelayR);
```

Because the delay time constantly changes, the pitch of the delayed signal also changes slightly, creating the modulation effect.

Using two oscillators with slightly different frequencies produces a wider and more natural stereo image.

## Callback

Each time the `ModulationSpeed` parameter changes, we update the frequency of both DCOs.

```cpp
// -------------------------------------------------------------------------
// Modulation speed callback
// -------------------------------------------------------------------------
void cMyFirstEffect::onModSpeedChange(DadDSP::cParameter* pModSpeedParameter, uint32_t Data){

    cMyFirstEffect* pThis = (cMyFirstEffect*)Data;

    float DCOSpeed = pModSpeedParameter->getValue();

    pThis->m_DCOLeft.setFreq(DCOSpeed);
    pThis->m_DCORight.setFreq(DCOSpeed + 0.02f);
}
```

The right oscillator uses a slightly different frequency (+0.02 Hz) in order to create subtle stereo movement.

## Result

You now have a modulated stereo delay capable of producing vibrato and chorus effects.
This modulation can notably emulate an old tape delay with a slightly unstable tape speed.  

By adjusting the modulation speed and depth, you can achieve anything from very subtle analog movement to strong psychedelic modulation effects.
