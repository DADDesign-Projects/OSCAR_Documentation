---
title: cUIVuMeter
parent: GUI Components
layout: default
nav_order: 8
---

# DadGUI::cUIVuMeter

**Namespace:** DadGUI::cUIVuMeter  
**Files:** cUIVuMeter.h / cUIVuMeter.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Description:** Stereo VU-meter display component for real-time visualization of audio levels (Input and Output).

---

## 📋 Class Description
The `cUIVuMeter` class is a graphic component that displays audio level indicators (VU-meters) for left and right channels, for both input signal (In) and output signal (Out). It uses dedicated DSP processors (`DadDSP::cVuMeter`) to calculate levels in dB and peaks. The component manages the dynamic display of level bars, peaks, and clipping indicators.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"AudioManager.h"` | `DAD_FORGE/Core` | Access to audio buffers |
| `"cVuMeter.h"` | `DAD_FORGE/DSP` | VU level calculation processor |
| `"GUI_Event.h"` | `DAD_FORGE/GUI` | Update event management |
| `"cDisplay.h"` | `DAD_FORGE/GUI` | Graphic display management |

### External Functions Called

| Function | Source | Usage |
|:---|:---|:---|
| `void Subscribe_RT_ProcessIn(...)` | `Global GUI Event Manager` | Subscribes the component to the input audio stream |
| `void Subscribe_RT_ProcessOut(...)` | `Global GUI Event Manager` | Subscribes the component to the output audio stream |
| `void Subscribe_Update(...)` | `Global GUI Event Manager` | Subscribes the component to GUI updates |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__Display` | `Display` | `Global GUI Context` | Graphic display manager |
| `__pActivePalette` | `Palette*` | `Global GUI Context` | Color palette for VU-meter rendering |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init()` |
| **Description** | Initializes the display layer, initializes the four VU-meter processors (In L/R, Out L/R), and subscribes to audio and GUI events. |
| **Return** | `void` |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `void Activate()` |
| **Description** | Activates the component, resets VU-meter levels and peaks, and brings the graphic layer to the foreground. |
| **Return** | `void` |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `void Deactivate()` |
| **Description** | Deactivates the component, resets levels, and hides the graphic layer. |
| **Return** | `void` |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_Update()` |
| **Description** | Refreshes the dynamic display (level bars, peaks, clipping) if the component is active. |
| **Return** | `void` |

### **on_GUI_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_RT_ProcessIn(AudioBuffer* pIn)` |
| **Description** | Processes the input audio buffer to update input VU-meter levels. |
| **Parameter(s)** | |
| `pIn` | Pointer to the input audio buffer. |
| **Return** | `void` |

### **on_GUI_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void on_GUI_RT_ProcessOut(AudioBuffer* pOut)` |
| **Description** | Processes the output audio buffer to update output VU-meter levels. |
| **Parameter(s)** | |
| `pOut` | Pointer to the output audio buffer. |
| **Return** | `void` |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `void Redraw()` |
| **Description** | Forces a complete redraw of static and dynamic parts. |
| **Return** | `void` |

---

## 🔒 Protected / Private Methods

### **drawStatPartOffLayer**

| Element | Details |
|:---|:---|
| **Method** | `void drawStatPartOffLayer()` |
| **Description** | Draws fixed elements: VU-meter frames, channel labels (L/R, In/Out), and background. |
| **Return** | `void` |

### **drawDynPartOffLayer**

| Element | Details |
|:---|:---|
| **Method** | `void drawDynPartOffLayer()` |
| **Description** | Draws moving elements: level bars (based on dB percentage), peak lines, and clipping indicators (circles). |
| **Return** | `void` |

---

## 📦 Data Members (Variables)

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pVuMeterLayer` | `DadGFX::cLayer*` | Pointer to the dedicated display layer. |
| `m_isActive` | `bool` | Component activation state. |
| `m_VuMeterInLeft` | `DadDSP::cVuMeter` | VU-meter processor (Input Left). |
| `m_VuMeterInRight` | `DadDSP::cVuMeter` | VU-meter processor (Input Right). |
| `m_VuMeterOutLeft` | `DadDSP::cVuMeter` | VU-meter processor (Output Left). |
| `m_VuMeterOutRight` | `DadDSP::cVuMeter` | VU-meter processor (Output Right). |
| `m_MemClippingInLeft` | `bool` | Clipping state memory (Input Left). |
| `m_MemClippingInRight` | `bool` | Clipping state memory (Input Right). |
| `m_MemClippingOutLeft` | `bool` | Clipping state memory (Output Left). |
| `m_MemClippingOutRight` | `bool` | Clipping state memory (Output Right). |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.