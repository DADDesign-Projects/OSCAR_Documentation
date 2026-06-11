---
title: iUIComponent
parent: GUI Core
layout: default
nav_order: 3
---
# DadGUI::iUIComponent

**Namespace:** DadGUI::iUIComponent  
**Files:** iUIComponent.h  
**Directory:** DAD_FORGE/GUI/Core/  
**Inheritance:** Pure interface (None)  
**Description:** Abstract interface for all user interface components.

---

## 📋 Class Description
`iUIComponent` is the base interface for all user interface (UI) components. It defines a standard lifecycle for components, including activation, deactivation, and periodic updating. Any derived class must implement the `Redraw()` method to define how the component should be rendered visually.

---

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"main.h"` | `DAD_FORGE/GUI/Core/` | Defines basic data types. |

---

## 📚 Public Methods

### **~iUIComponent**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~iUIComponent()` |
| **Description** | Default virtual destructor. |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Activate()` |
| **Description** | Called when the component becomes visible or active. |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Deactivate()` |
| **Description** | Called when the component is hidden or replaced. |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Update()` |
| **Description** | Called on each cycle (frame) or control tick to refresh logic or visuals. |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Redraw() = 0` |
| **Description** | Forces the UI component to redraw. Pure virtual method. |
| **Return** | N/A |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.