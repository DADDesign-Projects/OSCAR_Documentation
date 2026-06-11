---
title: GUI_EventManager
parent: GUI Core
layout: default
nav_order: 2
---
# DadGUI::GUI_EventManager

**Namespace :** DadGUI::GUI_EventManager  
**Files :** GUI_Event.h  
**Directory :** DAD_FORGE/GUI/Core/  
**Inheritance**: None  
**Description :** Manages all GUI event subscriptions and dispatching.  

---

## 📋 Class Description
The `GUI_EventManager` class is the central component for user interface event management. It handles the following event types:
* **FastUpdate** (Fast update, timing defined by GUI_FAST_UPDATE_MS - 10ms by default);
* **Update** (Normal update, timing defined by GUI_UPDATE_MS - 300ms by default);
* **RT_ProcessIn** Event before the real-time audio loop.
* **RT_ProcessEvent** Event within the real-time audio loop.
* **RT_ProcessOutEvent** Event after the real-time audio loop.
* **SerializeSave** Event for saving into a preset.
* **SerializeRestore** Event for restoring a preset.
* **SerializeIsDirty** Event to test if the object's state has changed since the last save or restore.

Listening classes must inherit:
* from the `iGUI_EventListener` class for update and RT_Process events.
* from the `cSerializedObject` class for serialization events.

The `GUI_EventManager` class uses a "family" system (numeric identifiers) to allow selective message routing: broadcasting events to all subscribers or only to those belonging to a specific family (active or explicitly designated).

---

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"main.h"` | `DAD_FORGE/Core` | Basic definitions and system types |
| `"AudioManager.h"` | `DAD_FORGE/Drivers` | Audio loop manager |
| `"EventManager.h"` | `DadUtilities` | Subscription and broadcast management engine |
| `"Serialize.h"` | `DAD_FORGE/Core` | Object serialization management |

---

## 🎯 Associated Enumerations and Structures

### **iGUI_EventListener**
Abstract interface for objects wishing to receive event notifications related to the interface lifecycle or audio processing.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `on_GUI_RT_Process` | `void` | Called during real-time audio processing. |
| `on_GUI_RT_ProcessIn` | `void` | Called before audio processing (input buffer). |
| `on_GUI_RT_ProcessOut` | `void` | Called after audio processing (output buffer). |
| `on_GUI_Update` | `void` | Called during standard GUI update cycles. |
| `on_GUI_FastUpdate` | `void` | Called during fast GUI update cycles. |

---

## 📚 Public Methods

### **GUI_EventManager**

| Element | Details |
|:---|:---|
| **Method** | `GUI_EventManager()` |
| **Description** | Default constructor. |

### **~GUI_EventManager**

| Element | Details |
|:---|:---|
| **Method** | `~GUI_EventManager()` |
| **Description** | Default destructor. |

### **Subscribe_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_RT_Process(iGUI_EventListener* listener, uint32_t family = 0)` |
| **Description** | Subscribes to real-time processing events. |
| **Parameter(s)** | |
| `listener` | `iGUI_EventListener*` The object that will receive the event. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_RT_ProcessIn(iGUI_EventListener* listener, uint32_t family = 0)` |
| **Description** | Subscribes to pre-audio processing events. |
| **Parameter(s)** | |
| `listener` | `iGUI_EventListener*` The object that will receive the event. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_RT_ProcessOut(iGUI_EventListener* listener, uint32_t family = 0)` |
| **Description** | Subscribes to post-audio processing events. |
| **Parameter(s)** | |
| `listener` | `iGUI_EventListener*` The object that will receive the event. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_Update**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_Update(iGUI_EventListener* listener, uint32_t family = 0)` |
| **Description** | Subscribes to regular GUI update events. |
| **Parameter(s)** | |
| `listener` | `iGUI_EventListener*` The object that will receive the event. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_FastUpdate(iGUI_EventListener* listener, uint32_t family = 0)` |
| **Description** | Subscribes to fast GUI update events. |
| **Parameter(s)** | |
| `listener` | `iGUI_EventListener*` The object that will receive the event. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_SerializeSave(DadPersistentStorage::cSerializedObject* serializable, uint32_t family = 0)` |
| **Description** | Subscribes to serialization save events. |
| **Parameter(s)** | |
| `serializable` | `DadPersistentStorage::cSerializedObject*` The object to serialize. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_SerializeRestore(DadPersistentStorage::cSerializedObject* serializable, uint32_t family = 0)` |
| **Description** | Subscribes to serialization restore events. |
| **Parameter(s)** | |
| `serializable` | `DadPersistentStorage::cSerializedObject*` The object to restore. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_SerializeIsDirty(DadPersistentStorage::cSerializedObject* serializable, uint32_t family = 0)` |
| **Description** | Subscribes to "dirty" state check events for serialization. |
| **Parameter(s)** | |
| `serializable` | `DadPersistentStorage::cSerializedObject*` The object to check. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Subscribe_AllSerializeEvents**

| Element | Details |
|:---|:---|
| **Method** | `void Subscribe_AllSerializeEvents(DadPersistentStorage::cSerializedObject* serializable, uint32_t family = 0)` |
| **Description** | Simultaneously subscribes to Save, Restore, and IsDirty serialization events. |
| **Parameter(s)** | |
| `serializable` | `DadPersistentStorage::cSerializedObject*` The object to monitor. |
| `family` | `uint32_t` Family ID (0 to receive all events). |

### **Unsubscribe_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_RT_Process(iGUI_EventListener* listener)` |
| **Description** | Unsubscribes from real-time processing events. |

### **Unsubscribe_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_RT_ProcessIn(iGUI_EventListener* listener)` |
| **Description** | Unsubscribes from pre-audio processing events. |

### **Unsubscribe_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_RT_ProcessOut(iGUI_EventListener* listener)` |
| **Description** | Unsubscribes from post-audio processing events. |

### **Unsubscribe_Update**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_Update(iGUI_EventListener* listener)` |
| **Description** | Unsubscribes from GUI update events. |

### **Unsubscribe_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_FastUpdate(iGUI_EventListener* listener)` |
| **Description** | Unsubscribes from fast GUI update events. |

### **Unsubscribe_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_SerializeSave(DadPersistentStorage::cSerializedObject* serializable)` |
| **Description** | Unsubscribes from serialization save events. |

### **Unsubscribe_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_SerializeRestore(DadPersistentStorage::cSerializedObject* serializable)` |
| **Description** | Unsubscribes from serialization restore events. |

### **Unsubscribe_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_SerializeIsDirty(DadPersistentStorage::cSerializedObject* serializable)` |
| **Description** | Unsubscribes from serialization "dirty" check events. |

### **Unsubscribe_AllSerializeEvents**

| Element | Details |
|:---|:---|
| **Method** | `void Unsubscribe_AllSerializeEvents(DadPersistentStorage::cSerializedObject* serializable)` |
| **Description** | Unsubscribes from all serialization events. |

### **SetFamily_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `bool SetFamily_RT_Process(iGUI_EventListener* listener, uint32_t newFamily)` |
| **Description** | Modifies the associated family for RT Process events. |
| **Return** | `bool` | `true` if the modification succeeded. |

### **SetFamily_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `bool SetFamily_RT_ProcessIn(iGUI_EventListener* listener, uint32_t newFamily)` |
| **Description** | Modifies the associated family for RT Process In events. |
| **Return** | `bool` | `true` if the modification succeeded. |

### **SetFamily_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `bool SetFamily_RT_ProcessOut(iGUI_EventListener* listener, uint32_t newFamily)` |
| **Description** | Modifies the associated family for RT Process Out events. |
| **Return** | `bool` | `true` if the modification succeeded. |

### **SetFamily_Update**

| Element | Details |
|:---|:---|
| **Method** | `bool SetFamily_Update(iGUI_EventListener* listener, uint32_t newFamily)` |
| **Description** | Modifies the associated family for update events. |
| **Return** | `bool` | `true` if the modification succeeded. |

### **SetFamily_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `bool SetFamily_FastUpdate(iGUI_EventListener* listener, uint32_t newFamily)` |
| **Description** | Modifies the associated family for fast update events. |
| **Return** | `bool` | `true` if the modification succeeded. |

### **SetActiveFamily_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_RT_Process(uint32_t family)` |
| **Description** | Sets the active family for RT Process events. |

### **SetActiveFamily_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_RT_ProcessIn(uint32_t family)` |
| **Description** | Sets the active family for RT Process In events. |

### **SetActiveFamily_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_RT_ProcessOut(uint32_t family)` |
| **Description** | Sets the active family for RT Process Out events. |

### **SetActiveFamily_Update**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_Update(uint32_t family)` |
| **Description** | Sets the active family for update events. |

### **SetActiveFamily_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_FastUpdate(uint32_t family)` |
| **Description** | Sets the active family for fast update events. |

### **SetActiveFamily_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_SerializeSave(uint32_t family)` |
| **Description** | Sets the active family for save events. |

### **SetActiveFamily_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_SerializeRestore(uint32_t family)` |
| **Description** | Sets the active family for restore events. |

### **SetActiveFamily_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily_SerializeIsDirty(uint32_t family)` |
| **Description** | Sets the active family for dirty check events. |

### **SetActiveFamily4AllEvents**

| Element | Details |
|:---|:---|
| **Method** | `void SetActiveFamily4AllEvents(uint32_t family)` |
| **Description** | Sets the same active family for all serialization event types. |

### **GetActiveFamily_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_RT_Process() const` |
| **Description** | Retrieves the active family ID for RT Process. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_RT_ProcessIn() const` |
| **Description** | Retrieves the active family ID for RT Process In. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_RT_ProcessOut() const` |
| **Description** | Retrieves the active family ID for RT Process Out. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_Update**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_Update() const` |
| **Description** | Retrieves the active family ID for updates. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_FastUpdate() const` |
| **Description** | Retrieves the active family ID for fast updates. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_SerializeSave() const` |
| **Description** | Retrieves the active family ID for save events. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_SerializeRestore() const` |
| **Description** | Retrieves the active family ID for restore events. |
| **Return** | `uint32_t` | The active family ID. |

### **GetActiveFamily_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetActiveFamily_SerializeIsDirty() const` |
| **Description** | Retrieves the active family ID for dirty check events. |
| **Return** | `uint32_t` | The active family ID. |

### **sendEvent_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_RT_Process(uint32_t family = 0)` |
| **Description** | Broadcasts a real-time processing event to the specified family (0 = all). |

### **sendEvent_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_RT_ProcessIn(uint32_t family, AudioBuffer* pIn)` |
| **Description** | Broadcasts a pre-audio processing event. |
| **Parameter(s)** | |
| `family` | `uint32_t` Family ID. |
| `pIn` | `AudioBuffer*` Pointer to the input buffer. |

### **sendEvent_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_RT_ProcessOut(uint32_t family, AudioBuffer* pOut)` |
| **Description** | Broadcasts a post-audio processing event. |
| **Parameter(s)** | |
| `family` | `uint32_t` Family ID. |
| `pOut` | `AudioBuffer*` Pointer to the output buffer. |

### **sendEvent_Update**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_Update(uint32_t family = 0)` |
| **Description** | Broadcasts a GUI update event. |

### **sendEvent_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_FastUpdate(uint32_t family = 0)` |
| **Description** | Broadcasts a fast GUI update event. |

### **sendEvent_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_SerializeSave(uint32_t family, DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Broadcasts a serialization save event. |
| **Parameter(s)** | |
| `family` | `uint32_t` Family ID. |
| `pSerializer` | `DadPersistentStorage::cSerialize*` The serialization object. |

### **sendEvent_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `void sendEvent_SerializeRestore(uint32_t family, DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Broadcasts a serialization restore event. |
| **Parameter(s)** | |
| `family` | `uint32_t` Family ID. |
| `pSerializer` | `DadPersistentStorage::cSerialize*` The serialization object. |

### **sendEvent_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `bool sendEvent_SerializeIsDirty(uint32_t family = 0)` |
| **Description** | Broadcasts a dirty check event. |
| **Return** | `bool` | `true` if at least one object is dirty. |

### **sendEventToActive_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_RT_Process()` |
| **Description** | Broadcasts the RT Process event to the currently active family. |

### **sendEventToActive_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_RT_ProcessIn(AudioBuffer* pIn)` |
| **Description** | Broadcasts the RT Process In event to the currently active family. |
| **Parameter(s)** | |
| `pIn` | `AudioBuffer*` Pointer to the input buffer. |

### **sendEventToActive_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_RT_ProcessOut(AudioBuffer* pOut)` |
| **Description** | Broadcasts the RT Process Out event to the currently active family. |
| **Parameter(s)** | |
| `pOut` | `AudioBuffer*` Pointer to the output buffer. |

### **sendEventToActive_Update**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_Update()` |
| **Description** | Broadcasts the update event to the currently active family. |

### **sendEventToActive_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_FastUpdate()` |
| **Description** | Broadcasts the fast update event to the currently active family. |

### **sendEventToActive_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_SerializeSave(DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Broadcasts the save event to the currently active family. |
| **Parameter(s)** | |
| `pSerializer` | `DadPersistentStorage::cSerialize*` The serialization object. |

### **sendEventToActive_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `void sendEventToActive_SerializeRestore(DadPersistentStorage::cSerialize* pSerializer)` |
| **Description** | Broadcasts the restore event to the currently active family. |
| **Parameter(s)** | |
| `pSerializer` | `DadPersistentStorage::cSerialize*` The serialization object. |

### **sendEventToActive_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `bool sendEventToActive_SerializeIsDirty()` |
| **Description** | Broadcasts the dirty event to the currently active family. |
| **Return** | `bool` | `true` if at least one object is dirty. |

### **GetSubscriberCount_RT_Process**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_RT_Process(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for RT processing. |

### **GetSubscriberCount_RT_ProcessIn**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_RT_ProcessIn(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for pre-audio processing. |

### **GetSubscriberCount_RT_ProcessOut**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_RT_ProcessOut(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for post-audio processing. |

### **GetSubscriberCount_Update**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_Update(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for GUI updates. |

### **GetSubscriberCount_FastUpdate**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_FastUpdate(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for fast updates. |

### **GetSubscriberCount_SerializeSave**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_SerializeSave(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for save events. |

### **GetSubscriberCount_SerializeRestore**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_SerializeRestore(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for restore events. |

### **GetSubscriberCount_SerializeIsDirty**

| Element | Details |
|:---|:---|
| **Method** | `int GetSubscriberCount_SerializeIsDirty(uint32_t family = UINT32_MAX, bool includeUniversal = false) const` |
| **Description** | Returns the number of subscribers for dirty check events. |

### **Clear**

| Element | Details |
|:---|:---|
| **Method** | `void Clear()` |
| **Description** | Resets all event managers and clears subscriptions. |

---

## 📦 Data

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_rtProcessManager` | `DadUtilities::EventManager<iGUI_EventListener, void>` | RT Process event manager. |
| `m_rtProcessInManager` | `DadUtilities::EventManager<iGUI_EventListener, void, AudioBuffer*>` | RT Process In event manager. |
| `m_rtProcessOutManager` | `DadUtilities::EventManager<iGUI_EventListener, void, AudioBuffer*>` | RT Process Out event manager. |
| `m_updateManager` | `DadUtilities::EventManager<iGUI_EventListener, void>` | Update event manager. |
| `m_fastUpdateManager` | `DadUtilities::EventManager<iGUI_EventListener, void>` | Fast update event manager. |
| `m_SerializeSaveManager` | `DadUtilities::EventManager<DadPersistentStorage::cSerializedObject, void, DadPersistentStorage::cSerialize*>` | Save event manager. |
| `m_SerializeRestoreManager` | `DadUtilities::EventManager<DadPersistentStorage::cSerializedObject, void, DadPersistentStorage::cSerialize*>` | Restore event manager. |
| `m_SerializeisDirtyManager` | `DadUtilities::EventManager<DadPersistentStorage::cSerializedObject, bool, void>` | Dirty event manager. |
| `m_activeFamilyRTProcess` | `uint32_t` | Active family for RT Process. |
| `m_activeFamilyRTProcessIn` | `uint32_t` | Active family for RT Process In. |
| `m_activeFamilyRTProcessOut` | `uint32_t` | Active family for RT Process Out. |
| `m_activeFamilyUpdate` | `uint32_t` | Active family for updates. |
| `m_activeFamilyFastUpdate` | `uint32_t` | Active family for fast updates. |
| `m_activeFamilySerializeSave` | `uint32_t` | Active family for save events. |
| `m_activeFamilySerializeRestore` | `uint32_t` | Active family for restore events. |
| `m_activeFamilySerializeIsDirty` | `uint32_t` | Active family for dirty check events. |

<BR>

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.