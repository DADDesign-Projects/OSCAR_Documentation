---
title: How to Program OSCAR
parent: Tutorials
layout: default
nav_order: 4
---

{: .text-blue-100 }
# **How to Program OSCAR**

{: .text-blue-300 }
This tutorial explains how to program FlasherLoader or an effect onto the OSCAR hardware platform.

{: .text-blue-200 }
## Prerequisites

**You must also have:**

- STM32CubeProgrammer installed

- A working OSCAR pedal (hardware platform) with a compatible 12V power supply to download and run the software

- The software to be loaded already compiled:\
*See tutorials: [How to Compile Effects](./Workspace/BuildEffects.md), [How to Compile FlasherLoader](./Workspace/BuildFlasher.md)*

    * For effects: build configurations `Release`, `#Delay`, `#Reverb`, or `#Modulations`
    * For FlasherLoader: build configuration `Release`

- Optionally, an ST-Link programmer

***

{: .text-blue-200 }
## Programming OSCAR

To program the software into the STM32H743 internal memory, you can use either:

{: .text-blue-300 }
* An ST-Link programmer
* The USB DFU protocol
  
{: .text-blue-300 }
### Programming Using DFU Mode (Without ST-Link)

- Connect OSCAR to your computer using a USB cable.

- Power on OSCAR while keeping the BOOT button pressed.\
*The board will enter DFU mode.*

- Launch STM32CubeProgrammer.

- In STM32CubeProgrammer:
  - Select `USB`
  - Click `Connect`

![](Programmer1.png)

{: .text-blue-300 }
### Programming Using an ST-Link

- Before powering the board, connect your ST-Link to OSCAR using the 4-pin debug connector:

  * GND -> 0V
  * SWDIO -> DIO
  * SWCLK -> CLK

- Power on OSCAR.

- Launch STM32CubeProgrammer.

- In STM32CubeProgrammer:
  - Select `ST-LINK`
  - Click `Connect`

![](Programmer2.png)

{: .text-blue-300 }
### Programming with DFU or ST-Link

- Select the `Erasing & Programming` tab.

- Select the ELF file to program (here, FlasherLoader):\
`......\OSCAR_Workspace\OSCAR_P01_FlasherLoader\Release\OSCAR_P01_FlasherLoader.elf`

- Click `Start Programming`

![](Programmer3.png)