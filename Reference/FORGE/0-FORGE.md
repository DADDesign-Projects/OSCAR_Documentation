---
title: FORGE
parent: Technical Reference
layout: default
nav_order: 1
---
# FORGE
FORGE (Framework for Open and Resourceful Guitar Effects) is a software platform designed to empower creators to build their own digital effects pedals for guitar, voice, keyboards, and more.  

**The goal of this project** is to provide a solid and reliable foundation that handles the full complexity of embedded audio software development, covering both driver-level and middleware-level layers.  

FORGE manages all the hardware interfaces—microcontroller, audio CODEC, encoders, switches, memory devices, TFT display, USB—as well as the software modules needed for the display, user interface, parameter persistence, filter libraries, delay lines, DCO, and more.  

This leaves you free to focus on what truly matters: designing and creating the digital effects you’ve always dreamed of.   

##  Structure
The FORGE is organized as follows:


| DSP | DSP components (audio processing, filters, oscillators, etc.) |
| Drivers | Hardware drivers (audio CODEC, GPIO, memory devices, etc.) |
| Effects | Effect implementations (delay, distortion, modulation, etc.) |
| GUI | Graphical user interface (menus, widgets, UI panels) |
| PersistentStorage | Management of parameter saving/loading in non-volatile memory |
| STM_GFX2 | Graphics driver and library (ST7789, etc.) |
| Utilities | Shared utility functions (monitoring, logging, conversions, helpers) |
