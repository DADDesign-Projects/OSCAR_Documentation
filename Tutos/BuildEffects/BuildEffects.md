---
title: How to Compile Effects
parent: Tutorials
layout: default
nav_order: 2
---

# How to Compile Effects

{: .text-blue-300 }
This tutorial explains how to compile effects and load them onto the OSCAR hardware platform.

## Prerequisites

**You must have the following installed and configured:**

*   Git and STM32CubeIDE.
*   The [Software_OSCAR_P01A01](https://github.com/DADDesign-Projects/Software_OSCAR_P01A01) repository cloned, with the workspace properly configured in STM32CubeIDE.

*See: [How to Create Your Workspace](./Workspace/TutoWorkspace.md)*

***

## Compiling Effects

1.  Open STM32CubeIDE and select the `OSCAR_Workspace`.
2.  We will now compile the Delay effect using a configuration compatible with the Loader.
3.  Select the `Software_OSCAR_P01_A01` project.
4.  In the toolbar, click the small downward arrow located next to the hammer icon (Build/Configure). This will open the list of build configurations.
5.  Click on `_Delay4Loader` in the list.

![](Workspace1.png)

The Delay effect is now compiling.

*   If the compilation completes successfully, a directory named `_Delay4Loader` should appear inside your project structure.
*   This directory contains the generated executable file: `Delay.elf`.

---

![](Workspace2.png)

You can compile the other effects in the same manner using these build configurations:

*   `_Modulations4Loader`
*   `_Reverb4Loader`

{: .tip}
> **Tip: Understanding Build Configurations**
>
> * Use configurations starting with **#xxx** (e.g., `#Delay`) to generate standalone executables that are stored in the internal flash memory of the STM32H743.
> * Configurations ending with **4Loader** (e.g., `Delay4Loader`) produce executables designed for storage in the board's QSPI flash memory, which are then launched by the bootloader.
> * The **Debug** configuration is used to debug the software directly within the STM32CubeIDE environment.
> * The **Release** configuration produces a standalone executable (similar to the `#xxx` configurations). The final compiled effect version is determined by the contents of this file: `Software_OSCAR_P01A01/DAD_FORGE/Effects/@Config/EffectsConfig.h`.
