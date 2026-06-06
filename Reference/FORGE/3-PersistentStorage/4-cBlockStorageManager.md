---
title: cBlockStorageManager
parent: Persistent Storage
layout: default
nav_order: 4
---
# DadPersistentStorage::cBlockStorageManager

**Namespace:** DadPersistentStorage::cBlockStorageManager  
**Files:** cBlockStorageManager.h / cBlockStorageManager.cpp  
**Directory:** DAD_FORGE/PersistentStorage  
**Description:** Persistent block storage manager for QSPI Flash memory.

---

## 📋 Class Description

The `cBlockStorageManager` class is a persistent storage manager. It implements a block-based storage system where each save can be distributed across multiple physical blocks if necessary.

The class handles:
- Initialization and intelligent erasing of Flash memory (64K, 32K, 4K)
- Saving data with automatic distribution across multiple blocks
- Loading data using a unique identification number
- Complete deletion of saves by erasing the entire associated block chain
- Round-robin allocation system for wear leveling

The storage uses a chained data structure where each block contains a 16-byte header (3 uint32_t + 1 pointer) followed by data. Valid blocks are identified by a magic number `0xDADDBA55`.

---

## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"PersistentDefine.h"` | `Application (HardwareAndCo in OSCAR structure)`  | Definition of BLOCK_SIZE, DATA_SIZE, NUM_BLOCKS, BLOCK_STORAGE_MEM_SIZE constants |

### Global Variables and Driver Objects Used

| Variable / Object | Type | Source | Role |
|:---|:---|:---|:---|
| `__Flash` | `iQSPI_FlashMemory*` | `DAD_FORGE/Drivers` | QSPI Flash driver interface - used to write, read, and erase Flash memory blocks |
| `hrng` | `RNG_HandleTypeDef` | Application | RNG hardware handler - used to generate a random initialization index (wear leveling) |

---

## 🎯 Enumerations and Associated Structures

### sSaveBlock
Structure defining the complete layout of a save block in Flash memory. Each block contains a metadata header followed by stored data.

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `m_saveNumber` | `uint32_t` | Unique save identification number |
| `m_dataSize` | `uint32_t` | Size in bytes of the data contained in this block |
| `m_pNextBlock` | `sSaveBlock*` | Pointer to the next block (for multi-block saves) - nullptr if last block |
| `m_isValid` | `uint32_t` | Validity marker: `0xDADDBA55` = valid, `0xFFFFFFFF` = invalid/erased |
| `m_Data[DATA_SIZE]` | `uint8_t[]` | Data storage array (size calculated as BLOCK_SIZE - HEADER_SIZE) |

---

## 📚 Public Methods

### **cBlockStorageManager**

| Element | Details |
|:---|:---|
| **Method** | `cBlockStorageManager(uint8_t* pTabSaveBlock)` |
| **Description** | Constructor initializing the manager with a persistent storage block array. The provided pointer must reference the Flash memory area dedicated to block storage. Complete initialization (erasing and configuration) is done via the `Init()` method. |
| **Parameter(s)** | |
| `pTabSaveBlock` | Pointer to the block database in Flash memory. Must point to an aligned area large enough to contain NUM_BLOCKS * BLOCK_SIZE bytes. |

---

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `bool Init(uint32_t NumBuild)` |
| **Description** | Initializes the persistent storage system by verifying the integrity of the main block. If the block is invalid or does not match the build number, returns true (initialization required). If already correctly initialized, returns false. Generates a random initialization index for wear leveling. |
| **Parameter(s)** | |
| `NumBuild` | Current system build number - used to verify compatibility with existing data |
| **Return** | `true` if initialization is required, `false` if already initialized and valid |

---

### **InitializeMemory**

| Element | Details |
|:---|:---|
| **Method** | `void InitializeMemory(uint32_t NumBuild)` |
| **Description** | Completely initializes Flash memory by erasing all storage blocks then creating and saving the main block containing build information. Must be called after a complete erase or first use of the system. |
| **Parameter(s)** | |
| `NumBuild` | Build number to record in the main block for future identification |

---

### **InitializeBlock**

| Element | Details |
|:---|:---|
| **Method** | `void InitializeBlock()` |
| **Description** | Initializes Flash memory by erasing all storage blocks. Uses a hierarchical erase strategy (64K → 32K → 4K) to optimize performance and minimize the number of erase operations. |

---

### **Save**

| Element | Details |
|:---|:---|
| **Method** | `bool Save(uint32_t saveNumber, const void* pDataSource, uint32_t Size)` |
| **Description** | Saves data to Flash memory by distributing it across one or more blocks if necessary. First deletes any existing save with the same number, then writes the new data into a chain of linked blocks. Each block contains a 16-byte header followed by data. Returns false if available space is insufficient. |
| **Parameter(s)** | |
| `saveNumber` | Unique identification number for this save - used for future loading and deletion |
| `pDataSource` | Pointer to the data to be saved (constant, will not be modified) |
| `Size` | Size in bytes of the data to save |
| **Return** | `true` if save successful, `false` if insufficient space (save canceled and cleaned up) |

---

### **Load**

| Element | Details |
|:---|:---|
| **Method** | `void Load(uint32_t saveNumber, void* pData, uint32_t DataSize, uint32_t& Size)` |
| **Description** | Loads data from Flash memory using the save number as identifier. Reads the entire associated block chain and copies data to the provided buffer. The Size parameter is updated with the actual size of the loaded data. Does nothing if the save does not exist or if the buffer is too small. |
| **Parameter(s)** | |
| `saveNumber` | Identification number of the save to load |
| `pData` | Pointer to the data reception buffer (non-constant, will be filled) |
| `DataSize` | Minimum buffer size in bytes - must be >= actual save size |
| `Size` | Output reference containing the actual size of loaded data after call |

---

### **Delete**

| Element | Details |
|:---|:---|
| **Method** | `void Delete(uint32_t saveNumber)` |
| **Description** | Deletes a save from Flash memory by erasing all blocks that make up its chain. Deletion is physical (Flash erase) and irreversible. Erases each block individually with a delay to ensure erase operations complete. |
| **Parameter(s)** | |
| `saveNumber` | Identification number of the save to delete |

---

### **getSize**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t getSize(uint32_t saveNumber)` |
| **Description** | Returns the size in bytes of the data associated with the specified save number. If the save does not exist, returns 0. Used only to check required space before a call to Load(). |
| **Parameter(s)** | |
| `saveNumber` | Identification number whose size is wanted |
| **Return** | Save size in bytes, or 0 if not found |

---

## 🔒 Protected / Private Methods

### **findFreeBlock**

| Element | Details |
|:---|:---|
| **Method** | `sSaveBlock* findFreeBlock(sSaveBlock* blockExcl)` |
| **Description** | Searches for a free block in the storage area using round-robin allocation. Starts the search from the last used free index to improve performance and distribute wear. Allows excluding a specific block (used when creating multi-block chains). Returns nullptr if no free block is available. |
| **Parameter(s)** | |
| `blockExcl` | Pointer to a block to exclude from the search (to avoid loops in chains) |
| **Return** | Pointer to the first free block found, or nullptr if exhausted |

---

### **FindFirstBlock**

| Element | Details |
|:---|:---|
| **Method** | `sSaveBlock* FindFirstBlock(uint32_t saveNumber)` |
| **Description** | Searches for the first valid block matching the specified save number. Linearly scans all blocks from the beginning of the array until finding a match with m_isValid == HEADER_MAGIC and m_saveNumber == saveNumber, or returns nullptr if not found. |
| **Parameter(s)** | |
| `saveNumber` | Identification number of the block to search for |
| **Return** | Pointer to the first valid matching block, or nullptr if not found |

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_pTabSaveBlock` | `sSaveBlock*` | Pointer to the persistent storage block array in Flash memory - main database |
| `m_LastFreeIndex` | `uint32_t` | Index of the last block used for free block search - used by round-robin allocation for wear leveling |

---

## 💡 Usage Example

```cpp
#include "DadPersistentStorage/cBlockStorageManager.h"
#include "DadDrivers/W25Q128.h"

// System constant configuration (to be defined in PersistentDefine.h)
constexpr uint32_t FLASH_STORAGE_BASE = 0x90000000;   // Base of Flash storage area
constexpr uint32_t FLASH_STORAGE_SIZE = 1024 * 1024;  // 1 MB for persistent storage

// Pointer to the flash driver object (to be initialized in main.cpp)
extern iQSPI_FlashMemory* __Flash;

int main(void) {
    // Flash memory initialization (only on first boot)
    uint32_t currentBuild = 1;
    
    DadPersistentStorage::cBlockStorageManager storage((uint8_t*)FLASH_STORAGE_BASE);
    
    if (!storage.Init(currentBuild)) {
        // Already initialized, can use directly
        MyData_t myData = { /* ... */ };
        
        uint32_t dataSize = sizeof(MyData_t);
        storage.Save(1, &myData, dataSize);
        
        MyData_t loadedData;
        uint32_t actualSize;
        storage.Load(1, &loadedData, sizeof(MyData_t), actualSize);
    } else {
        // First initialization - erase and configure
        storage.InitializeMemory(currentBuild);
        
        // Initial save
        MyData_t myData = { /* ... */ };
        storage.Save(1, &myData, sizeof(MyData_t));
    }
    
    return 0;
}
```
---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.