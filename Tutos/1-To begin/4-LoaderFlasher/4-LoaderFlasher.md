---
title: How to Use FlasherLoader
parent: To Begin
layout: default
nav_order: 5
---

# How to Use FlasherLoader

{: .text-blue-300 }
This tutorial explains the purpose of the `FlasherLoader` utility and how to use it within the OSCAR ecosystem.

## Why FlasherLoader Exists

Executable FORGE programs require resource files such as fonts, images, samples, and other assets. Without these resources, the effects cannot operate correctly.

Before running any effect, all required resources must therefore be loaded into the external QSPI flash memory of the OSCAR platform.



## The FlasherLoader

`FlasherLoader` has two main functions:

* **Flasher**  
  In collaboration with **OSCAR_Flasher_Server**, the FlasherLoader receives files through the USB connection and stores them into the OSCAR platform's QSPI flash memory.

* **Loader**  
  When the OSCAR platform powers on, the FlasherLoader loads an executable ELF file into RAM and starts its execution.



## The Flasher Server

**OSCAR_Flasher_Server** is a utility program that runs on your computer. It allows you to transfer files through a COM port over the USB connection linked to the OSCAR or PENDA pedal.

![](FlasherUI.png)

For **Windows** users, an executable file named **OSCAR_FLASHER_Server.exe** is located in directory:
* for OSCAR `...\FORGE_Workspace\OSCAR_P01_FlasherLoader\@FlasherServer\`,
* for PENDA `...\FORGE_Workspace\PENDA_FlasherLoader\@FlasherServer\`.

For **Linux** or **mac** users, the OSCAR_Flasher_Server utility is available as a Python version called **OSCAR_PY_Flasher_Server.py**. To install this version, please open the [DADDesign-Projects/OSCAR_PY_Flasher_Server](https://github.com/DADDesign-Projects/OSCAR_PY_Flasher_Server) repository and follow the instructions.


## Preparation of Files by the Server Processor

The Flasher Server preprocesses some file types to simplify their use inside OSCAR effects:

**Image Files**
Supported formats include:

* JPG
* PNG
* GIF
* and more...

Image files are automatically converted to RAW format, making them directly compatible with the `DAD_FORGE/STM_GFX2` graphics library.

**ELF Effect Files (Executables)**
ELF executables undergo processing: they are parsed, processed, and reformatted for easy use by the OSCAR Loader.

**Other Files**
All other file types are transferred without modification.

---

## Prerequisites

You must have:

* A working OSCAR (12V) PENDA(9v) pedal with a compatible power supply.
* **FlasherLoader** already programmed into the STM32 internal flash memory.
* For Linux or Mac users, **OSCAR_PY_Flasher_Server.py** must be installed.

See tutorials:

* [How to Compile FlasherLoader](../BuildFlasher/BuildFlasher.html)
* [How to Program OSCAR](../ProgramOSCAR/ProgramOSCAR.html)
* [DADDesign-Projects/OSCAR_PY_Flasher_Server](https://github.com/DADDesign-Projects/OSCAR_PY_Flasher_Server)


## Using FlasherLoader
### Installation 

### Start the Flasher Server

On your computer, launch:
**OSCAR_Flasher_Server.exe** or **OSCAR_PY_Flasher_Server.py** 

Connect the OSCAR/PENDA pedal to your computer using a USB cable. Power on the pedal while holding **Footswitch 1** pressed. This disables automatic booting and displays the FlasherLoader interface.

---

## OSCAR/PENDA FlasherLoader Interface
The interface contains two sections:
![](FlasherLoader.png)

### Top Section: Flasher

Displays the current flashing status and transfer progress.

### Bottom Section: Loader

Displays:

* The executable files currently stored in QSPI flash memory
* The executable selected to start automatically at power-up

---

## Detecting the OSCAR Pedal

In `OSCAR_Flasher_Server`:

1. Click **Refresh**
2. A new COM port should appear
3. Select the new COM port

The information window should display:

```text
OK : OSCAR is detected on COMxx
```

---

## Adding Resource Files

FORGES effects require at minimum all resource files located in `../DAD_FORGE/@Ressources`

![](Ressources.png)

1. in Flasher Server Click **Add File**
2. Open: .../DAD_FORGE/@Ressources/bin
3. Select all files
4. Click **Open**

The files will appear in the flashing list.


1. Click **Add File**
2. Open:.../DAD_FORGE/@Ressources/Images
3. Select all files
4. Click **Open**

The files will appear in the flashing list.

---

## Adding Effect Executables

Before continuing, the effects must be compiled using the following build configurations:

* `_Delay4Loader`
* `_Reverb4Loader`
* `_Modulations4Loader`

See tutorial: [How to Compile Effects](../BuildEffects/BuildEffects.html)

Add the executable files:

### Delay
1. Click **Add File**
2. Open:.../_Delay4Loader
3. Select file Delay4L.elf 
4. Click **Open**

### Reverb
1. Click **Add File**
2. Open:.../_Reverb4Loader
3. Select file Reverb4L.elf
4. Click **Open**

### Modulations
1. Click **Add File**
2. Open:.../_Modulations4Loader
3. Select file Modulations4L.elf 
4. Click **Open**

## Flashing the Files

Click the **Flash** button to start the transfer.

On the OSCAR/PENDA pedal:

1. All existing files in QSPI flash memory are erased
2. File transfer and storage begin
3. Progress is synchronized with the Flasher Server
4. Used and remaining flash memory sizes are displayed in real time

Once all files are written:

* The FlasherLoader verifies the data
* The screen displays: **Flash OK**


## OFSF Files

You can save an entire file list into a single `.ofsf` file by clicking **Save Files**. Later, you can simply add this OFSF file to the flashing list instead of manually selecting all files again.

Advantages:

* Faster workflow
* Fewer manipulation errors
* Easy project archiving
* Easy deployment of complete standalone configurations

---

## Selecting the Executable to Launch

On the OSCAR pedal:

* Rotate encoder **M** to browse executable files stored in QSPI flash
* Press the encoder **M** to select the executable that will launch automatically at power-up

---

## Launching an Effect

Power cycle the pedal or press the **RESET** button. The selected effect should start automatically.

---

## Returning to FlasherLoader Mode

If you wish to:

* Change the executable to launch
* Transfer new files

You must hold **Footswitch 1** during power-up. The executable launch is cancelled, and the FlasherLoader interface appears again.

---

## 🎉Congratulations

You have successfully programmed your OSCAR/PENDA pedal with its effects.

🎸 Now it is time to make music :)

Naturally, you will probably want to:

* Modify existing effects
* Create completely new ones

The DAD_FORGE library were designed to simplify development by handling hardware complexity and providing many helpers:

* Graphics (GFX)
* GUI tools
* DSP helpers
* Resource management
* Hardware abstraction

{: .note}
>🎶 This allows you to focus on what matters most:  
>**Designing and creating the digital audio effects you have always dreamed of**.