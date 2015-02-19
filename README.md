# Crackberry Pi hardware

## The plan

The idea is to develop a complete and usable handheld system based on the Raspberry Pi. For this purpose we plan to create a PCB that will provide a small touch screen, a tiny keyboard and some other features. Together with a custom enclosure and a Lithium Ion battery this would form a complete handheld system.

![basic sketch](https://github.com/CrackberryPi/hardware/raw/master/images/basic-sketch.png "basic sketch")

(The above is a very very rough sketch only)

## Hardware functionality

### Screen

Very likely a 3.5" 480x320 resistive touch screen. A number of parties sell boards to interface such screens to the Rasberry Pi already. The screens are generally more or less exactly the same size as the Raspberry Pi.

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


![rtc](https://github.com/CrackberryPi/hardware/raw/master/images/RTC.png)

### Hardware random circuit

Serious cryptographic applications on embedded hardware invariably suffer from a lack of proper sources of random entropy. We're not simply trusting the Pi SoC chip for this, but we're providing an additional avalanche noise circuit such as:

![random circuit](https://github.com/CrackberryPi/hardware/raw/master/images/random-circuit.png)

This needs to be powered by an 18V stepup circuit, possibly something like:

![18V stepup](https://github.com/CrackberryPi/hardware/raw/master/images/18V-stepup.png)

Quiescent current of circuit needs to investigated and if significant the Pi should be able to turn this on and off as random entropy will only be needed at boot time.

### Power, Lithium Ion Battery and charging circuit

A properly sized Lithium Ion battery should be sourced, and a charging circuit included on our board. We might be able to use the micro-USB power-connector on the Pi for power input, although our system should optimally include an actual on/off switch so that even security-conscious users can be certain the system is actually off when they think it is off.

### HAT standard

[HAT](http://www.raspberrypi.org/introducing-raspberry-pi-hats/)s are Raspberry Pi expansion boards that conform to the [standard](https://github.com/raspberrypi/hats) set by the Raspberry Pi foundation. This involves a small I2C EEPROM filled with manufacturer information as well as size constraints (which we will not meet if we include a keyboard). We should nevertheless try to comply with this standard to the extent possible. This will also cause the Pi bootloader and standard software distributions to know about our GPIO pins, meaning less chance to break something.

![rtc](https://github.com/CrackberryPi/hardware/raw/master/images/HAT.png)

This needs to be a CAT24C32 (32kbit, 4kbyte)

Check [here](https://github.com/raspberrypi/hats/blob/master/eeprom-format.md) for further details on EEPROM programming.

## License 

All development documentation, CAD drawings, driver source code that we develop ourselves is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a> unless specified otherwise. Please talk to us is you have commercial plans with any of this: we'll probably work something out.

![CC license image](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png "CC BY-NC-SA 4.0 International")
