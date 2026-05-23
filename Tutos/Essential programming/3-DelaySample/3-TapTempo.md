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

In the **OSCAR/FORGE** ecosystem, tap tempo is managed by the `DadGUI::cTapTempoMemChange` class. In our example, this functionality is already available through the `DadEffect::cEffectBase` class, from which our effect inherits.

### How to enable Tap Tempo

To activate tap tempo, you need to specify two things:

- **Which parameter** should be controlled by the tap tempo.
- **The operating mode**:
  - `DadGUI::eTempoType::period` → Sets the tap tempo as a **duration** in milliseconds (most common for delays).
  - `DadGUI::eTempoType::frequency` → Sets the tap tempo as a **frequency** in Hertz.
  - `DadGUI::eTempoType::none` → Disables tap tempo control.

## Code Example

We will now modify the previous example to add tap tempo functionality.

**In cMyFirstEffect.cpp**, inside the `onInitialize()` method:

First, update the **Time** parameter and its GUI view to work in milliseconds:

```cpp
// Initialize the Time parameter
m_DelayTime.Init(MY_FIRST_EFFECT_ID,    // Serialize ID
                 350.0f,                // Default value 350ms
                 DELAY_MIN_TIME,        // Minimum value 100ms
                 DELAY_MAX_TIME,        // Maximum value 2s
                 15000.0f,              // Fast increment 150ms
                 0.05f,                 // Slow increment 50µs
                 nullptr,               // Callback function
                 0,                     // Callback user data
                 2.0f,                  // Transition time (seconds)
                 23);                   // MIDI CC number

// Initialize the GUI view for the parameter
m_DelayTimeView.Init(&m_DelayTime,      // Linked parameter
                     "Time",            // Short name
                     "Time",            // Long name
                     "ms",              // Unit (short)
                     "Milliseconds");   // Unit (long)
```

Then, link the parameter to the tap tempo system by adding the following lines:

```cpp
// Initialize Tap Tempo
m_pTapTempoParameter = &m_DelayTime;
m_TempoType = DadGUI::eTempoType::period;
```
<BR>

{: .highlight}
> **Result:**
> Your effect now has a fully functional tap tempo! The user can tap the footswitch 1 at the desired speed, and the delay time will automatically adjust in real time.