---
title: cUIMenu
parent: GUI Components
layout: default
nav_order: 6
---

# DadGUI::cUIMenu

**Namespace:** DadGUI::cUIMenu  
**Files:** cUIMenu.h / cUIMenu.cpp  
**Directory:** DAD_FORGE/GUI/Components/  
**Inheritance**: `iUIComponent`  
**Description:** Manages the main menu display, navigation between items, and activation of sub-menus.

---

## 📋 Class Description
The `cUIMenu` class is a user interface component that manages a tabular menu system (tabs). It allows listing multiple items (`MenuItem`), navigating between them via an encoder, and managing the activation of associated components. It also supports horizontal scrolling and displays directional arrows if the item list exceeds the screen width.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"GUI_Include.h"` | `DAD_FORGE/GUI` | Access to base types, graphic components, constants, and global objects used by the GUI |

### Global Variables Used and Driver Objects

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__Display` | `Display` | `Global GUI Context` | Graphic display manager |
| `__pActivePalette` | `Palette*` | `Global GUI Context` | Access to colors for menu rendering |
| `__LayerMenuLayerDyn` | `cLayer` | `Global GUI Context` | Dynamic layer for the menu |
| `__LayerMenuLayerStat` | `cLayer` | `Global GUI Context` | Static layer for the menu |

---

## 🎯 Associated Enumerations and Structures

### MenuItem
Represents an individual entry in the menu.

| Element | Type | Description |
|:---|:---|:---|
| `Name` | `std::string` | Display name of the menu item |
| `pItem` | `iUIComponent*` | Pointer to the GUI component associated with this item |
| `pNextMenu` | `cUIMenu*` | Optional pointer to a sub-menu |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `void Init()` |
| **Description** | Initializes the menu system by creating render layers, clearing the item list, and resetting navigation states. |
| **Return** | `void` |

### **addMenuItem**

| Element | Details |
|:---|:---|
| **Method** | `void addMenuItem(iUIComponent* pItem, std::string Name, cUIMenu* pNextMenu)` |
| **Description** | Adds a new item to the menu with its name and associated component. |
| **Parameter(s)** | |
| `pItem` | Pointer to the UI component to activate upon selection. |
| `Name` | Display text for the item. |
| `pNextMenu` | Optional pointer to a child menu (sub-menu). |
| **Return** | `void` |

### **Activate**

| Element | Details |
|:---|:---|
| **Method** | `void Activate()` |
| **Description** | Activates the menu display by bringing layers to the foreground and activates the first item in the list. |
| **Return** | `void` |

### **Deactivate**

| Element | Details |
|:---|:---|
| **Method** | `void Deactivate()` |
| **Description** | Deactivates the menu display and hides the graphic layers. |
| **Return** | `void` |

### **Update**

| Element | Details |
|:---|:---|
| **Method** | `void Update()` |
| **Description** | Handles navigation logic via the encoder (moving the active index, managing scrolling/shift) and updates the currently selected component. |
| **Return** | `void` |

### **Redraw**

| Element | Details |
|:---|:---|
| **Method** | `void Redraw()` |
| **Description** | Forces a redraw of menu items and the active component. |
| **Return** | `void` |

### **drawTab**

| Element | Details |
|:---|:---|
| **Method** | `void drawTab()` |
| **Description** | Draws the visible menu items, including highlighting of the active item and scroll indicators (arrows). |
| **Return** | `void` |

### **setItem**

| Element | Details |
|:---|:---|
| **Method** | `void setItem(uint8_t Item)` |
| **Description** | Sets the index of the item to activate (with bounds checking). |
| **Parameter(s)** | |
| `Item` | Target item index. |
| **Return** | `void` |

### **GetItem**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t GetItem() const` |
| **Description** | Retrieves the index of the currently selected item. |
| **Return** | `uint8_t` (Item index) |

---

## 🔒 Protected / Private Methods

### **drawArrowIndicator**

| Element | Details |
|:---|:---|
| **Method** | `void drawArrowIndicator(bool isLeft)` |
| **Description** | Draws a scroll indicator (arrow) on the left or right edge of the menu bar if items are off-screen. |
| **Parameter(s)** | |
| `isLeft` | `true` for a left arrow, `false` for a right arrow. |
| **Return** | `void` |

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pDynMenuLayer` | `DadGFX::cLayer*` | Pointer to the dynamic (active) render layer. |
| `m_pStatMenuLayer` | `DadGFX::cLayer*` | Pointer to the static (background) render layer. |
| `m_TabMenuItem` | `std::vector<MenuItem>` | List containing all menu items. |
| `m_ActiveItem` | `int8_t` | Index of the currently selected item. |
| `m_MenuShift` | `int8_t` | Horizontal offset for item scrolling. |
| `m_isActive` | `uint8_t` | Component activation indicator. |

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.