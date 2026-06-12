---
title: cDisplay
parent: STM_GFX2
layout: default
nav_order: 2
---

# DadGFX::cDisplay

**Namespace:** DadGFX::cDisplay  
**Files:** cDisplay.h / cDisplay.cpp  
**Directory:** DAD_FORGE/STM_GFX2  
**Inheritance:** TFT_SPI  
**Description:** Display manager.  

---

## 📋 Class Description

The `cDisplay` class acts as the main rendering engine for the graphical interface:
- It manages hardware initialization via SPI, screen rotation handling, and the organization of graphical layers (`cLayer` and `cImageLayer`).
- It uses a "dirty block" system to refresh only the areas of the screen that have been modified, thus significantly optimizing SPI bus bandwidth and CPU usage.
- It also supports color blending (alpha blending) via the DMA2D engine (if available) or software calculation.

---

## 🔗 External Dependencies

### **File Inclusions**

| Included File | Source | Role |
|:---|:---|:---|
| `"Sections.h"` | `DAD_FORGE/GUI/Core/` | Definition of memory sections (DMA, RAM, etc.) |
| `"GFX.h"` | `DAD_FORGE/GUI/Core/` | Drawing primitives |
| `"TFT_SPI.h"` | `DAD_FORGE/GUI/Core/` | SPI driver for the display controller |

### **External Functions Called**

| Function | Source | Usage |
|:---|:---|:---|
| `void Init_TFT_SPI(SPI_HandleTypeDef*)` | `TFT_SPI` | SPI bus initialization |
| `void setTFTRotation(Rotation)` | `TFT_SPI` | Apply hardware rotation |
| `void SendDMACommand(...)` | `TFT_SPI` | Launch SPI command via DMA |
| `void SendDMAData(...)` | `TFT_SPI` | Transfer data via DMA |

---

## 🎯 Associated Enumerations and Structures

### **ORIENTATION**
Display orientation.

| Element | Value | Description |
|:---|:---|:---|
| `Portrait` | - | Vertical orientation |
| `Landscape` | - | Horizontal orientation |

### **DRAW_MODE**
Rendering mode for graphical layers.

| Element | Value | Description |
|:---|:---|:---|
| `Blend` | - | Color blending with transparency (Alpha) management |
| `Overwrite` | - | Overwrites existing pixels without blending |

### **sFIFO_Data**
Storage structure for the queue (FIFO) of DMA transfer commands.

| Element / Variable | Type / Value | Description |
|:---|:---|:---|
| `m_CmdCASET` | `Cmd_CASET[SIZE_FIFO]` | FIFO of column address set commands |
| `m_CmdRASET` | `Cmd_RASET[SIZE_FIFO]` | FIFO of row address set commands |
| `m_CmdRAWWR` | `Cmd_RAMWR[SIZE_FIFO]` | FIFO of pixel data write commands |

---

## 📚 Public Methods

### **cDisplay**

| Element | Details |
|:---|:---|
| **Method** | `cDisplay()` |
| **Description** | Constructor: initializes layer vectors and resets change flags. |

### **~cDisplay**

| Element | Details |
|:---|:---|
| **Method** | `~cDisplay()` |
| **Description** | Destructor: releases layer memory and cleans up resources. |

### **init**

| Element | Details |
|:---|:---|
| **Method** | `void init(SPI_HandleTypeDef* phspi, sFIFO_Data *pFIFO_Data, sColor* pDirtyBlocFrame)` |
| **Description** | Configures screen dimensions, refresh blocks, initializes the SPI bus, and prepares FIFO memory. |
| **Parameter(s)** | |
| `phspi` | `SPI_HandleTypeDef*` Pointer to the HAL SPI peripheral structure |
| `pFIFO_Data` | `sFIFO_Data*` Pointer to the FIFO structure (must be in DMA-accessible memory) |
| `pDirtyBlocFrame` | `sColor*` Pointer to the temporary framebuffer for dirty blocks |

### **addLayer**

| Element | Details |
|:---|:---|
| **Method** | `cLayer* addLayer(sColor* pLayerFrame, uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, uint8_t zPos)` |
| **Description** | Adds a standard graphical layer to the display. |
| **Parameter(s)** | |
| `pLayerFrame` | `sColor*` Pointer to the layer's framebuffer |
| `x` | `uint16_t` Layer X position |
| `y` | `uint16_t` Layer Y position |
| `Width` | `uint16_t` Layer width |
| `Height` | `uint16_t` Layer height |
| `zPos` | `uint8_t` Z-order (superposition order) |
| **Return** | `cLayer*` Pointer to the created layer or `nullptr` on failure. |

### **addLayer (Image)**

| Element | Details |
|:---|:---|
| **Method** | `cImageLayer* addLayer(const uint8_t * pLayerFrame, uint16_t x, uint16_t y, uint16_t Width, uint16_t Height, uint8_t zPos, uint8_t NbFrame = 1)` |
| **Description** | Adds an image layer. |
| **Parameter(s)** | |
| `pLayerFrame` | `const uint8_t*` Pointer to raw image data |
| `x` | `uint16_t` X position |
| `y` | `uint16_t` Y position |
| `Width` | `uint16_t` Width |
| `Height` | `uint16_t` Height |
| `zPos` | `uint8_t` Z-order |
| `NbFrame` | `uint8_t` Number of frames (for animations/multiple buffers) |
| **Return** | `cImageLayer*` Pointer to the created layer. |

### **setOrientation**

| Element | Details |
|:---|:---|
| **Method** | `void setOrientation(Rotation r)` |
| **Description** | Changes the display orientation (Portrait/Landscape) and invalidates the entire screen for refresh. |
| **Parameter(s)** | |
| `r` | `Rotation` The new desired orientation |

### **flush**

| Element | Details |
|:---|:---|
| **Method** | `void flush()` |
| **Description** | Triggers screen refresh by sending all blocks marked as "dirty" via the DMA FIFO. |

---

## 🔒 Protected / Private Methods

### **Blend2Block**

| Element | Details |
|:---|:---|
| **Method** | `void Blend2Block(uint32_t OutputOffset, uint32_t InputOffset1, uint32_t InputOffset2, sColor* pSource1, sColor* pSource2, sColor* pDest, uint32_t Width, uint32_t Height)` |
| **Description** | Performs alpha blending between two pixel blocks (foreground and background) and stores the result in the destination. <br> <br> If **USE_DMA2D** is defined, performs blending using the DMA2D hardware of the STM32H7xx. |
| **Parameter(s)** | |
| `OutputOffset` | `uint32_t` Line end offset for the destination |
| `InputOffset1` | `uint32_t` Line end offset for source 1 |
| `InputOffset2` | `uint32_t` Line end offset for source 2 |
| `pSource1` | `sColor*` Pointer to the foreground source |
| `pSource2` | `sColor*` Pointer to the background source |
| `pDest` | `sColor*` Pointer to the destination |
| `Width` | `uint32_t` Block width |
| `Height` | `uint32_t` Block height |

### **invalidateRect**

| Element | Details |
|:---|:---|
| **Method** | `void invalidateRect(uint16_t x0, uint16_t y0, uint16_t x1, uint16_t y1)` |
| **Description** | Marks as "dirty" all blocks covering the specified rectangular area. |
| **Parameter(s)** | |
| `x0, y0` | `uint16_t` Top-left corner |
| `x1, y1` | `uint16_t` Bottom-right corner |

### **AddBlock**

| Element | Details |
|:---|:---|
| **Method** | `bool AddBlock(uint16_t x, uint16_t y)` |
| **Description** | Prepares a pixel block in the FIFO for DMA transmission. |
| **Parameter(s)** | |
| `x, y` | `uint16_t` Block coordinates |
| **Return** | `bool` | `true` if added successfully, `false` if the FIFO is full |

### **sendDMA**

| Element | Details |
|:---|:---|
| **Method** | `bool sendDMA()` |
| **Description** | Starts the DMA transmission process for the first element in the FIFO. |
| **Return** | `bool` | `true` if the transfer started |

---

## ⚙️ Callback Methods (DMA State Machine Management)

These methods are static functions designed to be used as **interrupt callbacks** (Interrupt Service Routines - ISR). They are called by the hardware (DMA/SPI) once a transfer step is completed.

**Note on the `void* context` parameter:**
All these methods receive a `void* context` pointer. In the implementation, this pointer contains the address of the class instance (`this`). Since the functions are `static`, they cannot directly access object members. Therefore, they use this pointer to "retrieve" the instance and access class variables (such as the FIFO or the `m_Busy` status).

### **sendCASETDMAData**

| Element | Details |
|:---|:---|
| **Signature** | `static void sendCASETDMAData(void* context)` |
| **Role** | **Column data processing.** |
| **Description** | Called when the `CASET` (Column Address Set) command has been transmitted. It starts the DMA transfer of the 4 data bytes corresponding to the X coordinates (start and end). |
| **Context** | Command transfer complete interrupt. |

### **sendRASETDMACmd**

| Element | Details |
|:---|:---|
| **Signature** | `static void sendRASETDMACmd(void* context)` |
| **Role** | **Row command launch.** |
| **Description** | Called when column data has been sent. It launches the SPI command `RASET` (Row Address Set) to define the row range. |
| **Context** | Data transfer complete interrupt. |

### **sendRASETDMAData**

| Element | Details |
|:---|:---|
| **Signature** | `static void sendRASETDMAData(void* context)` |
| **Role** | **Row data processing.** |
| **Description** | Called when the `RASET` command is complete. It starts the DMA transfer of the 4 data bytes corresponding to the Y coordinates (start and end). |
| **Context** | Command transfer complete interrupt. |

### **sendRAMWRDMACmd**

| Element | Details |
|:---|:---|
| **Signature** | `static void sendRAMWRDMACmd(void* context)` |
| **Role** | **Memory write launch.** |
| **Description** | Called when row data has been sent. It launches the `RAMWR` (Write to RAM) command which prepares the screen to receive pixels. |
| **Context** | Data transfer complete interrupt. |

### **sendRAMWRDMAData**

| Element | Details |
|:---|:---|
| **Signature** | `static void sendRAMWRDMAData(void* context)` |
| **Role** | **Pixel block transfer.** |
| **Description** | Called when the `RAMWR` command is ready. It starts the mass transfer of the pixel block (the dirty framebuffer) to the screen via DMA. |
| **Context** | Write command complete interrupt. |

### **endDMA**

| Element | Details |
|:---|:---|
| **Method** | `static void endDMA(void* context)` |
| **Description** | **Transfer cycle finalization.** This method is the last step of a block. It increments the FIFO output index (`m_FIFO_out`), handles index wrap-around, and checks whether other blocks are waiting in the FIFO. If the FIFO is empty, it releases the busy state (`m_Busy = false`). |
| **Context** | Data transfer complete interrupt (last step of the block). |

---

## 📦 Data

### Private Variables

| Member | Type | Description |
|:---|:---|:---|
| `m_Width` | `uint16_t` | Screen width |
| `m_Height` | `uint16_t` | Screen height |
| `m_TabLayers` | `std::vector<cLayerBase*>` | List of active layers |
| `m_LayersChange` | `uint8_t` | Flag indicating whether layer sorting is needed |
| `m_Orientation` | `ORIENTATION` | Current orientation |
| `m_DirtyBlocks` | `uint8_t[NB_BLOC_MAX][NB_BLOC_MAX]` | Matrix of blocks to refresh |
| `m_DirtyBlockWidth` | `uint8_t` | Width of a refresh block |
| `m_DirtyBlockHeight` | `uint8_t` | Height of a refresh block |
| `m_NbDirtyBlockX` | `uint8_t` | Number of blocks in width |
| `m_NbDirtyBlockY` | `uint8_t` | Number of blocks in height |
| `m_pDirtyBlockFrame` | `sColor*` | Pointer to the block working buffer |
| `m_pFIFO` | `sFIFO_Data*` | Pointer to the FIFO structure |
| `m_FIFO_in` | `uint16_t` | FIFO write index |
| `m_FIFO_out` | `uint16_t` | FIFO read index |
| `m_FIFO_NbElements` | `volatile uint16_t` | Number of pending elements in the FIFO |
| `m_Busy` | `volatile bool` | DMA transfer in progress status |

---

<BR>

**DAD_FORGE/DSP** - Dad Design DSP Library
Copyright (c) 2024-2026 Dad Design.