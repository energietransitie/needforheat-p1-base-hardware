# P1-BASE-hardware Documentation

## Usage
<img src="../images/pcb.jpg" width="600"  />

<b>This guide assumes that the PCB's are fully assembled, that the device has an enclosure and that the device has been programmed with the [needforheat-p1-reader-firmware](https://github.com/energietransitie/needforheat-p1-reader-firmware) 
</b>

The J1 connector can be connected to the M5Stack CoreInk. It should be connected so that the pcb of the P1-BASE lines up with the M5Stack CoreInk. 
The smart meter that is to be read out can be connected to the P1-BASE with an inline straight RJ12 cable. This cable must be connected to the J2 connector.
The J3 connector allows another smart meter reading device to be connected. This allows the other device to also read data from the connected smart meter.

Once these connections have been made, the user can open the NeedForHeat application on their phone and scan a QR code from the E-Ink screen of the M5Stack CoreInk. 

## Design choices
<img src="../images/schematic.PNG" width=600> </img>
The P1-BASE-hardware design has been made as an improvement to the [twomes-p1-gateway-hardware](https://github.com/energietransitie/twomes-p1-gateway-hardware) The hardware was developed to work on the M5Stack CoreInk instead of a bare ESP32 Pico d4. The hardware has also been changed to work on more smart meters and an internal splitter has been added, which allows another smart meter reading device to be connected.

#### Smart meter connection
The smart meter can be connected with connector J2. A table of connections can be seen below 
| J2 pin number | function|
|---|---|
| 1 | 5V output from smart meter |
| 2 | Data request pin from smart meter |
| 3 | Data ground |
| 4 | Not connected |
| 5 | Data out |
| 6 | Ground |

When the data request pin is connected to +5V, the smart meter sends data. the smart meter features an open collector output between data out and data ground. 
When the device sends a logical '1', current can flow through the collector. By connecting data out to a voltage source with a resistor in between, voltage levels get created based on the signal received.
In the previous design, a 10kOhm resistor was chosen, the voltage source was 3.3V. Because of this design choice, only 0.33mA could flow through the circuit, this caused a very high RC time constant when connected to some smart meters. Data could not be read out properly because of this.

In the new design, the choice has been made to let 5mA flow using a 5V source and a 1K resistor.
the output of this signal is connected to a Schmitt-trigger (IC1). This improves noise immunity.
Q1 and Q2 form a level-shifter circuit that allows the 3.3V output from the M5Stack CoreInk to give a 5V output to the smart meter. This signals that data can be sent from the smart meter to the P1-BASE.

#### Internal Splitter
In the original design there wasn’t an internal splitter. Instead of this “the client” would get a package with pre-wired items. This was both messy and confusing for “the client”. This is why it has been decided to add an internal splitter to this design. 
To design the splitter there are some aspects that were considered beforehand. One was how the other  device (an external device that is not ours) could be connected to the splitter to either receive or not receive information. Normally this wouldn’t be much of an issue and the only problem would be when the other device was connected through ground. This would result in the data port being stuck as a logical 0 and the other device not being able to request data. For this design, because of the  connection issue, diodes were added. In this way  the current cannot go to the ground while not restricting the device from requesting data. 
Another issue is that device should be allowed to send a data request to the smart meter. A device that can ask for information is called a master, and a device that cannot request information is called a slave. The master or slave status of a device may  change depending on if the device needs input or should sent input to another device in order to be able for a device to change status, a dip switch was added where slipping the switch would make a device switch between being a master or a slave. One of the switches though is used to ensure that the smart meter is always able to send data regardless whether the device is requesting it or not. In this image below the 5V_METER is the switch for the smart meter always sending information. The EXT_DATA_REQ is the external device and the DATA_REQ is our own device. The METER_DATA_REQ goes to the smart meters data request line. 

![image](https://github.com/energietransitie/needforheat-p1-base-hardware/assets/159789931/4d5e910e-4a6b-48e6-9d21-6f5d7cf72989)



#### Important information 
* R7 and Q3 should not be assembled on the PCB. These are included in the PCB in case the optocoupler (U1) is not fast enough for the signals
