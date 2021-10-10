---
template: main.html
---

![Setup-Banner](https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-hardware/master/img/quick-start.png)

## Flashing via Wifi 

Target: `MATEK_2400_RX_via_WIFI`

[Wire up your receiver](../../quick-start/rx-fcprep/#mateksys-r24-d-and-r24-s) to a free UART on your Flight Controller. Wire TX on receiver to an RX pad on the FC, and the RX on receiver to a TX pad on the FC in the same UART. Wire 5v and Gnd as normal (5v to a 5v pad on FC and Gnd to a Gnd pad on the FC).

*Note: There are Flight Controllers that will pull the RX pads `LOW` which will put the ESP-based receivers into `Bootloader Mode` unintentionally. A solid LED light on these receivers even with the TX module off is a sign they are in Bootloader Mode. If this is the case, rewire the receiver to a different UART.*

**Build** the firmware using the ExpressLRS Configurator using the correct Target and [options](../../quick-start/firmware-options). Once done, it should open a new window where the `MATEK_2400_RX-<version>.bin` is. Do not close this window so you can easily navigate to it once it's time to upload the firmware into the receiver.

Power your Flight Controller by either connecting a LiPo or attaching the USB cable (if receiver gets powered from USB via a 4v5 pad). Receiver's LED will blink slow at first, and after 20s or 30s (can be adjusted via ExpressLRS Configurator using `AUTO_WIFI_ON_INTERVAL`), it should blink fast indicating it's on Wifi Hotspot Mode.

Connect to the Wifi Network the receiver has created. It should be named something like `ExpressLRS RX` with the same `expresslrs` password as the TX Module Hotspot. Navigate to the same web address as the TX Module (usually http://10.0.0.1). The Firmware upload page should load, and using the File Upload Form, navigate where the correct Receiver `MATEK_2400_RX-<version>.bin` is (you can drag-and-drop the firmware file into the form field or use the `Browse` or `Choose File` button). Click on **Update** button and the firmware file will be uploaded and the update process should commence. 

A white page should load momentarily with the message **Update Success! Rebooting...**. Wait a little bit (**you can wait until the LED on the Receiver starts to blink slowly again**) and the receiver should be updated. Power cycle and your module and receiver should now be bound (given you have updated the Tx Module as well, and that they have the same binding phrase and options).

## Flashing via Passthrough

Target: `MATEK_2400_RX_via_BetaflightPassthrough`

[Wire up your receiver](../../quick-start/rx-fcprep/#mateksys-r24-d-and-r24-s) to a free uart in your Flight Controller. Wire TX on receiver to an RX pad on the FC, and the RX on receiver to a TX pad on the FC in the same UART. Wire 5v and Gnd as normal (5v to a 5v pad on FC and Gnd to a Gnd pad on the FC).

Power your FC with a LiPo, or if receiver is powered via USB (receiver is connected to a 4v5 pad), connect the FC to your USB port.

*Note: if the receiver has solid LED light but is not bound to your module, your FC is probably pulling the current UART's RX pad `LOW` which will interfere with the normal and passthrough flashing of this receiver. Find another UART and wire your receiver there instead.*

Using the ExpressLRS Configurator, with the correct Target selected and [Firmware Options](../../quick-start/firmware-options) set, click on **Build & Flash**. Wait for the process to finish and you should be greeted with the "Success" banner.

Unplug USB and/or LiPo. Power your TX Module and then your FC to verify you are bound and has connection.

## Flashing via UART

Target: `MATEK_2400_RX_via_UART`

Wire the receiver into the FTDI, with TX on receiver connected to the Rx on the FTDI, and RX on receiver connected to the Tx of the FTDI. Wire 5V and GND of the FTDI to 5V and GND of the Receiver. Press the button while powering the RX on, and release - the LED should now be solid.

Select the target and set your [Firmware Options](../../quick-start/firmware-options) and once done, click on **Build and Flash**.