---
title: cFlasherStorage
parent: Persistent Storage
layout: default
nav_order: 3
---
# DadPersistentStorage::cFlasherStorage

**Namespace:** DadPersistentStorage::cFlasherStorage  
**Files:** cFlasherStorage.h / cFlasherStorage.cpp   
**Directory:** DAD_FORGE/PersistentStorage  
**Description:** Read-only file management system for QSPI flash memory with multi-format support (BIN, IMG, ELF).

---

## 📋 Class Description

The `cFlasherStorage` class implements a **minimalist read-only file system** directly in QSPI flash memory. Unlike traditional file systems with write operations, this class provides direct and efficient access to data stored in flash without disk management overhead.  
Data must be placed in flash memory during physical programming of the device using the Flasher_Server and LoaderFlasher pair, see [How to Use FlasherLoader](../../../Tutos/1-To begin/4-LoaderFlasher/4-LoaderFlasher.html)

### File System Architecture
- **Directory Table:** Fixed array `Dir[DIR_FILE_COUNT]` containing metadata for each file (name, size, address, type)
- **Data Zone:** Raw memory `Data[]` where file data pointed to by the table is stored
- **Direct Access:** `DataAddress` addresses point directly to physical flash memory.

### Technical Characteristics
- **Maximum number of files:** `DIR_FILE_COUNT = 40` directory entries
- **Maximum name length:** `MAX_ENTRY_NAME = 40` characters per file (null-terminated)
- **Supported file types:** BIN, IMG, ELF with distinct signatures.

---
## 🔗 External Dependencies

### File Inclusions

| Included File | Source | Role |
|:---|:---|:---|
| `"PersistentDefine.h"` | `Application (HardwareAndCo in OSCAR structure)` | Definition of BLOCK_SIZE, DATA_SIZE, NUM_BLOCKS, BLOCK_STORAGE_MEM_SIZE constants |

---

## 🎯 Enumerations and Associated Structures

### eFileType

| Element | Hexadecimal Value | Description |
|:---|:---|:---|
| `FILE_TYPE_BIN` | `0x2851` | Generic binary file (data, graphics, audio) |
| `FILE_TYPE_IMG` | `0x2852` | Animated image with metadata (frames, dimensions) |
| `FILE_TYPE_ELF` | `0x2853` | ELF executable for loading into RAM memory |
| `FILE_TYPE_MIN` | `0x2851` | Minimum valid value (alias of FILE_TYPE_BIN) |
| `FILE_TYPE_MAX` | `0x2853` | Maximum valid value (alias of FILE_TYPE_ELF) |
| `FILE_TYPE_INVALID` | `0xFFFFFFFF` | Marker indicating an invalid or empty entry |

### stFile

| Element | Type | Description |
|:---|:---|:---|
| `Name[]` | `char[40]` | File name (null-terminated string). Unique identifier in the directory. |
| `Size` | `uint32_t` | File size in bytes. Used to allocate appropriate buffers. |
| `DataAddress` | `uint32_t` | Absolute start address of file data in QSPI flash memory. Direct pointer for fast access. |
| `FileType` | `eFileType` | File type (BIN/IMG/ELF). Determines processing and associated metadata. |

### cImageInfo

| Element | Type | Description |
|:---|:---|:---|
| `pLayerFrame` | `const uint8_t*` | Pointer to the image buffer in flash. Start of raw image data (excluding metadata). |
| `Width` | `uint16_t` | Image width in pixels. Used for graphic rendering and memory allocation. |
| `Height` | `uint16_t` | Image height in pixels. Used for graphic rendering and memory allocation. |
| `NbFrame` | `uint8_t` | Number of animation frames. Indicates whether it's a static image (1) or animated (>1). |

### sRegionInfo

| Element | Type | Description |
|:---|:---|:---|
| `file_offset` | `uint32_t` | Read offset within the ELF file. Position to start reading data from. |
| `dest_addr` | `uint8_t*` | Destination address in RAM. Where to copy data after extraction. |
| `source_addr` | `uint8_t*` | Source address in QSPI flash. Physical pointer to segment data. |
| `copy_size` | `uint32_t` | Number of bytes to copy from source to destination (code/data section). |
| `zero_size` | `uint32_t` | Number of bytes to zero after copy (uninitialized BSS section). |

---

## 📚 Public Methods

### **GetFileName(uint16_t FileIndex)**

| Element | Details |
|:---|:---|
| **Method** | `const char* GetFileName(uint16_t FileIndex) const` |
| **Description** | Returns the name of the file located at the specified index in the directory. The index must be between 0 and `DIR_FILE_COUNT - 1`. If the entry is invalid (uninitialized, invalid type), returns `nullptr`. Useful for listing available files. |
| **Parameter(s)** | |
| `FileIndex` | Position index in the directory (0 to 39). Directly indexes the `Dir[]` array. |
| **Return** | `const char*` - Pointer to the file name (null-terminated string), or `nullptr` if the index is invalid. |

### **GetFileType(uint16_t FileIndex)**

| Element | Details |
|:---|:---|
| **Method** | `eFileType GetFileType(uint16_t FileIndex) const` |
| **Description** | Returns the file type at the specified index in the directory. Allows quick identification of the file format before reading. If the entry is invalid, returns `FILE_TYPE_INVALID`. |
| **Parameter(s)** | |
| `FileIndex` | Position index in the directory (0 to 39). Directly indexes the `Dir[]` array. |
| **Return** | `eFileType` - File type (BIN/IMG/ELF), or `FILE_TYPE_INVALID` if the index is invalid. |

### **GetFileType(const char\* pFileName)**

| Element | Details |
|:---|:---|
| **Method** | `eFileType GetFileType(const char* pFileName) const` |
| **Description** | Returns the type of the file identified by its name. Performs a linear search in the directory to find the entry matching the provided name. If the file does not exist or is invalid, returns `FILE_TYPE_INVALID`. Convenient method to check a type without knowing the index. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the file name to search for (null-terminated string). Must exactly match a directory entry. |
| **Return** | `eFileType` - Type of the found file, or `FILE_TYPE_INVALID` if not found or invalid. |

### **GetFilePtr(const char\* pFileName)**

| Element | Details |
|:---|:---|
| **Method** | `uint8_t* GetFilePtr(const char* pFileName) const` |
| **Description** | Returns a direct pointer to the raw data of the file in QSPI flash memory. Access is read-only from physical flash memory, offering optimal performance without intermediate copy. Useful for streaming data directly from flash. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the name of the file to access. Must exactly match a valid directory entry. |
| **Return** | `uint8_t*` - Direct pointer to the file data in flash, or `nullptr` if the file does not exist or is invalid. |

### **GetFileSize(const char\* pFileName)**

| Element | Details |
|:---|:---|
| **Method** | `uint32_t GetFileSize(const char* pFileName) const` |
| **Description** | Returns the size in bytes of the file identified by its name. Useful for allocating an appropriate read buffer before accessing data via `GetFilePtr()`. If the file does not exist, returns 0. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the name of the file whose size is wanted. Must exactly match a valid directory entry. |
| **Return** | `uint32_t` - File size in bytes, or 0 if the file does not exist or is invalid. |

### **GetImgInformation(const char\* pFileName, uint8_t\*& ImgPtr, uint8_t& NbFrame, uint16_t& Width, uint16_t& Height)**

| Element | Details |
|:---|:---|
| **Method** | `bool GetImgInformation(const char* pFileName, uint8_t*& ImgPtr, uint8_t& NbFrame, uint16_t& Width, uint16_t& Height)` |
| **Description** | Decrypts image metadata (IMG format) from the 16-byte magic block located at the end of the file. The IMG format encodes: "IMAG" signature, number of frames, width, and height. If the image does not exist, is invalid, or is too small (< 16 bytes), returns `false`. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the name of the image file to analyze. Must exactly match a valid IMG type entry. |
| `ImgPtr` | Output parameter (reference) that will receive the pointer to the raw image data (excluding metadata). |
| `NbFrame` | Output parameter (reference) that will receive the number of animation frames. |
| `Width` | Output parameter (reference) that will receive the image width in pixels. |
| `Height` | Output parameter (reference) that will receive the image height in pixels. |
| **Return** | `bool` - `true` if metadata was successfully extracted, `false` if the file does not exist, is invalid, or is too small to contain the magic block. |

### **GetElfRegionsInformation(const char\* pFileName, uint8_t\*& FilePtr, sRegionInfo\*& pRegions, uint32_t& nbRegions)**

| Element | Details |
|:---|:---|
| **Method** | `bool GetElfRegionsInformation(const char* pFileName, uint8_t*& FilePtr, sRegionInfo*& pRegions, uint32_t& nbRegions)` |
| **Description** | Decrypts region descriptors of an ELF file from the magic block located immediately after the file data. The ELF format encodes: "ELF0" signature, number of regions (segments), and an array of `sRegionInfo` for each segment to load into RAM. If the file does not exist, is invalid, or is too small (< 8 bytes), returns `false`. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the name of the ELF file to analyze. Must exactly match a valid ELF type entry. |
| `FilePtr` | Output parameter (reference) that will receive the pointer to the raw ELF file data. |
| `pRegions` | Output parameter (reference) that will receive a pointer to the array of regions to load. |
| `nbRegions` | Output parameter (reference) that will receive the number of regions in the array. |
| **Return** | `bool` - `true` if descriptors were successfully extracted, `false` if the file does not exist, is invalid, or is too small to contain the magic block. |

---

## 🔒 Protected / Private Methods

### **findFileIndex(const char* pFileName)**

| Element | Details |
|:---|:---|
| **Method** | `int16_t findFileIndex(const char* pFileName) const` |
| **Description** | Private linear search method that returns the directory index corresponding to the provided file name. Iterates through all entries and compares names with `strcmp()`. Returns `-1` if the file is not found or if the name is null. Used by all public file access methods. |
| **Parameter(s)** | |
| `pFileName` | Pointer to the file name to search for in the directory. Must be a valid null-terminated string. |
| **Return** | `int16_t` - Index of the found entry (0 to 39), or `-1` if not found. |

### **isValidEntry(uint16_t Index)**

| Element | Details |
|:---|:---|
| **Method** | `bool isValidEntry(uint16_t Index) const` |
| **Description** | Private validation method that checks if the entry at the specified index contains a valid file. Checks: index is within bounds (`< DIR_FILE_COUNT`) AND file type is known (`>= FILE_TYPE_MIN` and `<= FILE_TYPE_MAX`). Used by all public methods to avoid invalid accesses. |
| **Parameter(s)** | |
| `Index` | Index to check in the directory (0 to 39). |
| **Return** | `bool` - `true` if the entry is valid and contains a recognized file, `false` otherwise. |

---

## 📦 Data Members (Variables)

### Public Variables
No public variables.

### Protected / Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `Dir[]` | `stFile[40]` | File directory table. Contains metadata for up to 40 files stored in flash. Each entry contains name, size, address, and type. |
| `Data[]` | `uint8_t[]` | Raw file data storage area. Size = `FLASHER_MEM_SIZE - sizeof(Dir)`. Contains the actual file data pointed to by `Dir[].DataAddress`. |

---

## 💡 Usage Example

```cpp
#include "cFlasherStorage.h"
#include <iostream>

using namespace DadPersistentStorage;

int main() {
    // --- 1. Initialize flash storage manager ---
    extern uint8_t FlashFileSystem[];  // Address of flash memory dedicated to file system
    
    cFlasherStorage storage(FlashFileSystem);
    
    std::cout << "Initializing flash file system..." << std::endl;
    
    // --- 2. List available files ---
    std::cout << "\n--- List of files ---" << std::endl;
    for (uint16_t i = 0; i < DIR_FILE_COUNT; ++i) {
        const char* name = storage.GetFileName(i);
        if (name != nullptr) {
            eFileType type = storage.GetFileType(i);
            
            switch (type) {
                case FILE_TYPE_BIN:
                    std::cout << "  [" << i << "] " << name << " [BIN]";
                    break;
                case FILE_TYPE_IMG:
                    std::cout << "  [" << i << "] " << name << " [IMG]";
                    break;
                case FILE_TYPE_ELF:
                    std::cout << "  [" << i << "] " << name << " [ELF]";
                    break;
                default:
                    std::cout << "  [" << i << "] " << name << " [?]";
            }
            
            uint32_t size = storage.GetFileSize(name);
            std::cout << " (" << size << " bytes)" << std::endl;
        } else {
            break;  // End of valid directory
        }
    }
    
    // --- 3. Access a Binary file (BIN) ---
    const char* binName = "config.bin";
    if (storage.GetFileType(binName) == FILE_TYPE_BIN) {
        uint8_t* ptr = storage.GetFilePtr(binName);
        uint32_t size = storage.GetFileSize(binName);
        
        std::cout << "\nFile '" << binName << "':" << std::endl;
        std::cout << "  Flash address: 0x" << std::hex << ptr << std::dec << std::endl;
        std::cout << "  Size: " << size << " bytes" << std::endl;
        
        // Direct read from flash (read-only)
        uint8_t buffer[256];
        memcpy(buffer, ptr, 256);
        std::cout << "  First byte: 0x" << std::hex << (int)buffer[0] << std::dec << std::endl;
    }
    
    // --- 4. Analyze an animated image (IMG) ---
    const char* imgName = "background.gif";
    if (storage.GetFileType(imgName) == FILE_TYPE_IMG) {
        uint8_t* imgPtr = nullptr;
        uint8_t nbFrame = 0;
        uint16_t width = 0, height = 0;
        
        bool success = storage.GetImgInformation(imgName, imgPtr, nbFrame, width, height);
        
        if (success) {
            std::cout << "\nImage '" << imgName << "':" << std::endl;
            std::cout << "  Dimensions: " << width << " x " << height << " pixels" << std::endl;
            std::cout << "  Frames: " << nbFrame << (nbFrame == 1 ? " (static)" : " (animated)") << std::endl;
            std::cout << "  Data pointer: 0x" << std::hex << imgPtr << std::dec << std::endl;
        } else {
            std::cout << "  Error: unable to read IMG metadata" << std::endl;
        }
    }
        
    return 0;
}
```
---
**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
