---
title: How to Compile Effects
parent: Tutorials
layout: default
nav_order: 2
---

{: .text-blue-100 }
# **How to Compile Effects**

{: .text-blue-300 }
This tutorial explains how to compile effects for load it onto the OSCAR hardware platform.

{: .text-blue-200 }
## Prerequisites

**You must also have:**

* Git and STM32CUBEIDE installed

* Cloned  [Software\_OSCAR\_P01A01](https://github.com/DADDesign-Projects/Software_OSCAR_P01A01) repositories and configured the workspace in STM32CubeIDE :


*See: [How to Create Your Workspace](./Workspace/TutoWorkspace.md)*

***

{: .text-blue-200 }
## Compiling Effects

* Open STM32CubeIDE and select the `OSCAR_Workspace` workspace.

We will now compile the Delay effect in a version compatible with the Loader.

* Select the `Software_OSCAR_P01_A01` project.

* In the toolbar, click the small downward arrow located on the right side of the hammer icon.\
  -> The list of build configurations will open.

* Click on `_Delay4Loader` in the list.

![](Workspace1.png)

*The Delay effect will now compiled*
- If everything completed successfully, a `_Delay4Loader` directory should appear inside your project.\
- It contains the generated executable file `Delay.elf`

---

![](Workspace2.png)


Compile the other effects in the same way using the following build configurations:
* `_Modulations4Loader`
* `_Reverb4Loader`
\\
>{: .tip }
>Tip: Build Configurations:\
>* Build configurations starting with # (for example #Delay) produce standalone executables that are stored in the flash memory of the STM32H743.
>* Build configurations ending with 4Loader (for example Delay4Loader) are used to produce executables that are stored in the QSPI flash memory of the board and can be launched by the bootloader.
>* The Debug configuration is used to debug the software inside the STM32CubeIDE environment.
>* The Release configuration produces a standalone executable (similar to # configurations). The compiled effect is determined by the contents of the file: Software_OSCAR_P01A01\DAD_FORGE\Effects\@Config\EffectsConfig.h