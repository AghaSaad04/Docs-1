---
template: main.html
description: ExpressLRS can limit its transmit power to the level it needs to maintain good Signal Health.
---

<img src="https://raw.githubusercontent.com/ExpressLRS/ExpressLRS-Hardware/master/img/software.png">

## Description

Dynamic Power allows the TX module to *adjust* its output power up to the configured power level using the telemetry from the RX. The TX will lower power if the RSSI dBm is far enough above the sensitivity limit and will raise power if it is not, has a low LQ, or has a sudden drop in LQ. Advanced telemetry is not required for this feature, just a non-zero Telemetry Ratio.

!!! warning "Warning"
    Dynamic Power relies on telemetry. If no telemetry is received while armed, then the power level will be kicked up to the maximum configured power level.

### Feature demo

* Lowering/Raising power on a long-range flight: https://www.youtube.com/watch?v=Ah6h-QqM6Xs
* LQ-based power boost: https://www.youtube.com/watch?v=SMOfxdzQIJY
* AUX Switch feature demo: https://www.youtube.com/watch?v=wdPWw2xu8Ig

!!! note "Note"
    These videos were taken with a test version. The power lowering/raising thresholds are different from the deployed version.

### How to configure Dynamic Power

On the ELRS Lua script v2, Select `> TX Power`. There are three configurable elements.

* `Max Power`: The output power will never exceed this power output level in any situation.
* `Dynamic`: Three options are available.
    - `Off`: Fixed power, always set to the configure `Max Power` output.
    - `On`: Dynamic power is enabled, following the logic described below.
    - `AUX9`-`AUX12`: Dynamic power is enabled only when this AUX channel is `high`, and power is fixed to the `Max Power` when `low`.
* `Fan Thresh`: Fan threshold. If the module has a fan, it will be enabled starting at this power level after a short delay.

Another important setting is to make sure your craft is **armed** on AUX1=`high` (~2000us). ELRS never knows the craft arming state. Instead, it monitors the value of `AUX1` for arming detection. See [Switch Modes](switch-config.md) for more information about AUX channels.

## Details

### Starting Power

On module powerup with Dynamic Power enabled, transmit power is set to the minimum supported power.

### Lowering Power

The algorithm averages the last few RSSI dBm readings from the RX and will compare the average with the sensitivity limit for the current packet rate. If the RSSI is far enough from the limit, the transmit power is lowered by one level. Example: 250Hz = -108dBm sensitivity limit, if the current RSSI average is above -78dBm, the power will be lowered. This can only occur once every few seconds.

### Raising Power

The opposite of the "lowering power" algorithm is also in place, to raise power as needed slowly such as when flying away on a long range flight. When the average RSSI is too close to the sensitivity limit for the current packet rate, the transmit power is raised one level. Example: 250Hz = -108dBm sensitivity limit, if the current RSSI average is less than -93dBm, the power will be raised. This can only occur once every few seconds.

In addition to the slow power ramp up, three LQ-based conditions will raise the power immediately to the maximum configured value.

1. If the LQ ever drops below a hard limit. Example: LQ of 50% is received by the TX, and the power will jump to the max.
2. If the LQ drops suddenly in a single telemetry update compared to the moving average. This is intended to react to flying behind a structure where the LQ suddenly takes a hit and is expected to drop further. Example: LQ is running 100% (as ExpressLRS do) and the TX receives a telemetry packet with 80% LQ, the power will jump to the max.
3. If telemetry is lost entirely with the arm switch high. Any time the TX is "disconnected" while armed, the power will jump to the max.


## Notes

### Minimum Recommended Telemetry Ratio

Because dynamic power relies on information coming back from the RX to know how to adjust the power, dynamic power is only available if the "Telemetry Ratio" is not set to Off. Any ratio will allow it to operate, but the algorithm is optimized around having at least 2x Link Statistics telemetry packets per second.

| Packet Air Rate | Telemetry Ratio |
|---|---|
| 500Hz | 1:128 |
| 250Hz | 1:64 |
| 200Hz | 1:64 |
| 150Hz | 1:32 |
| 100Hz | 1:32 |
| 50Hz | 1:16 |

On startup, the output power will be set to the lowest possible value. If telemetry is lost, the output power will stay at the current value until telemetry is received again. This is intended to prevent everyone's TX from blasting to max power when swapping batteries.

### OSD Power Readout

To see the current output power on the OSD, enable the `Tx uplink power` OSD element. This is only available on Betaflight starting at v4.3.0 and on INAV starting at v2.6.0.

You will also have to set the switch mode to `Wide` in the transmitter settings via the ExpressLRS Lua Script (Switch Mode can only be changed when not bound/connected to a receiver). 

### OpenTX Power Readout

Depending on your FC Firmware version, TX Power telemetry (TPWR) may not be recognized and will not be displayed on your OSD. You can instead use OpenTX/EdgeTX Telemetry readout special function so you get notified when changes in the power level have happened, as in the demo video.

* Firstly, set a logical switch to `|Δ|>x` / `TPWR` / `1mW` as shown in L04 in the picture. 

<figure markdown>
![OpenTX logical switch page, L04 is set to absolute delta equal or larger than x, TPWR, 1mW](https://cdn.discordapp.com/attachments/738450139693449258/872521918446714920/IMG_9220.JPG)
</figure>

* Next, for a readout when the power changes, set a special function triggered from the logical switch previously set, and assign `Play Value` / `TPWR` / `1x` (SF10 in the picture). When you want a readout periodically throughout a flight, choose a switch to trigger another special function, and assign `Play Value` / `TPWR` / (SF11 in the picture, with 10s interval).

<figure markdown>
![OpenTX Special function page, SF10 is set to L04, Play Value, TPWR, 1x. SF11 is set to SB1 down, Play Value, TPWR, 10s](https://cdn.discordapp.com/attachments/738450139693449258/872521921382744074/IMG_9221.JPG)
</figure>

!!! note "Note"
    OpenTX has no value for 50mW in the CRSF Telemetry protocol and instead will be read as 0mW. EdgeTX starting 2.5.0 have the proper 50mW readout.
