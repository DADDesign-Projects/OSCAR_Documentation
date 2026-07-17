---
title: _MIDI
parent: Drivers
layout: default
nav_order: 11
---
# _MID
The **_MIDI directory** contains the high-level drivers required for the operation of the MIDI interface over UART and USB.

It consists of the following components:

* **cMidi**: main class handling MIDI communication management. It groups together the reception callbacks for data coming from UART and USB, a MIDI message parser, as well as a registration and callback system allowing various clients to receive the MIDI commands that concern them.

* **MIDI USB Driver**: implementation of the USB MIDI protocol for STM32H7xx microcontrollers. This driver is built upon the standard architecture of STMicroelectronics' USB Device stack, based on usbd_core, to which a custom USB MIDI class has been added. This approach ensures seamless integration with the other USB classes of the ST stack while guaranteeing compatibility with standard USB MIDI devices.

## MIDI/USB Driver Architecture

| Layer | File | Functional Role |
|:---|:---|:---|
| **User Application** |  | You implement your business logic here: reaction to received notes (OnNoteOn(), OnNoteOff()…), sending MIDI messages, LED control, synthesizer, etc. |
| &nbsp;&nbsp;&nbsp;&nbsp;**↓** | |  |
| **MIDI Interface** | usbd_midi_if.c/h | **Bridge between USB and your application**.<BR>• Provides a simple and clear API for sending MIDI messages (MIDI_SendNoteOn(), MIDI_SendControlChange()…).<BR>• Receives USB messages, decodes them (4-byte packets) and calls your callback functions (OnNoteOn(), OnControlChange()…)<BR>• This is the layer you will use most often. |
| &nbsp;&nbsp;&nbsp;&nbsp;**↓** | |  |
| **USB MIDI Class** | usbd_midi.c / .h<BR>usbd_midi_desc.c| **Core of the MIDI class**:<BR>• Implements the specific behavior of the USB MIDI protocol (Audio Class + MIDI Streaming)<BR>• Manages endpoints (Bulk IN and Bulk OUT)<BR>• Contains the USB descriptor for the MIDI interface `usbd_midi_desc.c`<BR>• Handles low-level reception and transmission of USB packets |
| &nbsp;&nbsp;&nbsp;&nbsp;**↓** | |  |
| **ST USB Stack** | usbd_core.c + HAL files | **Low-level layer provided by ST.** <BR>Manages all low-level USB protocol: enumeration, endpoint management, transfers, interrupts, etc. |
| &nbsp;&nbsp;&nbsp;&nbsp;**↓** | |  |
| **Global Initialization** | usb_midi_device.c / .h | **Single entry point for the driver.**<BR>Contains the MX_USB_MIDI_DEVICE_Init() function that must be called during initialization.<BR>It orchestrates initialization in the correct order:<BR>1. USB core initialization<BR>2. MIDI class registration<BR>3. Application interface registration<BR>4. USB device startup |