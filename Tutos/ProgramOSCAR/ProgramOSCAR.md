---
title: How to Program OSCAR
parent: Tutorials
layout: default
nav_order: 4
---

{: .text-blue-100 }
# **How to Program OSCAR**

{: .text-blue-300 }
This tutorial explains how to program the FlasherLoader or an effect onto the OSCAR hardware platform.

{: .text-blue-200 }
## Prerequisites

**You must also have:**

- STM32CUBEProgrammer installed

- A working OSCAR pedal (hardware platform) with compatible 12V power supply to download and run the software.

- Compiled the software to be loaded:\
*See tutorials : [How to Compile Effects](./Workspace/BuildEffects.md), [How to Compile FlasherLoader](./Workspace/BuildFlasher.md)*
    * For effects: compilation option Release, #Delay, #Reverb, or #Modulations.
    * For the Flashloader: compilation option Release.\

- Optional An ST-Link programmer

***

{: .text-blue-200 }
## Programming OSCAR
To program the software into the STM32H743 internal memory, you can use either:

* An ST-Link programmer,
* The USB DFU protocol.
{: .text-blue-300 }
### Programming Using DFU Mode (Without ST-LINK)

- Connect OSCAR to your computer using a USB cable.

- Power on OSCAR while keeping the BOOT button pressed.\
*The board will enter DFU mode*.
- Launch STM32CubeProgrammer.
- In STM32CubeProgrammer:
  - Select `USB`
  - Click `Connect`

  
  ![](Programmer1.png)

{: .text-blue-300 }
### Programming Using an ST-Link

- Before powering the board, connect your ST-Link to OSCAR using the 4-pin debug connector and connect:
  * GND -> 0V
  * SWDIO -> DIO
  * SWCLK -> CLK
- Power on OSCAR.
- Launch STM32CubeProgrammer.
- In STM32CubeProgrammer, select `ST-LINK`
- Click `Connect`


![](Programmer2.png)

{: .text-blue-300 }
### DFU or ST-LINK

- Select the `Erasing & Programming` tab.
- Select the ELF file to program (Here, the FlasherLoader.):\
`......\OSCAR_Workspace\OSCAR_P01_FlasherLoader\Release\OSCAR_P01_FlasherLoader.elf
`
- Click `Start Programming`
  

![](Programmer3.png)
