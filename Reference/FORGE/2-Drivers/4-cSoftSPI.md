---
title: cSoftSPI
parent: Drivers
layout: default
nav_order: 4
---
# DadDrivers::cSoftSPI

**Namespace:** DadDrivers::cSoftSPI  
**Files:** cSoftSPI.h / cSoftSPI.cpp   
**Directory:** DAD_FORGE/Drivers   
**Description:** 3-wire software-emulated SPI interface with timer interrupt asynchronous transmission

---

## 📋 Class Description

The `cSoftSPI` class implements a purely software-based 3-wire SPI (Serial Peripheral Interface) without using native SPI hardware. It is designed to communicate with SPI peripherals via GPIO pins configured in output/input mode. Transmission is driven by a timer interrupt that executes a finite state machine to sequentially generate MOSI, CLK, and CS signals. This approach allows using any microcontroller without dedicated SPI modules.

---

## 🎯 Enumerations and Associated Structures

### eTransState

| Element / Variable | Type / Value (if applicable) | Description |
|:---|:---|:---|
| `Stop` | 0 | Transmission stopped, no transfer in progress |
| `Start` | 1 | Transmission start, transitioning to CS activation |
| `Cs_Down` | 2 | Chip Select activated (low), preparing data transmission |
| `Data_Change` | 3 | Data bit change on the MOSI line |
| `Clk_Up` | 4 | Clock signal rising edge |
| `Nop` | 5 | NOP state for timing delay between rising and falling edge |
| `Clk_Down` | 6 | Clock signal falling edge, checking transmission end |
| `Cs_Up` | 7 | Chip Select deactivated (high), end of transmission or next queue item |

---

## 📚 Public Methods

### **Initialize**

| Element | Details |
|:---|:---|
| **Method** | `void Initialize(GPIO_TypeDef *MOSI_GPIO, uint16_t MOSI_Pin, GPIO_TypeDef *CLK_GPIO, uint16_t CLK_Pin, GPIO_TypeDef *CS_GPIO, uint16_t CS_Pin, TIM_HandleTypeDef *phtim, uint8_t NbBit)` |
| **Description** | Initializes the software SPI interface with GPIO configurations and the timer. Configures I/O ports for MOSI (Master Out Slave In), CLK (Clock), and CS (Chip Select), as well as the MSB bit mask according to data length. Resets all states to their default values. |
| **Parameter(s)** | |
| `MOSI_GPIO` | Pointer to the GPIO port register configured for MOSI (e.g., GPIOA) |
| `MOSI_Pin` | GPIO pin number for MOSI (e.g., GPIO_PIN_4) |
| `CLK_GPIO` | Pointer to the GPIO port register configured for CLK (e.g., GPIOB) |
| `CLK_Pin` | GPIO pin number for CLK (e.g., GPIO_PIN_5) |
| `CS_GPIO` | Pointer to the GPIO port register configured for CS (e.g., GPIOC) |
| `CS_Pin` | GPIO pin number for CS (e.g., GPIO_PIN_6) |
| `phtim` | Pointer to the HAL Timer handle used for timing interrupt (e.g., &htim1) |
| `NbBit` | Number of bits to transmit (max 32, default 16) |
| **Return** | None |

### **TimerCallback**

| Element | Details |
|:---|:---|
| **Method** | `void TimerCallback()` |
| **Description** | Timer interrupt callback that executes the state machine for SPI transmission. Sequentially manages CS activation, MSB to LSB bit shifting, clock transition generation, and CS deactivation. Supports asynchronous queuing to transmit multiple data items without interrupting the main code. |
| **Parameter(s)** | None |
| **Return** | None |

### **Transmit**

| Element | Details |
|:---|:---|
| **Method** | `void Transmit(uint32_t Data)` |
| **Description** | Starts a new SPI transmission with the specified data. If a transmission is already in progress, the data is queued for the next cycle. Temporarily disables interrupts during the operation to guarantee atomicity. |
| **Parameter(s)** | |
| `Data` | Data to transmit (32 bits, only the first NbBit MSB bits will be sent) |
| **Return** | None |

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
| `m_Port_MOSI` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for the MOSI line |
| `m_Pin_MOSI` | `uint16_t` | GPIO pin number for the MOSI line (used in setMOSI/resetMOSI macros) |
| `m_Port_CLK` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for the CLK clock line |
| `m_Pin_CLK` | `uint16_t` | GPIO pin number for the CLK clock line (used in setCLK/resetCLK macros) |
| `m_Port_CS` | `GPIO_TypeDef*` | Pointer to the GPIO port register configured for the CS Chip Select line |
| `m_Pin_CS` | `uint16_t` | GPIO pin number for the CS Chip Select line (used in setCS/resetCS macros) |
| `m_phtim` | `TIM_HandleTypeDef*` | Pointer to the Timer handle used to generate periodic interrupts |
| `m_Data` | `uint32_t` | Current data to transmit (contains MSB to LSB bits) |
| `m_DataBit` | `uint32_t` | Mask of the current bit being transmitted (shifted from MSB to LSB) |
| `m_TransState` | `volatile eTransState` | Current state of the transmission state machine (stop, start, data_change, etc.) |
| `m_NextData` | `uint32_t` | Next data queued for asynchronous transmission |
| `m_ValidNextData` | `uint8_t` | Flag indicating whether m_NextData contains valid data to transmit |
| `m_MSBmask` | `uint32_t` | MSB bit mask (1 << (NbBit-1)) for initializing transmission |

---

## 💡 Usage Example

```cpp
#include "DadDrivers/cSoftSPI.h"

using namespace DadDrivers;

int main()
{
    // GPIO configuration (must be done before)
    GPIO_Init(MOSI_GPIO, MOSI_Pin, GPIO_MODE_OUTPUT);
    GPIO_Init(CLK_GPIO, CLK_Pin, GPIO_MODE_OUTPUT);
    GPIO_Init(CS_GPIO, CS_Pin, GPIO_MODE_OUTPUT);
    
    // Software SPI interface initialization
    cSoftSPI softSPI;
    softSPI.Initialize(MOSI_GPIO, MOSI_Pin,
                       CLK_GPIO, CLK_Pin,
                       CS_GPIO, CS_Pin,
                       &htim1,  // Timer configured for ~50µs per callback
                       16);     // 16-bit transmission

    // Start timer interrupt (to be done in your configuration)
    HAL_TIM_Base_Start_IT(&htim1);

    // Data transmission
    softSPI.Transmit(0xABCD);   // First transmission
    softSPI.Transmit(0x1234);   // Second transmission (queued)
    softSPI.Transmit(0x5678);   // Third transmission (queued)

    // TimerCallback() will be called automatically
    // at each period to execute the state machine

    while(1) {
        // Main code - SPI works in background
        // via timer interrupts
        __WFI();  // Wait For Interrupt
    }

    return 0;
}
```

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.
