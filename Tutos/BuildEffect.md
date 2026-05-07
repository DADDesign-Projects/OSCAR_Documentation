---
title: How to Compile and Load an Effect
parent: Tutorials
layout: default
nav_order: 2
---

# How to Compile and Load an Effect

{: .text-blue-200 }
This tutorial explains how to compile an effect and then load it onto the OSCAR hardware platform.

## Prerequisites

**You must also have:**

* A working OSCAR pedal (hardware platform) with compatible 12V power supply to download and run the software.

* cloned the following repositories and configured the workspace in STM32CubeIDE :

  * [Software\_OSCAR\_P01A01](https://github.com/DADDesign-Projects/Software_OSCAR_P01A01)

  * \[OSCAR\_P01\_FLasherLoader]\(<https://github.com/>  DADDesign-Projects/OSCAR\_P01\_FLasherLoader)


  > See: [Workspace Tutorial](./Workspace/TutoWorkspace.md)

* If you want to trace code execution and debug the firmware, an ST-Link (or compatible clone) is required (not use in this tuto).

To program the software into the STM32H743 internal memory, you can use either:

* An ST-Link programmer/debugger

* The USB DFU protocol
{: .warning }
> **Note:**\
> The flasher client currently only works on Windows.\
> Collaboration is welcome to help port the `FlasherServer` utility to Linux or macOS.\
> Alternatively, a Python port could also be a good solution.
