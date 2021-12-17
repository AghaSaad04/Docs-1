---
template: main.html
---

![Hardware-Banner](https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-Hardware/master/img/hardware.png)

For quite some time we had the impression that the X9D(+) radio works well with high baud rates, needed for ExpressLRS.
However some users report it's not working as it should with ExpressLRS. For example:

- ExpressLRS Lua script just shows `Connecting...`
- ExpressLRS Lua script shows unstable behavior, especially on the higher packet rates, showing values different from 0:['SELECTED PACKET RATE']
- Constant Telemety Lost/Telemetry Recovered Notification.

## So what could be wrong?

It is possible that the X9D(+) radio also suffers from a slow inverter circuit that messes up the 400000 baud UART signals.

As you maybe know, for the Taranis QX7(S) you can do the [Crossfire inverter mod](https://blog.seidel-philipp.de/fixed-inverter-mod-for-tbs-crossfire-and-frsky-qx7/) or select a lower baud rate in the Hardware menu (Page 6/7) of OpenTX to fix the issue.

## Can it be fixed?

Well, the final verdict is not out yet, but the following may help to resolve the issue:

* Do a hardware mod as well on your X9D(+) as you can read all about here in [PR# 59](https://github.com/EdgeTX/edgetx/pull/59) for EdgeTX.
   * This requires changing the 10k resistor `R82` to a 1k resistor. 
* Download a OneBit_Status firmware using [this OpenTX version](https://heatermeter.com/dl/elrs/), based on OpenTX 2.3.12 and enable `ONEBIT`

<img src = "https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-Hardware/master/img/wiki/SampleMode_Normal.jpg" width = "45%" />
<img src = "https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-Hardware/master/img/wiki/SampleMode_OneBit.jpg" width = "45%" />

* Select `115200 baud` using the above OpenTX version. (ExpressLRS will lock you out on 500Hz packet rate, but below that rate should be stable now.)

The nice thing about the OpenTX build above is that it also adds two additional `Sync` lines to show the current OpenTX/UART behavior.
This will show in your Model Setup (Page 2/11) where you select the external module type (where you selected CRSF to enable ELRS):

<img src = "https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-Hardware/master/img/wiki/Sync.jpg" width = "60%" />

- The first `Sync` line shows something like `L00002R04000u`. `L` and `R` indicates the microseconds of Lag and Rate.
   - The `L` is how much OpenTX is trying to compensate to nail the rate you selected. (The closer to 0 the better)
   - The `R` is just the Packet Rate you selected using the ExpressLRS Lua script.
- The second `Sync` line shows something like `G11 C00 F00`. This stand for `G` (good packets), `C` (CRC error), `F` (Framing error). The goal is to have zero errors here. (Don't forget to power your receiver so a link is established!)

**If this has helped you in any way (good or bad) with your X9D(+) radio, let us know on the ExpressLRS discord!**