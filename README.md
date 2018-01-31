# Raspberry Pi 3 Power Management

How to turn off and on the Raspberry Pi with buttons and how to use a cheap UPS to protect the Raspberry Pi's memory from power interruptions.

Table of contents:
1. How to halt (i.e. shutdown) the Raspberry Pi.
1. How to turn on the Raspberry Pi after halting it.
1. PowerBoost 1000C UPS: Safely halt Raspberry Pi when power supply is interrupted or disconnected

## Halting the Raspberry Pi 3 Script

![Raspberry Pi 3 Halt Circuit](raspberry_pi_halt.png?raw=true "Raspberry Pi 3 Halt Circuit")

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
This code creates an input (with an internal pullup) on GPIO 21. When it is grounded, it executes the system command to halt the Raspberry Pi. GPIO 21 is nice because it's on pin 40 of the 40-pin header and sits right next to a ground connection on pin 39.

Next, we want the code to run at boot. Edit "/etc/rc.local":
```
sudo nano /etc/rc.local
```

Then add the following line just **before** "exit 0":
```
python /home/pi/shutdown-with-hold.py
```

Save and reboot the Raspberry Pi.

To test, when the Raspberry Pi on fully booted, touch GPIO 21 to ground for 6 secondes and the Raspberry Pi should start halting.


Source: https://github.com/TonyLHansen/raspberry-pi-safe-off-switch/ by https://github.com/TonyLHansen

## Turn on the Raspberry Pi 3 after halting it

![Raspberry Pi 3 Boot Circuit](raspberry_pi_boot.png?raw=true "Raspberry Pi 3 Boot Circuit")

No script in necessary for this behavior. It is built in. Short pin 5  (GPIO3) to pin 6 (GND) together will wake the Pi up from a halt state.

An easy way to test this is to shutdown the Pi with sudo shutdown -h now, and connect pins 5 and 6 with a female to female cable. You only need to short them momentarily. Then you should find that the Pi will "awake".

Source: https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi by https://howchoo.com/u/tyler

## Raspberry Pi 3 safe off/on button

You can use simple a single push buttons to toggle a halt and boot:

![Raspberry Pi 3 safe off/on button circuit](raspberry_pi_button_bb.png?raw=true "Raspberry Pi 3 safe off/on button circuit")

## PowerBoost 1000C UPS: Safely halt Raspberry Pi when power supply is interrupted or disconnected

This solution uses an Adafruit PowerBoost 1000C as a UPS for the Raspberry Pi and the "Halting the Raspberry Pi 3 Script". If the supply power is interrupted, the Raspberry Pi halts automatically with battery power. 

![Raspberry Pi 3 UPS Halt on Power Loss](raspberry_pi_ups_halt_power_loss_bb.png?raw=true "Raspberry Pi 3 UPS Halt on Power Loss")

## PowerBoost 1000C UPS: Safely halt Raspberry Pi when battery is low

This solution uses an Adafruit PowerBoost 1000C as a UPS for the Raspberry Pi and the "Halting the Raspberry Pi 3 Script". If the battery's voltage is low, the Raspberry Pi halts automatically. 

![Raspberry Pi 3 UPS Halt on Low Battery](raspberry_pi_ups_halt_low_battery_bb.png?raw=true "Raspberry Pi 3 UPS Halt on Low Battery")
