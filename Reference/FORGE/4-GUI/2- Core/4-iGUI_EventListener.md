---
title: iGUI_EventListener
parent: GUI Core
layout: default
nav_order: 4
---
# DadGUI::iGUI_EventListener

**Namespace:** DadGUI::iGUI_EventListener  
**Files:** GUI_Event.h  
**Directory:** DAD_FORGE/GUI/Core/  
**Inheritance:** Interface (None)  
**Description:** Abstract interface for objects requiring real-time processing or user interface-related updates.

---

## 📋 Class Description
`iGUI_EventListener` is a "Listener" type interface that allows an object to subscribe to various events triggered by the GUI event management system (`GUI_EventManager`). It is designed to separate processing logic (particularly in the audio thread for `RT` methods) from visual update logic.

It provides hooks for:
- Real-time audio processing (before and after main processing).
- Periodic data processing (Update 300ms by default).
- High-frequency processing (FastUpdate 10ms by default).

---

## 📚 Public Methods

### **~iGUI_EventListener**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~iGUI_EventListener()` |
| **Description** | Virtual destructor for safe destruction of derived classes. |

### **on_GUI_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `virtual void on_GUI_RT_Process()` |
| **Description** | Real-time processing method called in the audio thread. |

### **on_GUI_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `virtual void on_GUI_RT_ProcessIn(AudioBuffer *pIn)` |
| **Description** | Processes the audio buffer through the object before the main audio processing. |
| **Parameter(s)** | |
| `pIn` | `AudioBuffer*` Pointer to the incoming audio buffer. |

### **on_GUI_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `virtual void on_GUI_RT_ProcessOut(AudioBuffer *pOut)` |
| **Description** | Processes the audio buffer through the object after the main audio processing. |
| **Parameter(s)** | |
| `pOut` | `AudioBuffer*` Pointer to the outgoing audio buffer. |

### **on_GUI_Update**

| Element | Details |
|:---|:---|
| **Method** | `virtual void on_GUI_Update()` |
| **Description** | Called during regular interface updates (e.g., standard refresh cycle). |

### **on_GUI_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `virtual void on_GUI_FastUpdate()` |
| **Description** | Called during fast interface updates (high frequency). |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.