---
title: Home
layout: default
nav_order: 1
---

# Welcome
Welcome to the official documentation for the FORGE framework and projects built with it :
- PENDA,
- OSCAR.

## Overview 
**FORGE** (Framework for Open and Resourceful Guitar Effects) is a software framework for STM32 microcontrollers designed to help developers build their own professional digital audio effects for guitars, basses, vocals, keyboards, and many other audio applications.

The goal of FORGE is to provide a robust, efficient, and reliable software foundation that handles the complexity of embedded audio development. It covers everything from low-level hardware drivers to middleware and high-level application components, allowing developers to focus on designing great audio effects instead of infrastructure.

The framework includes all the building blocks required to create professional digital effects:
- Hardware drivers (CODECs, GPIO, SDRAM, QSPI Flash, timers, etc.)
- DAD_GFX2 graphics library (ST7789 and other LCD controllers)
- DSP components (filters, oscillators, delay lines, mixers, envelopes, and more)
- Graphical user interface (menus, widgets, control panels, VU meters, etc.)
- PersistentStorage for preset management in non-volatile memory
- Utilities (monitoring, debugging helpers, macros, and more)
- Development and testing tools (MIDI controller, BIQUAD filter designer, etc.)

**FORGE** also includes several fully featured, production-quality audio effects. They can be used as-is, customized to fit your own projects, or studied as reference implementations to learn embedded DSP programming on STM32.

The currently available effects include:
- Reverb
- Delay
- Chorus
- Flanger
- Phaser
- Vibrato
- Tremolo
- ...with many more to come.

FORGE has already been deployed on the following hardware platforms:

OSCAR: [https://github.com/DADDesign-Projects/OSCAR_P01A01](https://github.com/DADDesign-Projects/OSCAR_P01A01)  
PENDA: [https://github.com/DADDesign-Projects/PENDA-Software](https://github.com/DADDesign-Projects/PENDA-Software)

