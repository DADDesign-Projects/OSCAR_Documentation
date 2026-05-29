---
title: How to mix Dry/Wet
parent: Delay Effect
layout: default
nav_order: 2
---

# How to mix Dry/Wet

{: .text-blue-300 }
>This chapter details the Dry/Wet principles as implemented within the OSCAR ecosystem and demonstrates how to practically achieve a Dry/Wet mix.

## The Foundation of Dry/Wet Signal Processing in OSCAR
The construction of any sound effect relies on the synergistic blending between the untreated source signal (**Dry**) and the effect-modified signal (**Wet**).  

Within the OSCAR architecture, this processing is entirely realized in analog to achieve optimal sonic quality. The Dry channel remains purely analog; it does not pass through the complete digital processing loop (Analog-to-Digital conversion (A/D), DSP processing, and Digital-to-Analog reconversion (D/A)).  

This principle guarantees not only **zero latency** for the dry signal, but also ensures **perfect sonic integrity**.

## __DryWet object
The gain of the dry channel (the untreated original signal) is adjusted using an electronic potentiometer.  
To simplify usage, this technical aspect is entirely managed by the global object `__DryWet`. This object controls the digital potentiometer and automatically calculates the wet gain in a transparent manner, hiding all the complexity involved in maintaining a consistent psychoacoustic level between the dry and wet signals..

![](DryWetDiag.png)

## The code 

We are going to change the behavior of the `m_DelayMix` parameter.  
First, we add a callback to intercept any changes to this parameter.  
**In cMyFirstEffect.h**  
Add the callback declaration:  

```cpp
    // -------------------------------------------------------------------------
    // Mix callback
    // -------------------------------------------------------------------------
    void static onMixChange(DadDSP::cParameter* pMixParameter, uint32_t Data);
```

**In cMyFirstEffect.cpp**  
In the `onInitialize()` methode, update the initialization of the `m_DelayMix` parameter to include the callback:  

```cpp
    // Initialize the Mix parameter
    m_DelayMix.Init(MY_FIRST_EFFECT_ID,     // Serialize ID
                         50.0f,             // Default value
                         0.0f,              // Minimum value
                         100.0f,            // Maximum value
                         10.0f,             // Fast increment
                         1.0f,              // Slow increment
                         onMixChange,       // Callback function
                         (uint32_t)this,    // Callback user data
                         1.0f,              // Transition time (seconds)
                         25);               // MIDI CC number
```

At the end of the file, implement the callback. It updates the Dry/Wet mix ratio using the global `__DryWet` object:   

```cpp
// -------------------------------------------------------------------------
// Mix callback
// -------------------------------------------------------------------------
void cMyFirstEffect::onMixChange(DadDSP::cParameter* pMixParameter, uint32_t Data){
    __DryWet.setMix(pMixParameter->getValue());
}
```
In the `onProcess()` function, replace the previous mixing code with the following:
We ask the `__DryWet` object for the gain to apply to the Wet channel.  
The Dry channel is no longer processed manually, __DryWet now fully handles the mixing.

> The factor 1.25f compensates for the gain reduction introduced by the feedback loop.
> The feedback is calculated as: m_DelayFeedback.getValue() * 0.008f,
> which represents an attenuation to 0.8% of full scale.
> To maintain consistent overall loudness and prevent a noticeable loss of level
> on the Wet signal, we apply a compensatory gain of 1.25 (equivalent to +1.94 dB)

```cpp
// Apply wet gain to the outputs. The 1.25f factor compensates for the minimum
// feedback attenuation (0.008f)
float GainWet = __DryWet.getGainWet();
pOut->Left  = DelayLeftOut * GainWet  * m_LeftGain * 1.25f;
pOut->Right = DelayRightOut * GainWet * m_RightGain * 1.25f;
```
