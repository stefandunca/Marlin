# My configuration

## Board

Version: `Creality3D v1.1.4`
Photo: `AF1QipPjR8s5onqQ2QEIpM279wBy-mNP68LSRhLl5iHK`

## Info

[Original Bootloader](https://gauravsohoni.wordpress.com/2020/08/25/ender-3-pro-firmware-upgrade-using-raspberry-pi/)

[Reference pinout](https://all3dp.com/2/ender-3-with-marlin-how-to-install-marlin-firmware-on-your-ender-3/)

Steps:

- Select Board as `Sanguino`, Processor as `ATMega1284` or `ATMega1284P (16 MHz)`.


```bash
# Copy config for the board
meld ../Configurations/config/examples/Creality/Ender-3\ Pro/CrealityV1 Marlin/
```

## Firmware Update

Ender Header Connection:
Connected: `AF1QipNy8ihsHkiOi6hvgFXawgp5naka6CbVaFTcoqyH`
First row: `AF1QipN01QQzSlIvyEebCEWuOMbvfE3iiHpd8mLd3dVU`
    - Gnd,   MOSI,   5V
    - Brown, Orange, Green
    - R#3     R#9    R#2
Second row: `AF1QipME5IFwRDeWh2GwTN1afZJ245hPlrWxLBK-M3Qj`
    - Reset,        SCK,        MISO
    - Black Narrow, Black Wide, Yellow
    - L#7           L#8         R#8

Append to `avrdude.conf`:

```conf
#------------------------------------------------------------
# Mine, from: https://gauravsohoni.wordpress.com/2020/08/25/ender-3-pro-firmware-upgrade-using-raspberry-pi/
#------------------------------------------------------------
programmer
id = "pi_1";
desc = "Use the Linux sysfs interface to bitbang GPIO lines";
type = "linuxgpio";
reset = 27;
sck = 22;
mosi = 24;
miso = 23;
;
```

RPI `pinout`
```ascii
,--------------------------------.
| oooooooooooooooooooo J8     +====
| 1ooooooooooooooooooo  PoE   | USB
|  Wi                    oo   +====
|  Fi  Pi Model 3B+ V1.3 oo      |
|        ,----.               +====
| |D|    |SoC |               | USB
| |S|    |    |               +====
| |I|    `----'                  |
|                   |C|     +======
|                   |S|     |   Net
| pwr        |HDMI| |I||A|  +======
`-| |--------|    |----|V|-------'

Revision           : a020d3
SoC                : BCM2837
RAM                : 1024Mb
Storage            : MicroSD
USB ports          : 4 (excluding power)
Ethernet ports     : 1
Wi-fi              : True
Bluetooth          : True
Camera ports (CSI) : 1
Display ports (DSI): 1

J8:
   3V3  (1) (2)  5V    
 GPIO2  (3) (4)  5V    
 GPIO3  (5) (6)  GND   
 GPIO4  (7) (8)  GPIO14
   GND  (9) (10) GPIO15
GPIO17 (11) (12) GPIO18
GPIO27 (13) (14) GND   
GPIO22 (15) (16) GPIO23
   3V3 (17) (18) GPIO24
GPIO10 (19) (20) GND   
 GPIO9 (21) (22) GPIO25
GPIO11 (23) (24) GPIO8 
   GND (25) (26) GPIO7 
 GPIO0 (27) (28) GPIO1 
 GPIO5 (29) (30) GND   
 GPIO6 (31) (32) GPIO12
GPIO13 (33) (34) GND   
GPIO19 (35) (36) GPIO16
GPIO26 (37) (38) GPIO20
   GND (39) (40) GPIO21
```

Flash bootloader:

```bash
# Test connection
sudo avrdude -p atmega1284p -C ~/avrdude.conf -c pi_1 –v
# Flash bootloader + firmware
sudo avrdude -p atmega1284p -C ~/avrdude.conf -c pi_1 -v -U flash:w:/home/pi/firmware/builds/2.0.7.2/Marlin.ino.with_bootloader.sanguino.hex:i

# Flash firmware
avrdude -v -patmega1284p -carduino -P/dev/ttyUSB0 -D -Uflash:w:/home/human/Downloads/tmp/firmware.hex:i
```
