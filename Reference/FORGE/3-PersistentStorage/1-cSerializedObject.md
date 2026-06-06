---
title: cSerializedObject
parent: Persistent Storage
layout: default
nav_order: 1
---
# DadPersistentStorage::cSerializedObject

**Namespace:** DadPersistentStorage::cSerializedObject  
**Files:** Serialize.h / (Interface only)   
**Directory:** DAD_FORGE/PersistentStorage  
**Description:** Abstract base class defining the interface for serializable objects in the persistent storage system.

---

## 📋 Class Description

The `cSerializedObject` class is an **abstract base class** that defines the contractual interface that all objects intended for persistence (storage, backup, transfer) in the DAD_FORGE system must respect. It serves as an interface contract for serialization and deserialization of custom objects.

### Role in the DSP Module
This class is fundamental to the system's persistence architecture:
- **Unified Interface**: Allows any object to implement a standardized method for being stored/retrieved
- **Decoupling**: Serializable objects do not need to know the implementation details of `cSerialize`
- **Extensibility**: Any class can inherit from this interface and become persistent

### Expected Derived Classes
Classes that implement this interface must provide concrete implementations for:
- `Save(cSerialize* pSerializer)` - Encoding data into the serialization buffer
- `Restore(cSerialize* pSerializer)` - Decoding data from the buffer
- `isDirty()` - Modification indicator requiring persistence

---


## 📚 Public Methods

### **cSerializedObject()**

| Element | Details |
|:---|:---|
| **Method** | `cSerializedObject()` (implicit constructor) |
| **Description** | Constructor of the abstract class. Implements no concrete logic as it is a pure interface intended to be inherited and implemented by derived classes. |

### **~cSerializedObject()**

| Element | Details |
|:---|:---|
| **Method** | `virtual ~cSerializedObject()` |
| **Description** | Virtual destructor of the abstract class. Ensures proper cleanup of derived objects when used with pointers or references to the base class. Virtual nature enables correct polymorphism. |

### **Save(cSerialize\* pSerializer)**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Save(cSerialize* pSerializer) = 0` |
| **Description** | Pure virtual method that must be implemented by derived classes. It encodes all object data into the serialization buffer provided via the `cSerialize` class. The implementation must follow a consistent format to enable reliable deserialization. **Mandatory for all persistable objects.** |
| **Parameter(s)** | |
| `pSerializer` | Pointer to the active `cSerialize` instance managing the serialization buffer. The object must write its data into this buffer via appropriate Push/Pull methods. |
| **Return** | `void` - No return (pure method). |

### **Restore(cSerialize\* pSerializer)**

| Element | Details |
|:---|:---|
| **Method** | `virtual void Restore(cSerialize* pSerializer) = 0` |
| **Description** | Pure virtual method that must be implemented by derived classes. It reads and reconstructs the object's state from the serialized data contained in the buffer provided via `cSerialize`. The extraction order must exactly match the insertion order from the Save method. |
| **Parameter(s)** | |
| `pSerializer` | Pointer to the active `cSerialize` instance containing the serialized data to restore. The object must extract its data via appropriate Pull/PullRaw methods. |
| **Return** | `void` - No return (pure method). |

### **isDirty()**

| Element | Details |
|:---|:---|
| **Method** | `virtual bool isDirty() = 0` |
| **Description** | Pure virtual method that must be implemented by derived classes. Returns an indicator of whether the object has been modified since its last persistence. Used to optimize save operations by only persisting objects that have changed. |
| **Parameter(s)** | None. |
| **Return** | `bool` - `true` if the object has unsaved modifications, `false` otherwise. |

---

## 🔒 Protected/Private Methods

No protected or private methods defined in the abstract class.

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected/Private Variables
No variables defined in the abstract class.

---

## 💡 Usage Example
[see cSerialize](2-Serialize.html )

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.