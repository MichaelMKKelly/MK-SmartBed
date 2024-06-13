# MK-SmartBed

WRITEUP IS A WORK IN PROGRESS

This Project was mostly a learning exercise for myself to learn about creating a custom sensor control using an esp32.

I decided write this up in case this helps or inspires someone else to do the same or a simalar project.

## Project Goal

Use multiple Sensors to detect presence in a bed

Add a tempreture sensor to the bed

Have this report into home Assistant

OR this sensor with a prexisting mmWave Sensor to help ensure accurate room presence detection

## The Plan

get an ESP32 dev board and install with espHome

Create PCB on stripboard to house esp32 and other components

get and attach 4 FSR sensors and a tempreture probe

put PCB into a "box" with an easy way to connect it to the sensors and power

Program the ESP32 to do what I want

## The FSR Sensors

So I decided to go with 4 sensors to ensure accurate sensor detection (this would not be necessary but we will get to that later)

2 at "shoulder level" starting at ethier side of the bed

2 at "butt level" starting at ethier side of the bed


Wiring these around the bed felt like it might become an issue so I opted to use a cat6 network cable to connect the sensors as this gave me 4 pairs of cables nicely bundled together.

Could I have shared the grounds for the sensors on the cat6? almost certainly yes but I figured keeping them seperate gave me the best troubleshooting options if something went wrong.

I investigated and found small rj45 breakout boards with screw terminals which would allow for me to easilly break into the signals along the way. these came in 2 flavours. 1 port for the ends of the chain and 2 connected ports for the middle parts of the chain.


Sensor 1 - 1 Port - breaking out the cat6 orange pair

Sensor 2 - 2 Port - breaking out the cat6 blue pair

Sensor 3 - 2 Port - breaking out the cat6 green pair

Sensor 4 - 2 Port - breaking out the cat6 brown pair

Controller - 1 port - connecting the pairs to the pcb

My plan was to measure the resistance of each sensor with the bed empty and again when "occupied" to calculate the best pull up resistor value to use. however this proved unsucsessful as measuring the resistance over this distance was unreliable with my (admittedly cheap) multimeter. My next plan was to build a quick voltage devider resistance sensor circuit using the esp32 on breadboard but this also proved unreliable. So i opted for trial and error going through my resistor box until I saw it working. after a few attempts I settled on 5.6k which although maybe didnt give me the best range it was definetly accurate enough to detect yes/no in software.


these would then be attached along the slats of the bed with the rj45 ports underneath the slat (I opted to use electical tape for this)

## The Tempreture Probe 

This was just a DS18B20 with a long cable taped in position behind the headboard of the bed

this cable along with the cat6 from the FSR sensors would then be attached to the Controller

## The Controller PCB

on the stripboard I then attached 2 dip headers to put my prepinned esp32 dev board onto and attached a number of short cables to attach to my sensors. along with 5 resistors:

4 x 5.6k resistors as pull up resistors on the FSR sensors
1 x 4.7k resistor as pull up resistor on the tempreture sensor data line

not the most complicated circuit in the word but it was fairly easy for someone who hasnt touched a soldering iron in years

## The controller Box

A cheap project box with some holes drilled for the cables to come out and a short USB-C extention for power was the layout I went with.

one of the RJ45 breakout boards mounted with cable ties through its mounting holes and just some wagos cable tie based down was my basic connection handlers

## The ESPHome Code

I was plesently supprised how easy this was to write and soon had it running as intended and used the following sensors:

Tempreture - using the dallas tempreture sensor component

Bed Trigger Level - adjustable number that would let me move adjust the sensitivity as required

Bed_sensor_1/2/3/4 - For reading the voltage on the pin through the SR sensors

Bed Sensor 1/2/3/4 occupied - binary sensor showing showing if sensor voltage is above/below the trigger level

Top of Bed Occupied - binary sensor that is an OR of sensors 1 and 2 being occupied

Middle of Bed occupied - binary sensor that is an OR of sensors 3 and 4 being occupied

Bed Occupied - Binary sensor that is an OR of all 4 sensors which has a delayed on of 5 seconds and a delayed off of 30 seconds


in normal operation I expect to use only the "Bed Occupied" and the "Tempreture" sensors and there is definetly more efficient ways to write this setup however this was straight forward and left me room for fault finding/ debbugging if/when needed


