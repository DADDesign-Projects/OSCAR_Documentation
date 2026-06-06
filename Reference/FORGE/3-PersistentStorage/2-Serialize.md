---
title: cSerialize
parent: Persistent Storage
layout: default
nav_order: 2
---
# DadPersistentStorage::cSerialize

**Namespace:** DadPersistentStorage::cSerialize  
**Files:** Serialize.h / Serialize.cpp   
**Directory:** DAD_FORGE/PersistentStorage  
**Description:** Utility class for serialization/deserialization of data types in the persistent storage module.

---

## 📋 Class Description

The `cSerialize` class is a central utility of the DAD_FORGE persistent storage system, designed to facilitate binary data encoding and decoding. It provides a generic mechanism for storing data in an internal buffer (serialization) or extracting it (deserialization).

This class is essential for the persistence of `cSerializedObject` objects that implement the basic interface for data storage and retrieval. It supports:
- Generic serialization/deserialization via templates for all C++ types
- String handling (`std::string`) with automatic length management
- Raw data manipulation (`void*`) for fine-grained binary format control
- Complete internal buffer management (append, read, replace, clear)


>*Note: This class interacts with the abstract class `cSerializedObject` which defines the basic interface for serializable objects.*

---
## 📚 Public Methods

### **cSerialize()**

| Element | Details |
|:---|:---|
| **Method** | `cSerialize()` |
| **Description** | Default constructor that initializes an empty serialization buffer and resets the read index to 0. Creates a new instance ready to receive data. |

### **Push\<T\>(const T& value)**

| Element | Details |
|:---|:---|
| **Method** | `template<typename T> void Push(const T& value)` |
| **Description** | Template-based generic method that pushes a value of arbitrary type into the buffer. The method automatically converts the value to raw data and calls `PushRaw` with the appropriate type size. Supports all standard C++ types (int, float, double, bool, etc.). |
| **Parameter(s)** | |
| `value` | Constant reference to the value of type T to serialize into the buffer. |

### **Pull\<T\>(T& value)**

| Element | Details |
|:---|:---|
| **Method** | `template<typename T> void Pull(T& value)` |
| **Description** | Template-based generic method that extracts a value of arbitrary type from the buffer. The method reads the type size, retrieves raw data at the current read index, and reconstructs the value in the reference parameter. |
| **Parameter(s)** | |
| `value` | Non-constant reference to the variable of type T where the deserialized value extracted from the buffer will be stored. |

### **PushRaw(const void\* data, size_t size)**

| Element | Details |
|:---|:---|
| **Method** | `void PushRaw(const void* data, size_t size)` |
| **Description** | Method for adding raw data directly to the buffer. Used for inserting custom structures or types without automatic conversion. Data is copied byte by byte at the end of the current buffer. |
| **Parameter(s)** | |
| `data` | Constant pointer to the raw data to add to the buffer. |
| `size` | Size in bytes of the data to copy into the buffer. |

### **PullRaw(void\* data, size_t size)**

| Element | Details |
|:---|:---|
| **Method** | `void PullRaw(void* data, size_t size)` |
| **Description** | Method for extracting raw data from the buffer. Verifies that sufficient data is available from the current read index before copying data to the provided memory. The read index is incremented after extraction. |
| **Parameter(s)** | |
| `data` | Pointer to memory where the extracted data from the buffer will be copied. |
| `size` | Number of bytes to extract from the buffer. |

### **Push(const std::string& str)**

| Element | Details |
|:---|:---|
| **Method** | `void Push(const std::string& str)` |
| **Description** | Specialized method for string serialization. The string is encoded in two parts: first a 32-bit integer representing the string length, then the characters themselves. This structure allows reliable deserialization without knowing the string size in advance. |
| **Parameter(s)** | |
| `str` | Constant reference to the string to serialize. |

### **PullString()**

| Element | Details |
|:---|:---|
| **Method** | `std::string PullString()` |
| **Description** | Specialized method for extracting a string from the buffer. The process involves first reading the string length (32-bit), then extracting the corresponding characters. Returns an empty string on error (insufficient data in buffer). |
| **Return** | `std::string` - The deserialized string, or an empty string if extraction fails. |

### **getBuffer(const uint8_t** outBuffer) const**

| Element | Details |
|:---|:---|
| **Method** | `size_t getBuffer(const uint8_t** outBuffer) const` |
| **Description** | Method for accessing the complete serialization buffer content. Returns the current buffer size and provides a pointer to raw data via the output parameter. Useful for exporting serialized data to a file or external memory. |
| **Parameter(s)** | |
| `outBuffer` | Pointer to uint8_t pointer that will receive the buffer data address. |
| **Return** | `size_t` - Current buffer size in bytes. |

### **setBuffer(const void\* data, size_t size)**

| Element | Details |
|:---|:---|
| **Method** | `void setBuffer(const void* data, size_t size)` |
| **Description** | Method for completely replacing the buffer content with new data. Used to load serialized data from a file or external source. The buffer is entirely reassigned and the read index is reset to 0. |
| **Parameter(s)** | |
| `data` | Pointer to the raw data to load into the buffer. |
| `size` | Size in bytes of the data to load. |

### **clearBuffer()**

| Element | Details |
|:---|:---|
| **Method** | `void clearBuffer()` |
| **Description** | Method for completely clearing the serialization buffer. All data is deleted and the read index is reset to 0. This method prepares a new instance for serialization of new data. |

### **resetReadIndex()**

| Element | Details |
|:---|:---|
| **Method** | `void resetReadIndex()` |
| **Description** | Method for resetting only the read index to position 0 of the buffer, without modifying buffer content. Useful for allowing restart of deserialization on the same serialized data. |

---

## 🔒 Protected/Private Methods

No protected or private methods.

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected/Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `buffer` | `std::vector<uint8_t>` | Dynamic buffer containing all serialized data. Stores encoded raw data for persistence or transfer. |
| `readIndex` | `size_t` | Index indicating the current read position in the buffer. Used when extracting data to know where to start reading. |

---

## 💡 Usage Example

```cpp
#pragma once

#include "SerializeExample.h"
#include <iostream>
#include <fstream>
#include <vector>

using namespace DadPersistentStorage;

// Class A - Serializable with float members
class ClassA : public cSerializedObject {
public:
    float x;
    float y;
    float z;
    
    ClassA() : x(0.0f), y(0.0f), z(0.0f), dirtyFlag(true) {}
    ClassA(float a, float b, float c) : x(a), y(b), z(c), dirtyFlag(true) {}
    
    virtual void Save(cSerialize* pSerializer) override {
        pSerializer->Push(x);
        pSerializer->Push(y);
        pSerializer->Push(z);
    }
    
    virtual void Restore(cSerialize* pSerializer) override {
        pSerializer->Pull(x);
        pSerializer->Pull(y);
        pSerializer->Pull(z);
        dirtyFlag = false;
    }
    
    virtual bool isDirty() override { return dirtyFlag; }
    void setDirty(bool dirty) { dirtyFlag = dirty; }
       
private:
    bool dirtyFlag;
};

// Class B - Serializable with string
class ClassB : public cSerializedObject {
public:
    std::string message;
    
    ClassB() : message(""), dirtyFlag(true) {}
    ClassB(const std::string& msg) : message(msg), dirtyFlag(true) {}
    
    virtual void Save(cSerialize* pSerializer) override {
        pSerializer->Push(message);
    }
    
    virtual void Restore(cSerialize* pSerializer) override {
        message = pSerializer->PullString();
        dirtyFlag = false;
    }
    
    virtual bool isDirty() override { return dirtyFlag; }
    void setDirty(bool dirty) { dirtyFlag = dirty; }    
    
private:
    bool dirtyFlag;
};

// Class C - Serializable with raw structure

// Example structure
struct SensorData {
    uint32_t timestamp;
    int16_t temperature;   // in 0.1°C (e.g., 235 = 23.5°C)
    uint16_t humidity;     // in 0.1% (e.g., 578 = 57.8%)
    uint8_t status;        // 0=OK, 1=Warning, 2=Error
    uint8_t reserved[3];   // Padding for alignment
};

class ClassC : public cSerializedObject {
public:
    SensorData sensor;
    
    ClassC() : dirtyFlag(true) {
        std::memset(&sensor, 0, sizeof(SensorData));
    }
    
    ClassC(uint32_t ts, int16_t temp, uint16_t hum, uint8_t stat) : dirtyFlag(true) {
        sensor.timestamp = ts;
        sensor.temperature = temp;
        sensor.humidity = hum;
        sensor.status = stat;
        std::memset(sensor.reserved, 0, sizeof(sensor.reserved));
    }
    
    virtual void Save(cSerialize* pSerializer) override {
        // Push raw structure data
        pSerializer->PushRaw(&sensor, sizeof(SensorData));
    }
    
    virtual void Restore(cSerialize* pSerializer) override {
        pSerializer->PullRaw(&sensor, sizeof(SensorData));
        dirtyFlag = false;
    }
    
    virtual bool isDirty() override { return dirtyFlag; }
    void setDirty(bool dirty) { dirtyFlag = dirty; }
      
private:
    bool dirtyFlag;
};


// Helper function to save all objects to a file
void saveObjectsToFile(const std::vector<cSerializedObject*>& objects, 
                       const std::string& filename) {
    cSerialize serializer;
    
    // Save each object
    for (auto* obj : objects) {
        if (obj) {
            obj->Save(&serializer);
        }
    }
    
    // Get serialized buffer
    const uint8_t* bufferData = nullptr;
    size_t bufferSize = serializer.getBuffer(&bufferData);
    
    // Write to file
    std::ofstream file(filename, std::ios::binary);
    if (file.is_open()) {
        file.write(reinterpret_cast<const char*>(bufferData), bufferSize);
        file.close();
    } else {
        std::cerr << "Error: Cannot open " << filename << " for writing" << std::endl;
    }
}

// Helper function to restore objects from a file
void restoreObjectsFromFile(const std::vector<cSerializedObject*>& objects,
                            const std::string& filename) {
    // Read file
    std::ifstream file(filename, std::ios::binary | std::ios::ate);
    if (!file.is_open()) {
        std::cerr << "Error: Cannot open " << filename << " for reading" << std::endl;
        return;
    }
    
    size_t fileSize = file.tellg();
    file.seekg(0, std::ios::beg);
    
    std::vector<uint8_t> fileData(fileSize);
    file.read(reinterpret_cast<char*>(fileData.data()), fileSize);
    file.close();
    
    // Restore objects
    cSerialize serializer;
    serializer.setBuffer(fileData.data(), fileSize);
    
    for (auto* obj : objects) {
        if (obj) {
            obj->Restore(&serializer);
        }
    }
    
    std::cout << "Restored " << fileSize << " bytes from " << filename << std::endl;
}

int main() {
   
    // Create objects with initial data
    ClassA objA(1.5f, 2.7f, 3.14f);
    ClassB objB("Hello, this is a serialized string!");
    ClassC objC(1700000000,    // timestamp
                235,           // 23.5°C
                578,           // 57.8% humidity
                0);            // status OK
    
    // Store pointers in a vector for easy management
    std::vector<cSerializedObject*> objects = {
        &objA,
        &objB,
        &objC
    };
    
    // Save objects to file
    saveObjectsToFile(objects, "saved_data.bin");
    
    // Modify objects to verify restoration works
    objA = ClassA(99.9f, 88.8f, 77.7f);
    objB = ClassB("Temporary modified data that should be overwritten");
    objC = ClassC(999999999, -150, 300, 2);
       
    // Restore objects from file
    restoreObjectsFromFile(objects, "saved_data.bin");
        
    return 0;
}

```

---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.