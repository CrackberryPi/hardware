# Crackberry Pi hardware

## The plan

The idea is to develop a complete and usable handheld system based on the Raspberry Pi. For this purpose we plan to create a PCB that will provide a small touch screen, a tiny keyboard and some other features. Together with a custom enclosure and a Lithium Ion battery this would form a complete handheld system.

![basic sketch](https://github.com/CrackberryPi/hardware/raw/master/images/basic-sketch.png "basic sketch")

(The above is a very very rough sketch only)

## Hardware functionality

### Screen

Very likely a 3.5" 480x320 resistive touch screen. A number of parties sell boards to interface such screens to the Rasberry Pi already. The screens are generally more or less exactly the same size as the Raspberry Pi.

The preliminary candidate is the [CFAF320480C4-035T-TS](https://www.crystalfontz.com/product/CFAF320480C4035TTS#pcn).

![touchscreen TFT](https://github.com/CrackberryPi/hardware/raw/master/images/CFAF320480.jpg "touchscreen TFT")

The screen has an integrated Himax HX8357-B microcontroller. It is capable of handling DPI, MIPI and SPI interfaces. The latter we will use, connected directly to the RPi headers.

The resistive touchscreen requires an additional driver IC, for which we will use the [ADS7843](http://www.farnell.com/datasheets/1822405.pdf).

LED backlight can be dimmed using pulse width modulation.

![screen circuitry](https://github.com/CrackberryPi/hardware/raw/master/images/screen.JPG "screen circuitry")

### Keyboard

Looks like we'll go for the same technology (and designer :) ) as [TOHKBD](https://www.kickstarter.com/projects/2028347278/tohkbd-the-other-half-keyboard-for-your-jolla), with the difference that the Crackberry Pi's keyboard will be narrower. The following is a preliminary key layout:

```
         !     @     #     $     %     ^     &     *     (     )     Del
  ESC   1     2     3     4     5     6     7     8     9     0     BS


         F11   F12   {     }     [     ]     _     -     +     =
  TAB     Q     W     E     R     T     Y     U     I     O     P     CR


                                         ;     :     '     " 
  Caps      A     S     D     F     G     H     J     K     L     CR  CR

    
             ~     `                       <     >     ? ,   PgUp  Ins \
  Shift       Z     X     C     V     B     N     M     .     Up    /


                                                       Home  PgDn  End
  Sym   Ctrl  Alt              Spacebar                 Left  Down  Right
```

This keyboard is a combination of a metal frame, a silicon mat, plastic keys and a dome-sticker. Here's a picture:

![keyboard tech](https://github.com/CrackberryPi/hardware/raw/master/images/keyboard-tech.jpg "keyboard-tech")

### Real Time Clock

Our circuit provide a Real Time Clock (RTC) to the Raspberry Pi. Plenty of ICs that do this. Will probably need its own miniature battery of some sort.

[MCP7940N](http://nl.mouser.com/Search/ProductDetail.aspx?R=MCP7940N-I%2fSNvirtualkeyMCP7940N-I%2fSNvirtualkey579-MCP7940N-I%2fSN)

[CM200C](http://nl.mouser.com/ProductDetail/Citizen-Finetech-Miyota/CM200C-32768KDZB-UT/?qs=sGAEpiMZZMtzFpQ%252bfsc2VHRVaru4Cxjh)

[CR2032](http://nl.mouser.com/ProductDetail/Linx-Technologies/BAT-HLD-001/?qs=sGAEpiMZZMtT9MhkajLHrnz%2fFPUZSgO1koSsSyYdFD4%3d)

[20x2 Header](http://nl.mouser.com/ProductDetail/Harwin/M20-7832046/?qs=sGAEpiMZZMs%252bGHln7q6pmzlZUuX%2f53qjvrmS12bWgrU%3d)

[ZIF 50](http://nl.mouser.com/ProductDetail/Hirose-Electric/FH12-50S-05SH55/?qs=%2fha2pyFaduiqcGVowlci21qBbMcOq%252bHO9Q2MbUNlnDgQg9ES%252bZv3cQ%3d%3d)

![rtc](https://github.com/CrackberryPi/hardware/raw/master/images/RTC.png)

### Hardware random circuit

Serious cryptographic applications on embedded hardware invariably suffer from a lack of proper sources of random entropy. We're not simply trusting the Pi SoC chip for this, but we're providing an additional avalanche noise circuit such as:

![random circuit](https://github.com/CrackberryPi/hardware/raw/master/images/random-circuit.png)

This needs to be powered by an 18V stepup circuit, possibly something like:

![18V stepup](https://github.com/CrackberryPi/hardware/raw/master/images/18V-stepup.png)

Quiescent current of circuit needs to investigated and if significant the Pi should be able to turn this on and off as random entropy will only be needed at boot time.

### Power, Lithium Ion Battery and charging circuit

A properly sized Lithium Ion battery should be sourced, and a charging circuit included on our board. We might be able to use the micro-USB power-connector on the Pi for power input, although our system should optimally include an actual on/off switch so that even security-conscious users can be certain the system is actually off when they think it is off.

This means adding an extra micro-usb port on our PCB, which is intended for charging the battery. The RPi will in turn be powered through the appropriate pin on the connector, with a switch in between. This allows the battery to charge while all other components are turned off.

Current best battery we can find is [this one](https://www.adafruit.com/products/354) from Adafruit, which fits rather perfectly and at 3.7V * 4.4 Ah would give us 16.28 Wh which at 5W gives us somewhat over 3 hrs of operation.

For the charging circuit we will use the MCP73833/4.

![Charging circuit](https://github.com/CrackberryPi/hardware/raw/master/images/charger.JPG "charging circuit")

### HAT standard

[HAT](http://www.raspberrypi.org/introducing-raspberry-pi-hats/)s are Raspberry Pi expansion boards that conform to the [standard](https://github.com/raspberrypi/hats) set by the Raspberry Pi foundation. This involves a small I2C EEPROM filled with manufacturer information as well as size constraints (which we will not meet if we include a keyboard). We should nevertheless try to comply with this standard to the extent possible. This will also cause the Pi bootloader and standard software distributions to know about our GPIO pins, meaning less chance to break something.

![EEPROM](https://github.com/CrackberryPi/hardware/raw/master/images/HAT.png)

This needs to be a CAT24C32 (32kbit, 4kbyte, same package and pin-out). By default the EEPROM is write-protected. By soldering the solder-jumper of shorting the testpads the write-protect is disabled. This will reduce the risk of accidental EEPROM resetting and breaking HAT-'compliance', but still give full freedom to the user.

Check [here](https://github.com/raspberrypi/hats/blob/master/eeprom-format.md) for further details on EEPROM programming.

## License 

All development documentation, CAD drawings, driver source code that we develop ourselves is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a> unless specified otherwise. Please talk to us is you have commercial plans with any of this: we'll probably work something out.

![CC license image](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png "CC BY-NC-SA 4.0 International")
