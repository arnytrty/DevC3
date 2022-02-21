## THIS BOARD HAS AN ERROR WITH WIFI & RESTARTING, I AM WORKING ON THE FIX BUT I HAVENT CREATED IT YET

---

# DevC3
DevC3 is a small, open source board (shaped as a dongle) based on ESP32-C3 with MicroPython support. It is designed in [EasyEDA](https://easyeda.com/editor) and it is fully assemblable in [JLCPCB assembly](https://jlcpcb.com/).
You can find it on [OSHWLab](https://oshwlab.com/trty69230/devc3), or in this repository.

![assembled](images/P_20220211_172651[1].jpg)
![top](images/P_20220211_172717[1].jpg)
![bot](images/P_20220211_172736[1].jpg)
![hand](images/P_20220211_172917[1].jpg)
![leds](images/P_20220211_181910[1].jpg)

# Installing MicroPython
For now, there isn't any direct port of MicroPython, but [GENERIC_C3_USB](https://github.com/micropython/micropython/tree/master/ports/esp32/boards/GENERIC_C3_USB) works perfectly! The installation process basically of these steps:

1. Download the latest firmware binary from [MicroPython website](https://micropython.org/download/esp32c3-usb/)
2. **With Python & Pip installed**, install [esptool.py](https://github.com/espressif/esptool) with `pip install esptool`
3. Plug the board into a computer while holding down the button (device should show as *USB JTAG/serial debug unit*)
4. Install MicroPython with:
    * clear flash: `python -m esptool --chip esp32-c3 --port /dev/ttyUSB0 erase_flash`
    * flash firmware: `python -m esptool --chip esp32c3 --port /dev/ttyUSB0 --baud 460800 write_flash -z 0x0 esp32c3.bin`
5. Reboot the board simply by taking it out and plugging it back

*Replace /dev/ttyUSB0 with your device, on Windows use COM, for example COM5, COM12, ... (can be found in device manager under Ports). This guide can also be found on the [port page](https://micropython.org/download/esp32c3-usb/).*

# Using Peripherals
You can use all libraries available in MicroPython (for example the [Pin](https://docs.micropython.org/en/latest/library/machine.Pin.html) library).
There is only one problem with the [NeoPixels](https://docs.micropython.org/en/latest/esp8266/tutorial/neopixel.html) library.

As stated in [esp32-c3 port used wront rmt channel](https://github.com/micropython/micropython/issues/8109), you need to use `esp32.RMT.bitstream_channel(0)` before using NeoPixel. For Example:
```py
from machine import Pin
from neopixel import NeoPixel
from esp32 import RMT

# correct the channel error
RMT.bitstream_channel(0)

pin = Pin(0, Pin.OUT)
np = NeoPixel(pin, 3)
np[0] = (32, 0, 0)
np[1] = (0, 32, 0)
np[2] = (0, 0, 32)
np.write()
```

All Peripherals:
* WS2812 = **IO0**
* Button = **IO9**
* IO1-IO10 = On the bottom side of the board
* RX-TX = On the bottom side of the board

*GPIO2, GPIO8 and GPIO9 have on board 10KOhm pullups*
