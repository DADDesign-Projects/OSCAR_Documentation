---
title: How to Compile FlasherLoader
parent: Tutorials
layout: default
nav_order: 3
---

{: .text-blue-100 }
# **How to Compile and Load an Effect**

{: .text-blue-300 }
This tutorial explains how to compile FlasherLoader.

> {: tip }
> About FlasherLoader\
>- The flasher utility used to transfer resource files (images, fonts, ELF executables, samples, etc.) into the external QSPI flash memory of the OSCAR P01 board
>-  The bootloader used by the pedal to launch the selected executable from the external QSPI flash memory at power-up

{: .text-blue-200 }
## Prerequisites

**You must also have:**

* Git and STM32CUBEIDE installed

* Cloned the [OSCAR\_P01\_FLasherLoader](https://github.com/DADDesign-Projects/OSCAR_P01_FLasherLoader) repositories and configured the workspace in STM32CubeIDE :

*See: [Workspace Tutorial](./Workspace/TutoWorkspace.md)*

***
# Compiling the Flasher Loader

We will now compile the Flasher Loader.

* Select the `OSCAR_P01_FlasherLoader` project.
* Build the `Release` configuration.

![](Workspace2.png)

*The FlasherLoader will now compiled*

* If everything completed successfully, a `Release` directory should appear inside your project.\\

* It contains the generated executable file `OSCAR\_P01\_FlasherLoader.elf`

***

![](Workspace3.png)
