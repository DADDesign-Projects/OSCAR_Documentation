---
title: How to add a Tap Tempo
parent: Delay Effect
layout: default
nav_order: 3
---

# How to add a Tap Tempo

{: .text-blue-300 }
> This chapter explains how to implement a tap tempo function in your effect.

## Tap Tempo

The **tap tempo** feature allows the user to adjust a parameter of an effect by simply tapping a footswitch with their foot, instead of manually turning a knob (although adjusting the knob remains possible).

Instead of calculating precise values, the musician can intuitively set the timing by tapping at the desired speed. The system measures the time between taps and automatically applies that tempo to the chosen parameter.

In the **FORGE** ecosystem, tap tempo is managed by the `DadGUI::cTapTempoMemChange` class. In our example, this functionality is already available through the `DadEffect::cEffectBase` class, from which our effect inherits.

### How to enable Tap Tempo

To activate tap tempo, you need to specify two things:

- **Which parameter** should be controlled by the tap tempo.
- **The operating mode**:
  - `DadGUI::eTempoType::period` → Sets the tap tempo as a **duration** in milliseconds (most common for delays).
  - `DadGUI::eTempoType::frequency` → Sets the tap tempo as a **frequency** in Hertz.
  - `DadGUI::eTempoType::none` → Disables tap tempo control.

## The Code
### **In cMyFirstEffect.cpp**
We will now modify the previous example to add tap tempo functionality.

**inside the onInitialize()**

Link the parameter to the tap tempo system by adding the following lines:

```cpp
// Initialize Tap Tempo
m_pTapTempoParameter = &m_DelayTime;
m_TempoType = DadGUI::eTempoType::period;
```

## Necessary improvements
At this stage, we have a functional delay, but it is not yet usable in real conditions.  
As you have probably noticed, crackling noises appear when changing the delay time, whether using the encoder or the tap tempo.  
**Explanation of the phenomenon:**
A sudden change in delay length causes a discontinuity in the buffer, which generates audio artefacts (clicks and crackles).

**We have two solutions to eliminate these crackles:**

  **1. Limit the rate of change** of the delay (in samples)
The DelaySample value is already limited, but it is still not sufficient.
This solution is acceptable if we can tolerate a temporary pitch shift (upwards or downwards) while the delay time is changing. 

  **2. Perform a crossfade** (fade out / fade in)
When changing the delay time, we apply a smooth crossfade between the old and the new delay. This method allows a completely transparent transition, without any crackling or pitch alteration.

## Limiting the Rate of Change of DelaySample

The second solution is more robust, but its implementation is significantly more complex and goes beyond the scope of this tutorial. We will therefore go with the simpler approach: **limiting the rate of change of DelaySample**.

The idea is straightforward — instead of allowing the delay to jump instantly to its new value, we constrain how much it can change per audio sample. This is known as a **slew rate limiter**.

### In cMyFirstEffect.h

Declare a new member variable to store the delay value applied during the previous sample:

```cpp
float m_PrevDelaySample;
```

### In cMyFirstEffect.cpp

At the top of the file, declare a constant defining the maximum allowed variation of the delay (in samples) per process cycle:

```cpp
constexpr float MAX_CHANGE_DELAY_SAMPLE = 0.50f;  // Maximum variation in the number of sample delays
```

**Inside onInitialize()**

Initialize `m_PrevDelaySample` with the delay time converted to samples, so the slew limiter starts from a coherent state:

```cpp
// Initialize PrevDelaySample
m_PrevDelaySample = m_DelayTime.getTargetValue() * SAMPLING_RATE;
```

**Inside onProcess()**

We add a slew rate limiting algorithm to smooth out sudden changes in the delay value. The principle is simple:
- Compute the **delta** between the new `DelaySample` and the value from the previous process cycle.
- If the delta exceeds `MAX_CHANGE_DELAY_SAMPLE` (in either direction), clamp the variation to that maximum.
- Store the result back into `m_PrevDelaySample` for the next cycle.

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

// Reading the delayed line with a DelaySample delay
float DelayLeftOut  = m_LeftDelayLine.Pull(DelaySample);
float DelayRightOut = m_RightDelayLine.Pull(DelaySample);
```

> **Note:** The value `0.50f` means the delay can shift by at most half a sample per audio sample processed. This creates a very smooth transition and effectively eliminates pitch artifacts when the delay time is modulated.


## Result
Your effect now has a fully functional tap tempo! The user can tap the footswitch 1 at the desired speed, and the delay time will automatically adjust in real time.