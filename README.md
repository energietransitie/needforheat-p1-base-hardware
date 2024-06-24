# NeedForHeat P1-BASE hardware 

This repository contains the open hardware design files for the NeedForHeat P1-BASE device, which can be used as a [CoreInk BASE extension module](https://docs.m5stack.com/en/coreink/proto_base), on the back of an [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink) device. 

The NeedForHeat P1-BASE enables reading [smart meters that adhere to the Dutch Smart Meter Requirements](https://github.com/energietransitie/dsmr-info) via the [P1 port](https://nl.wikipedia.org/wiki/P1-poort).

<img src="./images/pcb.jpg" width="600"  />

## Table of contents
- [NeedForHeat P1-BASE hardware](#needforheat-p1-base-hardware)
  - [Table of contents](#table-of-contents)
  - [General info](#general-info)
  - [Producing](#producing)
    - [Printed Circuit Board](#printed-circuit-board)
    - [Enclosure](#enclosure)
  - [Deploying](#deploying)
    - [Connecting the device](#connecting-the-device)
    - [Setting the DIP Switches](#setting-the-dip-switches)
  - [Developing](#developing)
    - [Printed Circuit Board](#printed-circuit-board-1)
  - [Features](#features)
  - [Status](#status)
  - [License](#license)
  - [Credits](#credits)

## General info
This repository contains the open hardware designs files for the NeedForHeat P1-BASE. It also includes a `docs` folder with recent printouts of the [schematics](./docs/needforheat-p1-base-hardware-sch.pdf) and [PCB layout](./docs/needforheat-p1-base-hardware-pcb.pdf). 

For the associated firmware that you can run on this device, please see [this repository](https://github.com/energietransitie/needforheat-p1-reader-firmware).

## Producing

### Printed Circuit Board
To manufacture the printed circuit board you can use various PCB services. 

The folder [pcb/jlcpcb](./pcb/jlcpcb) includes all exported files needed to have the PCBs manufactured by [JLCPCB](https://www.jlcpcb.com). Upload the [zipped gerber files](./pcb/jlcpcb/assembly/GERBER-needforheat-p1-base-hardware.zip) to the [JLCPCB quote page](https://cart.jlcpcb.com/quote), select the amount of PCBs and a colour for the silkscreen. All other options can be left on default. 

If you do not want to solder components on the PCB yourself, select `PCB assembly` and JLCPCB will do it for you (even for through-hole components if you so desire). This will take you to a page where the BOM and POS file can be uploaded. Use the files [BOM-needforheat-p1-base-hardware.csv](./pcb/jlcpcb/assembly/BOM-needforheat-p1-base-hardware.csv) and [CPL-needforheat-p1-base-hardware.csv](./pcb/jlcpcb/assembly/CPL-needforheat-p1-base-hardware.csv).

JLCPCB provides detailed steps on their website to select component assembly, including wave soldering. For wave soldering or through-hole component assembly, specify this requirement when uploading your BOM and POS files. 

> After uploading BOM and CPL, you may be asked to select a component for Q3. You do not need to do so and can select 'do not place. For more information, see the [design document](docs/documentation.md#optional-components)





### Enclosure

An enclosure for this device has not been developed yet.

<!-- an enclosure has not been developed yet
To fabricate the enclosure you can use your own 3D printer or use a 3D printing service. 

<img src="./images/enclosure.jpg" height="600" />

The folder [enclosure/fabrication](./enclosure/fabrication) contains exported STL files for the [case](./enclosure/fabrication/twomes-hardware-repository-template-case.stl) and [lid](./enclosure/twomes-hardware-repository-template-lid.step) of the NeedForHeat P1-BASE enclosure. The STL files can be imported into any slicer and turned into G-Code for a 3D printer.
-->

## Deploying

To deploy to P1-BASE to a home, in addition to the P1-BASE PCB and associated enclosure, you need addional hardware. We list these below, with links to the suppliers we used before:
* [M5Stack CoreInk](https://www.tinytronics.nl/nl/platformen-en-systemen/m5stack/e-paper/m5stack-m5core-ink-met-1.54-inch-e-paper-e-ink-display-esp32) device
* [RJ12 cable (male-male; 6P6C; straight)](https://www.inline-info.com/en/products/cable/tae-isdn-modular-telephone/rj10111245-modular-cable/8637/inline-modular-cable-rj12-male-to-male-6p6c-0.5m)<br>*The shorter the cable the better: this allows users to simply click in the RJ12 connector to the P1 port of their smart to leave and leave the NeedForHeat smart meter reader device dangling from the P1-port;*
* [USB-C power cable and 230V adapter]();<br>This is only needed for users who have a [smart meter adhering to DSMR3.0 or older](https://github.com/energietransitie/dsmr-info/blob/main/dsmr-e-meters.csv).

### Connecting the device

Assuming that the PCB is fully assembled, the device has an enclosure, and the device has been programmed with the [needforheat-p1-reader-firmware](https://github.com/energietransitie/needforheat-p1-reader-firmware), you can proceed and connect the device as follows:

1. **2x 8male pin header (`J1` on the PCB)**<br> Connect to the M5Stack CoreInk MI-BUS, ensuring alignment of the PCB outline with theM5Stack CoreInk outline.
2. **Upper female RJ12 connector (`J2` on the PCB)**<br> Connect to the smart meter using an RJ12 cable (male-male; 6P6C; straight).
3. **Lower female RJ12 Connector(`J3` on the PCB)**<br> (Optional) Connect another smart meter raeder.

### Setting the DIP Switches

The hardware version v2.7 (2024-06-07) features 3 DIP switches on the PCB to control data request line management. Adjust the switches according to the following settings before delivering the device to the user:

| DIP-switch Number | ON Position                                        | OFF Position                                          |
| ----------------- | -------------------------------------------------- | ----------------------------------------------------- |
| 1                 | Data requested continuously (DSMR 4 & 5)           | Data not requested continuously                       |
| 2                 | Data request driven by M5Stack CoreInk             | Data request from M5Stack CoreInk ignored             |
| 3                 | Data request driven by external smart meter reader | Data request from external smart meter reader ignored |

If multiple DIP switches are ON simultaneously, data is requested if any corresponding source requires it.

 
## Developing

### Printed Circuit Board
To change the hardware design of the PCB, you need:
* [KiCad version 8.0.0 or later](https://www.kicad.org/download/) installed to change te PCB design. 

The KiCad source files of the PCB can be found in the folder [pcb](./pcb).

To convert the PCBs into a format suitable for fabrication, consult the webpage of your PCB manufacturer of choice. For example, see the [JLCPCB guide on how to export Gerbers](https://support.jlcpcb.com/article/149-how-to-generate-gerber-and-drill-files-in-kicad) and the  [JLCPCB guide how to export the BOM and POS files](https://support.jlcpcb.com/article/84-how-to-generate-the-bom-and-centroid-file-from-kicad). You may also use a KiCad plug-in for this purpose such as [kicad-jlcpcb-tools](https://github.com/Bouni/kicad-jlcpcb-tools).

<!-- 
### Enclosure
To change the hardware design of the enclosure, you need either:
* [Autodesk Fusion 360](https://www.kicad.org/download/) installed (Autodesk provides 30 day free trials and [free one-year educational access](https://www.autodesk.com/education/edu-software/overview?sorting=featured&filters=individual) to its products and services for eligible students, teachers and research staff); 
* or [FreeCAD](https://www.freecadweb.org/), an open source alternative.

The source files of the enclosure can be found in the folder [enclosure](./enclosure). We include both .f3d source files and .step source files we obtained after conversion.
-->

## Features

The NeedForHeat P1-BASE includes the following main hardware components:

* Built-in P1-splitter for connecting an additional smart meter reader to the lower female RJ12 connector (`J3`).
* Integrated [Schmitt trigger](https://en.wikipedia.org/wiki/Schmitt_trigger) to reduce signal noise.
* 3 DIP-switches on the PCB for enabling/disabling data request line management.
* Bottom-side 2x8 pin header for MI-BUS connection to the [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink) (requires soldering yourself, or a hefty JLCPCB fee for bottom-side hand-soldering).

To-do:

* Test compatibility with Landis+Gyr E360 series smart meters (e.g., [Landis+Gyr CD2D](https://www.landisgyr.eu/), [CM3D](https://www.landisgyr.eu/)).
* Consider using 'upside-down' RJ12 female connectors for easier [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink) screen readability.
* Evaluate relocating and optimizing the 2x8 pin header from the bottom side to the top side of the PCB to reduce manufacturing costs. 
  * > (This would also obviate the need for 'upside-down' RJ12 female connectors, above.)
* Refine PCB design to reduce trace lenghts where possible.
* Experiment with alternative data request management methods (e.g., MOSFETs, BJTs), which would allow the [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink) to check and control data request management in software.
* Implement low-power LEDs for indicating power status, data request status, and data flow.
* Develop energy-efficient charging strategies for the [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink) 390mAh LiPo battery from DSMR 4 and DSMR 5 smart meters, without triggering the fold-back current limiting feature of some smart meters.
* Design a 3D-printable enclosure compatible with the [M5Stack CoreInk](https://docs.m5stack.com/en/core/coreink), potentially using [LEGO connector pens](https://www.blokjeskoning.nl/product/pen-stroef-pen-stroef-zwart/). A preliminary version can be found in the [needforheat-boiler-monitor-hardware repository](https://github.com/energietransitie/needforheat-boiler-monitor-hardware).


## Status
Project is:  _in progress_

## License
The hardware designs in this repository are available under the [CERN-OHL-P v2 license](./LICENSE), Copyright 2024 [Research group Energy Transition, Windesheim University of Applied Sciences](https://windesheim.nl/energietransitie)

## Credits
This open hardware design is a collaborative effort of:
* Rick de Graaf · [@277r](https://github.com/277r) 
* Gerwin ten Have · [@GerwintenHave](https://github.com/GerwintenHave) 

Product owners:
* Henri ter Hofte · [@henriterhofte](https://github.com/henriterhofte) · Twitter [@HeNRGi](https://twitter.com/HeNRGi)
* Marco Winkelman · [@MarcoW71](https://github.com/MarcoW71)
* 
We use and gratefully acknowlegde the efforts of the makers of:
* [KiCad Libraries](https://kicad.github.io/), by the KiCad Development Team, licensed under [an adapted version of the CC-BY-SA 4.0 License](https://www.kicad.org/libraries/license/)