* Blink a LED


Now we are ready to blink a led. The CHIP docs point to a python library that will be very helpful, CHIP-IO. In its github page, the instructions for installation are as follows:

    sudo apt-get update
    sudo apt-get install git build-essential python3-dev python3-pip flex bison chip-dt-overlays -y
    sudo pip3 install CHIP-IO
    
So follow them! 

After doing this, you will be able to program in python all what we need to control the robot arm.

We will also need to locate the GPIO pin. We will use XIO-P0, so get the LED and connect the anode (long part) to the VCC3V3 hole (5th on the 3rd row) and the catode to the XIO-P0 (7th on the same row). Current will flow from anode to catode when the catode is open.

Let's log into python:

    python3
    
We will be received by the prompt. Now we only need to write the following:

    import CHIP_IO.GPIO as GPIO
    from time import sleep
    
    def blink():
        GPIO.setup("XIO-P0", GPIO.OUT)
        GPIO.output("XIO-P0", GPIO.LOW) # Low opens the gate, current flows, and LED shines
        sleep(1)
        GPIO.output("XIO-P0", GPIO.HIGH) # High closes the catode gate again.
        sleep(1)
    
    def tick-tack():
        for x in range (0, 60):
            blink()
            
            
Magic! A blinking led! This lesson is done.

