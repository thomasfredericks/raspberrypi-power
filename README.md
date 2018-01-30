# raspberrypi-power
Scripts and circuits to manage power to the Raspberry Pi 3

## Turn on the Raspberry PI after halting it

Short pin 5  (GPIO3) to pin 6 (GND) together will wake the Pi up from a halt state.

An easy way to test this is to shutdown the Pi with sudo shutdown -h now, and connect pins 5 and 6 with a female to female cable. You only need to short them momentarily. Then you should find that the Pi will "awake".
