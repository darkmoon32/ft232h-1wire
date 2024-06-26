## 1-Wire bus over GPIO on FT232H

Implementation of 1-wire using the Adafruit FT232H Breakout board.

GPIO seems to function significanlty better on the second MPSSE (ACBUS) on my board.
If you have problems using the D4-D7 pins, then try using C0-C7

See Also: [Adafruit GPIO fork with 1-Wire Support](https://github.com/TuxInvader/Adafruit_Python_GPIO)

Created using hints and examples from Adafruits GPIO library, and Neenars PyDigitemp:

 * [Adafruit GPIO](https://github.com/adafruit/Adafruit_Python_GPIO)
 * [PyDigiTemp](https://github.com/neenar/pydigitemp)

## Specification Docs

### FTDI - MPSSE

 * [MPSSE AN 135](http://www.ftdichip.com/Support/Documents/AppNotes/AN_135_MPSSE_Basics.pdf)
 * [MPSSE AN 108](http://www.ftdichip.com/Support/Documents/AppNotes/AN_108_Command_Processor_for_MPSSE_and_MCU_Host_Bus_Emulation_Modes.pdf)

### 1-Wire

 * [1-Wire in software](https://www.maximintegrated.com/en/app-notes/index.mvp/id/126)
 * [1-Wire Search](https://www.maximintegrated.com/en/app-notes/index.mvp/id/187)
 * [AN4255 (Power for extended features)](http://pdfserv.maximintegrated.com/en/an/AN4255.pdf)

### Thermometers

 * [Maxim DS18B20](http://datasheets.maximintegrated.com/en/ds/DS18B20.pdf)

### iButton

 * [Maxim iButton (DS1977)](https://datasheets.maximintegrated.com/en/ds/DS1977.pdf)

## 1-Wire

1-Wire is managed entirley by the master. All communications begin with a reset signal, followed by a command and a sequence of reads/writes. The master transmits a binary 1 by holding the line low for a brief period, and a binary 0 by holding the line low for a much longer period. A read is elicited by briefly pulling the line low, and then looking for a response. The slave lets the line return high to signal a 1, and pulls it low to signal a 0.

1-Wire includes a search function to locate slave devices and recovery their ROM codes. If there is only one device on the bus then the master may send a skip-rom or read-rom command, otherwise all commands except for skip,read,search need to address a device first. All devices except for the one being addressed will go to sleep until the next reset signal.

1-Wire can run at two speeds, standard mode, and overdrive. Timings are included for running in overdrive, and have been tested with a DS1977 iButton module.


## Wiring (Example using DS18B20)

You will need to provide a pull-up resistor on the GPIO line which you want to use for 1-Wire. This will provide power in parasite mode power mode. I use a 4k7 pull up to the 5v line on the FT232h. See Image below:

![FT232H Wiring](resources/wiring.jpg "FT232H wiring")
![FT232H Diagram](resources/ft232h-1wire.png "FT232H wiring Diagram")

The pin number is mapped to the table 3.10 in [FT232 datasheet](https://ftdichip.com/wp-content/uploads/2020/07/DS_FT232H.pdf). The label on the board is then taken from **FT232H Pin Descriptions** from the same document. For example pin *GPIOL3* has pin number *20* and it's name is *ADBUS7*. From this the *AD7* name is created.

## Requirements

```
sudo apt install python3-ftdi1
pip install ctypes-ftdi1
export PYTHONPATH=$PWD
```

## Examples

See test files in examples folder for usage.

The w1ftdi class contains lots of debugging information, so you can get a full breakdown of the 1-wire and MPSSE commands used. Set the debug to level 5 to get the most verbose output.

 * examples/test1.py
   - Performs a search of the 1-wire bus and reports the devices found.
   - Uses C0 pin to communicate

 * examples/test2.py
   - Performs a search of the 1-wire bus and then reads the temperature from any DS18B20 devices present.
   - Uses C0 pin to communicate

 * examples/fever-checker.py
   - Uses a modified Adafruit_GPIO library to talk to a DS18B20 over 1-wire, and control some LEDs with standard GPIO, and update an I2C Seven Segment display with the temperature reading. See the wiring diagram:

  ![Fever-Check Diagram](resources/fever-check-diagram.png)


