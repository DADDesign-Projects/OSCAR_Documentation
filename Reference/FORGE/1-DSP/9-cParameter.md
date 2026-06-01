---
title: cParameter
parent: DSP
layout: default
nav_order: 9
---
# DadDSP::cParameter

**Namespace:** DadDSP::cParameter  
**Files:** cParameter.h / cParameter.cpp   
**Directory:** DAD_FORGE/DSP   
**Description:** Parameter manager with smoothing, normalization, and MIDI control for reactive and smooth audio user interfaces.

---

## 📋 Class Description

The `cParameter` class is a generic parameter manager designed for real-time audio applications. It provides essential features for controlling effects, synthesizers, and other DSP modules with:

- **Value Smoothing:** Smooth transition between current and target values via a slope factor
- **Automatic Normalization:** Bidirectional conversion between absolute and normalized [0.0-1.0] values
- **MIDI Control:** Native integration with the CC (Control Change) protocol for music programming
- **Change Detection:** "Dirty" flag to track modifications
- **Inverted Ranges:** Support for inverted min/max ranges for specific logical controls

**Typical Use Cases:**
- Effect parameter control (volume, cutoff, resonance, etc.)
- Graphical or touch user interface
- MIDI control from keyboard/piano
- Parameter automation in sequencers

---

## 🎯 Enumerations and Associated Structures

No associated enumerations or structures.

---

## 📚 Public Methods

### **~cParameter** (Virtual Destructor)

| Element | Details |
|:---|:---|
| **Method** | `virtual ~cParameter()` |
| **Description** | Virtual destructor that allows proper destruction of objects inherited from this class. The destructor is empty (no special cleanup required). Virtual allows polymorphism for potential derived classes. |
| **Parameter(s)** | None |
| **Return** | None (void) |

---

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init(float InitValue, float Min, float Max, float RapidIncrement, float SlowIncrement, CallbackType Callback = nullptr, uint32_t CallbackUserData = 0, float Slope = 0, uint8_t Control = 0xFF)` |
| **Description** | Fully initializes the parameter with all its configuration attributes. Sets min/max bounds, fast/slow increments, optional callback, slope factor, and registers the MIDI control if specified. The initial value is clamped within valid bounds. |
| **Parameter(s)** | |
| `InitValue` | Initial parameter value. Will be automatically clamped to respect min/max bounds. |
| `Min` | Minimum parameter value. Defines the lower bound of the valid range. |
| `Max` | Maximum parameter value. Defines the upper bound of the valid range. |
| `RapidIncrement` | Fast increment step size (e.g., 0.1). Used for fast manual adjustments via `Increment()`. |
| `SlowIncrement` | Slow increment step size (e.g., 0.01). Used for fine adjustments or automatic control. |
| `Callback` | Pointer to a callback function called when the value changes. Signature: `void(*)(cParameter*, uint32_t)`. Can be nullptr to disable. |
| `CallbackUserData` | User data passed to the callback (generally an index or identifier). |
| `Slope` | Smoothing factor. Determines the speed of transition toward the target value. Gives the value in seconds to go from Min to Max value. |
| `Control` | MIDI control number (CC number, 0-127). Value 0xFF disables MIDI control. The MIDI callback will be registered if a valid value is provided. |
| **Return** | None (void) |

---

### **Increment**

| Element | Details |
|:---|:---|
| **Method** | `void Increment(int32_t nbStep, bool Switch)` |
| **Description** | Increments or decrements the parameter's target value by a specified number of steps. The choice between fast or slow increment depends on the `Switch` flag. The current value will then smooth toward this new target via the slope factor. Useful for manual controls (knobs, buttons) or discrete automation. |
| **Parameter(s)** | |
| `nbStep` | Number of steps to increment (positive to increase, negative to decrease). |
| `Switch` | Speed mode: `false` = fast increment (`RapidIncrement`), `true` = slow increment (`SlowIncrement`). |
| **Return** | None (void) |

---

### **getValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getValue() const` |
| **Description** | Returns the current smoothed value of the parameter. This value represents the actual parameter state after smoothing. Different from `getTargetValue()` which contains the not-yet-smoothed target value. |
| **Parameter(s)** | None |
| **Return** | Current float value of the parameter (within [Min, Max] range) |

---

### **setValue**

| Element | Details |
|:---|:---|
| **Method** | `void setValue(float value)` |
| **Description** | Directly sets the parameter's target value with bounds checking. The value is automatically clamped to stay within [Min, Max]. Note: this method does not immediately update the current value - smoothing occurs on the next call to `Process()`. The dirty flag is marked as true. |
| **Parameter(s)** | |
| `value` | New target value for the parameter. Will be clamped to min/max bounds if out of range. |
| **Return** | None (void) |

---

### **getTargetValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getTargetValue() const` |
| **Description** | Returns the parameter's target value. This is the value toward which the current value (`getValue()`) is progressively smoothing. Useful for knowing the next expected value or for predictive calculations. |
| **Parameter(s)** | None |
| **Return** | Target float value of the parameter (within [Min, Max] range) |

---

### **operator=(float)**

| Element | Details |
|:---|:---|
| **Method** | `inline cParameter& operator=(float value)` |
| **Description** | Assignment operator overload for concise syntax. Allows writing `param = 0.5f` instead of `param.setValue(0.5f)`. The value is automatically clamped to valid bounds. Returns a reference to the object to allow chaining. |
| **Parameter(s)** | |
| `value` | Target value to assign to the parameter. Will be clamped to min/max bounds. |
| **Return** | Reference to the `cParameter` object for chaining |

---

### **operator float**

| Element | Details |
|:---|:---|
| **Method** | `inline operator float() const` |
| **Description** | Implicit type conversion to float. Allows using a parameter directly as a float value in mathematical expressions or functions. Returns the current smoothed value (`getValue()`). |
| **Parameter(s)** | None |
| **Return** | Current float value of the parameter (smoothed value) |

---

### **getNormalizedValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getNormalizedValue() const` |
| **Description** | Returns the current parameter value normalized to the interval [0.0, 1.0]. Useful for graphical user interfaces (progress bars), normalized slider control, or when a standardized range is required regardless of the parameter's absolute bounds. Returns 0 if Min == Max to avoid division by zero. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 (Min) and 1.0 (Max) representing the current position |

---

### **setNormalizedValue**

| Element | Details |
|:---|:---|
| **Method** | `inline void setNormalizedValue(float normalizedValue)` |
| **Description** | Sets the parameter value from a normalized value between 0.0 and 1.0. The value is clamped to [0.0, 1.0] before conversion. Useful for graphical interfaces using sliders or normalized controllers. The dirty flag is marked as true after conversion. |
| **Parameter(s)** | |
| `normalizedValue` | Normalized value between 0.0 and 1.0. Will be automatically clamped if out of range. |
| **Return** | None (void) |

---

### **getNormalizedTargetValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getNormalizedTargetValue() const` |
| **Description** | Returns the parameter's target value normalized to the interval [0.0, 1.0]. Useful for displaying visual indicators of progress toward a target, or for normalized controllers that need to follow a target value during smoothing. Returns 0 if Min == Max. |
| **Parameter(s)** | None |
| **Return** | Float value between 0.0 and 1.0 representing the target position |

---

### **setMaxValue**

| Element | Details |
|:---|:---|
| **Method** | `inline void setMaxValue(float MaxValue)` |
| **Description** | Modifies the parameter's maximum value at runtime. Automatically recalculates the step size (`m_Step`) based on the new maximum and the slope factor. Useful for dynamic adjustments or range changes during use. |
| **Parameter(s)** | |
| `MaxValue` | New maximum parameter value (must be >= Min). |
| **Return** | None (void) |

---

### **getMaxValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getMaxValue()` |
| **Description** | Returns the current maximum value of the parameter. Useful for checking valid bounds or displaying information in a user interface. |
| **Parameter(s)** | None |
| **Return** | Float value representing the current maximum |

---

### **setMinValue**

| Element | Details |
|:---|:---|
| **Method** | `inline void setMinValue(float MinValue)` |
| **Description** | Modifies the parameter's minimum value at runtime. Automatically recalculates the step size (`m_Step`) based on the new minimum and the slope factor. Useful for dynamic adjustments or range changes during use. |
| **Parameter(s)** | |
| `MinValue` | New minimum parameter value (must be <= Max). |
| **Return** | None (void) |

---

### **getMinValue**

| Element | Details |
|:---|:---|
| **Method** | `inline float getMinValue()` |
| **Description** | Returns the current minimum value of the parameter. Useful for checking valid bounds or displaying information in a user interface. |
| **Parameter(s)** | None |
| **Return** | Float value representing the current minimum |

---

### **Process**

| Element | Details |
|:---|:---|
| **Method** | `bool Process()` |
| **Description** | Main processing method that applies smoothing between the current value and the target value. Progressively advances `m_Value` toward `m_TargetValue` according to the slope factor. Returns `true` if an update was performed (value changed), `false` otherwise (already at target). Must be called regularly to maintain smooth smoothing. |
| **Parameter(s)** | None |
| **Return** | `true` if the value was updated, `false` if already at target |

---

### **MIDIControlChangeCallBack**

| Element | Details |
|:---|:---|
| **Method** | `static void MIDIControlChangeCallBack(uint8_t control, uint8_t value, uint32_t userData)` |
| **Description** | Static callback function called upon receipt of a MIDI Control Change message. Converts the MIDI value (0-127) to a normalized parameter value, takes into account potentially inverted min/max ranges, and marks the parameter as modified. The userData contains a pointer to the `cParameter` instance which will be cast to call `setValue()`. |
| **Parameter(s)** | |
| `control` | MIDI control number (CC number) - generally unused in this callback |
| `value` | Received MIDI value (0-127). Will be clamped to 127 if greater. |
| `userData` | User data containing a pointer to the `cParameter` instance. |
| **Return** | None (void) |

---

## 🔒 Protected / Private Members

| Member | Type | Description |
|:---|:---|:---|
| `m_Step` | `float` | Calculated step size for smoothing. Determined by: `step = (Max - Min) / Slope`. If Slope == 0, then `step = Max - Min`. Used in `Process()` to progressively advance toward the target. |
| `m_Min` | `float` | Minimum parameter value (default: 0.0). Defines the lower bound of the valid range. Used for clamping and normalization calculations. |
| `m_Max` | `float` | Maximum parameter value (default: 1.0). Defines the upper bound of the valid range. Used for clamping and normalization calculations. |
| `m_RapidIncrement` | `float` | Fast increment step size (default: 0.1). Used by `Increment()` with `Switch=false` for fast manual adjustments. |
| `m_SlowIncrement` | `float` | Slow increment step size (default: 0.01). Used by `Increment()` with `Switch=true` for fine or automatic adjustments. |
| `m_Value` | `float` | Current smoothed parameter value (default: 0.0). Represents the actual state after smoothing. Progressively moves toward `m_TargetValue`. |
| `m_TargetValue` | `float` | Parameter target value. Destination point toward which `m_Value` progressively smooths. Updated by `setValue()`, `Increment()`, and the MIDI callback. |
| `m_Slope` | `float` | Smoothing factor. Controls the speed of transition toward the target. Gives the time in seconds to go from m_Min to m_Max value. |
| `m_Callback` | `CallbackType` | Pointer to callback function (default: nullptr). Called during each value update via `Process()`. Signature: `void(cParameter*, uint32_t)`. Allows executing custom code when a parameter changes. |
| `m_CallbackUserData` | `uint32_t` | User data passed to the callback (default: 0). Typically used to store an index, identifier, or other context needed when executing the callback. |
| `m_Dirty` | `bool` | Dirty flag (default: false). Set to `true` when a change is requested via `setValue()`, `Increment()`, or the MIDI callback. |

---

## 💡 Usage Example

```cpp
#include "DadDSP/cParameter.h"

using namespace DadDSP;

// Custom callback function
void OnParameterChange(cParameter* param, uint32_t userData) {
    // This function is called on every value change
    printf("Parameter changed! Current value: %f\n", param->getValue());
    
    // Example: Use userData to identify which parameter changed
    if (userData == 1) {
        // This is the "volume" parameter that changed
        // Update display, etc.
    }
}

int main() {
    // --- Scenario 1: Volume parameter with MIDI control ---
    
    cParameter volumeParam;
    
    // Initialize a volume parameter (0-1) with MIDI control CC#71
    volumeParam.Init(
        0.5f,           // Initial value: 50%
        0.0f,           // Min: silence
        1.0f,           // Max: full volume
        0.1f,           // Fast increment (knob)
        0.01f,          // Slow increment (automation)
        OnParameterChange,  // Custom callback
        0,              // UserData (parameter index)
        0.5f,           // Smoothing at 500ms to go from min to max
        71              // MIDI control CC#71
    );
    
    float currentVolume = volumeParam;  // Implicit conversion
    
    // Main audio loop (called regularly)
    while(true) {
        // Process parameter smoothing
        bool changed = volumeParam.Process();
        
        // Apply the value to the audio module
        applyVolume(currentVolume);
        
        // ... other audio processing ...
    }
    
    // --- Scenario 2: Cutoff parameter with inverted ranges ---
    
    cParameter cutoffParam;
    
    // Initialize a low-pass filter (high frequencies on left)
    cutoffParam.Init(
        1000.0f,        // Initial value: 1kHz
        20000.0f,       // Min: high frequency (inverted)
        20.0f,          // Max: low frequency (inverted)
        100.0f,         // Fast increment
        10.0f,          // Slow increment
        nullptr,        // No callback
        0,              // UserData
        0.3f,           // Smoothing at 300ms to go from min to max
        0xFF            // No MIDI control
    );
    
    // Increase cutoff (reduces high frequencies)
    cutoffParam.Increment(5, false);  // 5 fast steps
    
    float currentCutoff = cutoffParam;
    
    // --- Scenario 3: Manual controls with normalization ---
    
    cParameter filterParam;
    filterParam.Init(100.0f, 20.0f, 10000.0f, 50.0f, 5.0f);
    
    // GUI using a normalized slider [0-1]
    float sliderValue = 0.5f;  // Slider position (50%)
    
    while(true) {
        // Convert slider to parameter value
        filterParam.setNormalizedValue(sliderValue);
        
        // Apply smoothing
        filterParam.Process();
        
        float currentValue = filterParam.getValue();
        applyFilter(currentValue);
        
        // ... other processing ...
    }
    
    // --- Scenario 4: Dynamic range adjustments ---
    
    cParameter dynamicParam;
    dynamicParam.Init(0.5f, 0.0f, 1.0f, 0.1f, 0.01f);
    
    while(true) {
        // Increase maximum during execution
        dynamicParam.setMaxValue(2.0f);
        
        // Current value will be clamped to the new maximum on next Process()
        dynamicParam.Process();
        
        float value = dynamicParam.getValue();  // Will be <= 2.0 now
        
        // ... other processing ...
    }
    
    return 0;
}
```
---

## 📊 Access Method Comparison

| Method | Returns | Typical Use |
|:---|:---|:---|
| `getValue()` | Current smoothed value | Real-time parameter application |
| `getTargetValue()` | Non-smoothed target value | Prediction, progress display |
| `operator float()` | Current smoothed value | Concise syntax in mathematical expressions |

---

## 📊 Normalized Value Comparison

| Method | Returns | When to Use |
|:---|:---|:---|
| `getNormalizedValue()` | Current [0.0-1.0] | UI display, graphic sliders |
| `setNormalizedValue()` | Sets via [0.0-1.0] | Normalized controllers, GUIs |
| `getNormalizedTargetValue()` | Target [0.0-1.0] | Progress indicators toward a target |

---

## ⚙️ Implementation Notes

1. **Linear Smoothing:** The `Process()` method uses a fixed-step linear smoothing algorithm (`m_Step`). The number of calls needed to reach the target depends on the slope factor and the distance between current and target values.

2. **Inverted Ranges:** The parameter supports ranges where `Min > Max` (e.g., 20000 to 20 Hz for a low-pass filter). In this case, increments are automatically reversed for intuitive logic.

3. **Intelligent Clamping:** The `setValue()` method correctly handles inverted ranges by clamping the target value to valid bounds regardless of Min/Max order.

4. **Dynamic Callback:** The callback is called on each update via `Process()`, allowing immediate responsiveness to parameter changes.

5. **MIDI Control:** Integration with the MIDI system occurs via `__Midi.addControlChangeCallback()`. The MIDI value (0-127) is linearly converted to the parameter's [Min, Max] range.

6. **Dirty Flag:** The `m_Dirty` flag can be used to optimize performance - for example, only updating the display if the parameter has actually changed.

7. **Implicit Conversion:** The conversion operator to float allows concise syntax but beware of accidental conversions in complex expressions.

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.