* Introduction

** How the project started

It's been some time since I last did anything related with Electronics - actually, last time was in uni, ca. 2006. And I loved it! So the last couple of years I've had this itch that I wanted to scratch, and during boring meetings I would daydream about a possible project to build.

A robot would be great! But each time I saw the price of servos (~30 euros), and multiplied by the number of servos on a robot arm (3 degrees of freedom for shoulder, 1 for the elbow, another 3 for the wrist, and at least 1 for a claw or some other actuator), I thought "This project is way too expensive for something that is not really useful!".

Also servos make a lot of noise, with all that gearing. It would be cool to have something more similar to a real muscle.

Servos are noisy, slow, weak and expensive because they have plastic gearing and controls that I don't need - I want to have a series elastic or direct drive system that emulates a muscle. Theoretically a sufficiently advanced system can simulate the elastic behaviour in the motor... but I think I will experiment with both.

** Reinventing the wheel

I spent some time thinking about how an electronic muscle would be built. How would a linear actuator work, etc.

Direct drive.

** Impulse purchase

Then I found these motors at about an euro per motor:

(image)

(specs)
20 x 15 x 25 mm
3V
18K rev/min max
local current 200mA
stall current 2A
weight 17 gram
axis: 7mm long x 2mm diam

So I bought them and a couple of Chip computers, and my project was started!

** Buying some more parts, a little bit more rationally

Some high-level design
Rest of purchases in mouser, and price.

** The plan

Now I already have about 100 euros in equipment, let's do something!

I have devised the following plan:

 1. Boot the CHIP
 2. Blink a LED! Ok, super basic project, but I need to see how to work with the CHIP.
 3. Read the value of the potentiometer. In here I will interface with the MCP3008 (the ADC), and read a simple resistor.
 4. Move a motor with the CHIP. Nothing too fancy, just vary the current that goes into the motor
 5. Read an amperage. I need to know how the motor is performing, and for this I will use 2 feedbacks: the potentiometer for position, and amperage for "intent".
 6. All together now! Mount a motor with amperage and potentiometer, and see how it performs by itself, under load, etc.
 7. One articulation. Let's call an articulation a joint with 2 degrees of freedom: the target position and the strength that is used to keep it. This step creates one articulation. I will try several configurations: a direct drive one vs. a series elastic, and a 2 motors vs. a 2 motor and a closed wire. It doesn't make sense to have a closed wire and 2 motors, so that makes 3 configurations.
 8. A robot arm!

Let's get to it!
