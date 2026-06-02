---
title: iQSPI_FlashMemory
parent: Drivers
layout: default
nav_order: 6
---
# DadDrivers::iQSPI_FlashMemory

**Namespace:** DadDrivers::iQSPI_FlashMemory  
**Files:** iQSPI_FlashMemory.h   
**Directory:** DAD_FORGE/Drivers   
**Description:** Abstract interface for QSPI (Quad SPI) flash memory devices

---

## 📋 Class Description

The `iQSPI_FlashMemory` interface defines the contractual agreement for all QSPI flash memory controller implementations in the DadDrivers library. This abstract interface enables polymorphic programming, where specific classes (such as `cW25Q128`) can implement different flash technologies while sharing a unified API. The interface supports memory-mapped and indirect modes, as well as all fundamental operations: read, write, sector/block/chip erase, and hardware identification.

---

## 🎯 Enumerations and Associated Structures

### FlashID

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `ManufactuerID` | `uint8_t` | Manufacturer identification code (e.g., 0xEF for Winbond, 0xC2 for Macronix) |
| `MemoryType` | `uint8_t` | Flash memory type code (indicates capacity and generation) |
| `Capacity` | `uint8_t` | Memory capacity code (in MB: 16, 32, 64, 128, etc.) |

---

## 📚 Public Methods

### **Init**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef Init(QSPI_HandleTypeDef* phqspi, bool DualMode = false, uint32_t MemoryAddress = 0x90000000)` |
| **Description** | Initializes the QSPI interface and flash memory. Configures the QSPI controller, performs a software reset, initializes status registers (block protection, driver strength), enables Quad I/O mode, and configures memory mapping. The default version implements reset and initialization of registers 1 and 3. |
| **Parameter(s)** | |
| `phqspi` | Pointer to the configured HAL QSPI handle (contains GPIOs for MOSI, MISO, CLK, CS) |
| `DualMode` | Flag enabling dual flash mode (two flash chips in parallel), default false |
| `MemoryAddress` | Base address for memory mapping in the processor's address space, default 0x90000000 |
| **Return** | HAL status code: `HAL_OK` success, `HAL_ERROR` initialization failure |

### **ModeMemoryMap**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef ModeMemoryMap()` |
| **Description** | Configures the flash memory in mapped mode, allowing the CPU to directly access the flash memory via standard read instructions (LDR/STR) without issuing SPI commands. The CPU can read the entire memory capacity as if it were RAM. Requires cache invalidation after activation. |
| **Parameter(s)** | None |
| **Return** | HAL status code: `HAL_OK` success, `HAL_ERROR` configuration failure |

### **ModeIndirect**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef ModeIndirect()` |
| **Description** | Restores indirect access mode after using memory mapping. The CPU returns to issuing standard SPI commands (READ, WRITE, ERASE) to access the flash memory. Required before any write or erase operations. |
| **Parameter(s)** | None |
| **Return** | HAL status code: `HAL_OK` success, `HAL_ERROR` configuration failure |

### **Read**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef Read(uint8_t* pData, uint32_t Address, uint32_t NbData)` |
| **Description** | Performs a read operation from the QSPI flash memory. The output buffer must be allocated by the caller. |
| **Parameter(s)** | |
| `pData` | Pointer to the read buffer where data will be stored (must be accessible in RAM) |
| `Address` | Starting read address in the flash memory (Memory-mapped address) |
| `NbData` | Number of bytes to read |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` operation timeout, `HAL_ERROR` error |

### **Write**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef Write(uint8_t* pData, uint32_t Address, uint32_t NbData)` |
| **Description** | Performs a write (programming) operation to the QSPI flash memory. |
| **Parameter(s)** | |
| `pData` | Pointer to the write buffer containing data to be programmed |
| `Address` | Starting write address in the flash memory (Memory-mapped address) |
| `NbData` | Number of bytes to write |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` programming timeout, `HAL_ERROR` error |

### **EraseBlock4K**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef EraseBlock4K(uint32_t Address)` |
| **Description** | Erases a 4 KB sector (or 8 KB in DualMode) of the QSPI flash memory. After erasure, all bits are set to 1 (flash "empty" state). |
| **Parameter(s)** | |
| `Address` | Starting address of the sector to erase (Memory-mapped address) |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` erase timeout, `HAL_ERROR` error |

### **EraseBlock32K**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef EraseBlock32K(uint32_t Address)` |
| **Description** | Erases a 32 KB block (or 64 KB in DualMode) of the QSPI flash memory. |
| **Parameter(s)** | |
| `Address` | Starting address of the block to erase (Memory-mapped address) |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` erase timeout, `HAL_ERROR` error |

### **EraseBlock64K**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef EraseBlock64K(uint32_t Address)` |
| **Description** | Erases a 64 KB block (or 128 KB in DualMode) of the QSPI flash memory. |
| **Parameter(s)** | |
| `Address` | Starting address of the 64K block to erase (Memory-mapped address) |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` erase timeout, `HAL_ERROR` error |

### **EraseChip**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef EraseChip()` |
| **Description** | Erases the entire QSPI flash memory chip. Complete operation that erases all sectors, blocks, and pages. |
| **Parameter(s)** | None |
| **Return** | HAL status code: `HAL_OK` success, `HAL_TIMEOUT` chip erase timeout, `HAL_ERROR` error |

### **getSize**

| Element | Details |
|:---|:---|
| **Method** | `virtual uint32_t getSize() const` |
| **Description** | Returns the total size of the flash memory in bytes. Fixed value based on the model (e.g., 16777216 for W25Q128 = 16MB). |
| **Parameter(s)** | None |
| **Return** | `uint32_t` value: total memory size in bytes |

### **getFlashID**

| Element | Details |
|:---|:---|
| **Method** | `virtual HAL_StatusTypeDef getFlashID(FlashID* pID)` |
| **Description** | Reads and returns the flash device identification (Manufacturer ID, Memory Type, Capacity). |
| **Parameter(s)** | |
| `pID` | Pointer to the FlashID structure to be filled with device identification |
| **Return** | HAL status code: `HAL_OK` success, `HAL_ERROR` read failed |

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.