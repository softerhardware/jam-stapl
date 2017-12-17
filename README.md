
# Hermes-Lite 2.0 JAM-STAPL

This is a forked project to support Hermes-Lite 2.0 FPGA programming with a Raspberry Pi. See the [origial github](https://github.com/margro/jam-stapl) and [Altera documentation](https://www.altera.com/content/dam/altera-www/global/en_US/pdfs/literature/an/an425.pdf) for background information.

## Compilation

You can compile this on your Raspberry Pi. You must have build-essentials installed, `sudo apt-get install build-essentials`. To compile, `cd jam-stapl/source` then `make -f Makefile.RPi`. This will build the exectuable `jam` in that subdirectory. 

## Connection

You must connect pins from the Raspberry Pi GPIO header to programming header CN1 on the Hermes-Lite 2.0. 

| Signal        | Raspberry Pi Header Pin | Hermes-Lite 2.0 CN1 Pin | 
| ------------- | -------------:| -----:|
| TCK     | 40 | 1 |
| TDO     | 38 | 3 |
| TMS     | 36 | 5 |
| TDI     | 32 | 9 |
| GND     | 30,34,39 | 2,10 |

The pins are aligned such that two [6x1 short cables](https://www.elecrow.com/6pin-dualfemale-jumper-wire-200mm-p-463.html) can be used to make the required connection. In this case, cable 1 makes the connections shown below.

| Raspberry Pi Header Pin | Hermes-Lite 2.0 CN1 Pin | 
| -------------:| -----:|
| 40 | 1 |
| 38 | 3 |
| 36 | 5 |
| 34 | 7 |
| 32 | 9 |
| 30 | NC (hangs over) |

Cable 2 makes the connections shown below.

| Raspberry Pi Header Pin | Hermes-Lite 2.0 CN1 Pin | 
| -------------:| -----:|
| 39 | 2 |
| 37 | 4 |
| 35 | 6 |
| 33 | 8 |
| 31 | 10 |
| 29 | NC (hangs over) |


## Usage

Both the Raspberry Pi and Hermes-Lite 2.0 must be powered on and connected to program. You must also download to the Raspberry Pi the desired [.jam firmware file](https://github.com/softerhardware/Hermes-Lite2/tree/master/firmware/bitfiles). Look in the [release subdirectory](https://github.com/softerhardware/Hermes-Lite2/tree/master/firmware/bitfiles) and then the Hermes-Lite 2.0 version subirectory for the .jam file. 

Programming is done in two steps. First the FPGA is configured to talk to the serial EEPROM. Next the EEPROM is programmed through the FPGA.

* Configure

Execute on the Raspberry Pi:
```
sudo ./jam -aconfigure <yourjamfile.jam>
```

* Program

Execute on the Raspberry Pi:
```
sudo ./jam -aprogram <yourjamfile.jam>
```

If there are any errors, check your connection between the Raspberry Pi and Hermes-Lite 2.0. This connection must be solid for the programming to be successful. Also, try again as subsequent attempts may not experience errors.

## Modification

This code has been tested on a [Raspberry Pi 3 Model B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/). For older Raspberry Pi models, you may need to change the BCM2708_PERI_BASE back to 0x20000000 as in the original code. See jamgpio.c:
```c
#define BCM2708_PERI_BASE        0x20000000
```

You can change the pins used for JTAG communication in jamgpio.c. See the defines starting at `#define TCK`. Note that Broadcom SOC GPIO numbering is different from the 40-pin header numbering. See the [Raspberry Pi documentation](https://www.raspberrypi.org/documentation/usage/gpio-plus-and-raspi2/) for more details.

There is a hardcoded delay for the JTAG clock TCK in jamstub.c:
```c
// Pulse TCK
delay_loop(10);
gpio_set_tck();
delay_loop(10);
gpio_clear_tck();
```

This delay may be increased if you experience programming failures.

