# raspberrypi-power
Scripts and circuits to manage power to the Raspberry Pi 3

## Halting the Raspberry PI 3

Install "gpio-zero" for "Python 2":
```
sudo apt update
sudo apt install python-gpiozero
```

Create a file named "shutdown-with-hold.py" in "/home/pi" and paste this inside:
```python
from gpiozero import Button
from signal import pause
import os, sys

offGPIO = int(sys.argv[1]) if len(sys.argv) >= 2 else 21
holdTime = int(sys.argv[2]) if len(sys.argv) >= 3 else 6

# the function called to shut down the RPI
def shutdown():
    os.system("sudo poweroff")

btn = Button(offGPIO, hold_time=holdTime)
btn.when_held = shutdown
pause()    # handle the button presses in the background
```
This code creates a button on GPIO 21, waits for it to be pressed, and then executes the system command to power down the Raspberry Pi. GPIO 21 is nice because it's on pin 40 of the 40-pin header and sits right next to a ground connection on pin 39. This combination makes it difficult for an off switch to get plugged in incorrectly. On a 26-pin header, GPIO 7 is similarly situated at the bottom there on pin 26, next to a ground connection on pin 25.

Next, we want the code to run at boot. Edit "/etc/rc.local" and add the following line just **before** "exit 0":
```
python /home/pi/shutdown-with-hold.py
```

Reboot the Raspberry PI.


Source: https://github.com/TonyLHansen/raspberry-pi-safe-off-switch/ by https://github.com/TonyLHansen

## Turn on the Raspberry PI 3 after halting it

Short pin 5  (GPIO3) to pin 6 (GND) together will wake the Pi up from a halt state.

An easy way to test this is to shutdown the Pi with sudo shutdown -h now, and connect pins 5 and 6 with a female to female cable. You only need to short them momentarily. Then you should find that the Pi will "awake".

Source: https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi by https://howchoo.com/u/tyler
