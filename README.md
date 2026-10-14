# RS41 XDATA Breakout

A small adapter board for the Vaisala RS41 radiosonde. It plugs into the 2 mm XDATA connector at the bottom of the sonde and brings all 10 signals out to a labelled 2.54 mm pin header. This makes it easy to use jumper wires, an ST-Link dongle or a breadboard.

It is meant for debugging, flashing and hacking. It is not a flight-grade connector.

## Parts

| Part | Notes |
|---|---|
| PCB | Gerbers are in this repo |
| 2x5 pin header, 2 mm pitch, 90 degree | For example LCSC C3012188 |
| 1x10 pin header, 2.54 mm pitch | Generic |

## Assembly

Solder all parts on the top side of the PCB.

The original mating connector on the sonde side is an Amphenol 89947-710LF. The 2 mm right-angle header listed above is cheaper and works fine, but the fit is a bit loose or tight depending on the batch. It may need a little jiggle to seat it in the sonde.

Pay attention to the cut-out (key) in the sonde connector when plugging in the board.

## RS41 XDATA pinout

View with the sonde lying flat, button, LED and battery side up. Note the gap in the connector.

```
 _______________________________________________________
|                                                       |
|   2           4           6           8           10  |
|                                                       |
|   1           3           5           7           9   |
|______________________           ______________________|
                      |           |
```

| RS41 pin | Signal | Use |
|---|---|---|
| 1 | GND | Ground |
| 2 | I2C2_SDA (PB11) / UART3 RX | External I2C data (Si5351, sensors). Pulse counter input. |
| 3 | I2C2_SCL (PB10) / UART3 TX | External I2C clock. Optional GPS NMEA output. |
| 4 | +VDD_MCU / PB1 | Sense line through 10k + cap + 10k. Not a power pin. |
| 5 | V_Boost / switched 3.3V | Main supply rail. Power in from ST-Link, power out to external devices. |
| 6 | VBAT | Raw battery voltage (2x AA, about 2.4 to 3.2 V). Unregulated. |
| 7 | RST | MCU reset |
| 8 | SWCLK (PA14) | SWD clock |
| 9 | SWDIO (PA13) | SWD data |
| 10 | GND | Ground |

The 2.54 mm header on the breakout carries the same signals. Use the silkscreen labels on the PCB to find each pin.

## Power pins

Only pin 5 is meant for power in or out.

- Pin 4 goes to the MCU through a resistor network. It can only be used to sense voltage.
- Pin 5 is the main supply rail of the board. It works in both directions.
- Pin 6 is the battery voltage before the boost converter. It drops as the cells drain. Do not use it to power external chips.

## Use case 1: Flashing with an ST-Link

### Cheap USB ST-Link dongle (with a 3.3V output)

Remove the batteries from the sonde. The dongle powers the board.

| ST-Link | RS41 pin |
|---|---|
| GND | 1 (or 10) |
| 3.3V | 5 |
| SWCLK | 8 |
| SWDIO | 9 |
| RST (optional) | 7 |

Cheap dongles have a weak 3.3V output. This is fine for flashing and light testing. For transmit tests, use batteries or a bench supply.

### Genuine ST-Link v2 (20-pin)

On the genuine ST-Link v2, Target VCC is a voltage sense input. It does not supply power. Keep the batteries in and switch the sonde on.

| ST-Link v2 (20-pin) | RS41 pin |
|---|---|
| 1 Target VCC | 4 |
| 4 GND | 1 |
| 7 SWDIO | 9 |
| 9 SWCLK | 8 |
| 15 RST | 7 |

## Use case 2: Powering an external device (Si5351, BMP280, ...)

| Device | RS41 pin |
|---|---|
| VCC | 5 |
| GND | 1 or 10 |
| SDA | 2 |
| SCL | 3 |

Notes:

- Pin 5 is live only while the sonde is switched on.
- Measure pin 5 with a multimeter before you connect anything. The documentation lists both 3.3V and "Boost out 5.0V" for this pin. One user in RS41ng issue #68 measured 3.8V on the Si5351 supply, with a modified 1x AA power setup.
- A bare Si5351 chip has a 3.6V maximum supply. Most Si5351 breakout boards (for example the Adafruit one) have their own regulator and accept 3 to 5 V, so they are safe.
- Do not power the Si5351 from VBAT (pin 6). The voltage falls too low as the batteries drain.
- In RS41ng, I2C and the UART NMEA output share pins 2 and 3. You can use one or the other, not both.

## References

- [RS41ng firmware and pinout](https://github.com/mikaelnousiainen/RS41ng)
- [RS41 to ST-Link v2 wiring (darksidelemm)](https://gist.github.com/darksidelemm/4c4053fa83e58b7fb4d3ffc2c84b6dfc)
- [RS41ng issue #68, Si5351 supply voltage](https://github.com/mikaelnousiainen/RS41ng/issues/68)
- [RS41 hardware documentation (bazjo)](https://github.com/bazjo/RS41_Hardware)

## License

This project is licensed under the GNU General Public License (GPL). See the LICENSE file for details.
