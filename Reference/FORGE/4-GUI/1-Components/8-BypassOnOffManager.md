---
title: BypassOnOff
parent: GUI Components
layout: default
nav_order: 9
---

# BypassOnOff

## 📋 Description
This file contains the classes responsible for managing the state transitions of an audio effect (enable, disable, bypass, etc.).

Several state managers are provided to support different pedal hardware architectures. Each manager implements a specific state transition strategy based on the hardware resources available on the pedal and the software's ability to control these hardware components.

Depending on the hardware, a manager may control elements such as the Dry channel, the pedal bypass, or any other component involved in effect switching.

The abstract class **iBypassOnOffManager** defines the common interface and the methods that all state managers must implement.

The **cBypassOnOffManager** class implements a state manager for hardware platforms where both the Dry channel volume and the pedal bypass can be controlled by software. This is the case for the OSCAR platform.

The **cOnOffManager** class implements a state manager for hardware platforms where neither the Dry channel volume nor the pedal bypass can be controlled by software. In this case, these functions are handled entirely by the hardware. This is the case for the PENDA platform.

---

**DAD_FORGE/DSP** - Dad Design DSP Library  
Copyright (c) 2024-2026 Dad Design.