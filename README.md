![OpenWeatherStation (OWS)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/OpenWeatherStation.png)

**The Open Weather Station (OWS) is an accessible do-it-yourself weather station solution that aims to be affordable, stable, easy to build and tested in the wild. It evolved from other approaches I have been testing and using in the field since late 2012 to this day.**

**Cheers!** 

  ___Eng. Francisco Clariá___

![OpenWeatherStation Presentation (OWS)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/openweatherstation_presentation.jpg)

# Table of contents

* [Weather data](#weather-data) 
* [Concept](#concept) 
  * [Motivation and goals](#motivation-and-goals) 
  * [Affordable](#affordable) 
  * [Compact and unattractive to burglary](#compact-and-unattractive-to-burglary) 
  * [Simple and repeatable to build](#simple-and-repeatable-to-build) 
  * [Wifi and cell phone network](#wifi-and-cell-phone-network) 
  * [Power loss](#power-loss) 
  * [Software stability](#software-stability) 
  * [Data visualization and diagnosis](#data-visualization-and-diagnosis) 
  * [Data log and storage](#data-log-and-storage) 
  * [Reduced maintenance](#reduced-maintenance) 
* [Why arduino?](#why-arduino) 
* [Android app](#android-app) 
* [Connecting other devices](#connecting-other-devices) 
* [Arduino module assembly](#arduino-module-assembly) 
  * [Schematics diagram](#schematics-diagram) 
  * [List of materials](#list-of-materials) 
  * [Soldering & wiring](#soldering-wiring) 
  * [PCB etching](#pcb-etching) 
  * [Drill PCB board](#drill-pcb-board) 
  * [Solder surface components](#solder-surface-components) 
  * [Wire and connect sensors](#wire-and-connect-sensors) 
  * [Powering arduino module](#powering-arduino-module) 
  * [Housing recommendations](#housing-recommendations) 
  * [Load the code and run](#load-the-code-and-run) 
  * [Connect to the OWS module via bluetooth](#connect-to-the-OWS-module-via-bluetooth) 
  * [Sending commands to the OWS module](#sending-commands-to-the-OWS-module) 
* [Coming next, the App](#coming-next-the-app) 


# Weather data
**The information generated by the OWS every minute is the following:**
* wind speed (m/s)
* wind direction (angle)
* wind gust (m/s)
* wind gust direction (angle)
* rain (mm)
* temperature (Cº)
* atmospheric/barometric pressure (Pascal)
* relative humidity (%)
* ambient light (lux)


# Concept
The current implementation is my personal approach to the challenges I have faced during several years of dealing with a lot of unexpected scenarios in the field. Having said that lets understand how this works.

![OWS Concept](https://github.com/panchazo/open-weather-station/blob/master/docs/img/concept.jpg)

An Arduino Uno module reads data from sensors and every minute sends a payload of data via bluetooth to a connected device. The “connected device” is an android smartphone running an app (the app will be ready soon) that receives this information, stores it in memory, displays the data on the screen and if needed it can push the measures to an external service such as wunderground or another custom cloud service either via wifi and/or mobile network - this is one of the primary advantages of using the smartphone. You could also write a Raspberry Pi program to connect to the Arduino via bluetooth to receive the data if the Android app is not suitable for you (at this point the Pi implementation is not in the scope of the project).

Next you can read more about the concepts behind this implementation. Otherwise click here to [jump to the assembly instructions](#arduino-module-assembly).

# Motivation and goals

Before the current approach I have programed and tested other alternatives like: raspberry, pic, arduino + wifi shield, arduino + ethernet shield, arduino + GPRs shield, arduino uno + arduino mega, wifi boards, routers, integrated arduino with sd/microsd modules, etc etc etc and finally the current approach turned out to be the most effective for my goals:

### Affordable 
I tried to make the price as low as possible so it is not a barrier especially for those in countries where electronics are expensive, currently the total cost is around 300USD to 500USD  including sensors, smartphone, housing, etc depending on your location and mostly the smartphone you get

### Compact and unattractive to burglary
Usually the station would end up in a rooftop, tower, etc from someone who allowed me to install the device, being it very compact makes it less attractive to thieves, easier to install, looks better and the elements such as wind, rain, hailstones will make less damage. Also as the arduino module connects via bluetooth the smartphone it is connected to can be placed in a safer place like inside the house to prevent theft.

### Simple and repeatable to build
Some solutions I evaluated before involved a lot of testing, pre configuration or calibration, complex assembly or even difficulty to get the parts for EVERY station i had to build, current OWS approach reduces that headache a lot

### Wifi and cell phone network 
By using an android smartphone I have the possibility to connect via wifi and cell phone network as needed to keep updating external services in the cloud. And even more, if the wifi connection is lost it will automatically switch to the cell network without any hassle, no AT commands and weird handshakes to achieve a simple HTTP connection…

### Power loss 
Electricity goes away many times, specially during storms: the Arduino module can stay on for around a day with a 5000mAh power bank and the cellphone has already batteries on it, so, depending on the model it will work probably for a day or so too, while still being able to send data if the cell network is working (wifi will probably will die in this scenario)


### Software stability
Arduino module software is really robust, if it reboots it will restart from scratch without any stuff hanging around, I even added a watchdog to make it reboot if for some reason some process takes more than 4 seconds. In this matter I have tested over long periods of time the sd shields, ethernet, wifi and GPRs shields and ALL of them hang after some weeks or even less, requiring a manual reset and in the case of the SD/memory I even needed to extract the card and insert it back… In the other hand I found after long periods of testing in the wild that Android is rock solid and smartphones (like a moto E/moto G) endurance is amazing, coping with temperatures below 0Cº and over 50Cº, experiencing repeated power loss (until battery drained completely) as well as connectivity loss without hanging at all.

### Data visualization and diagnosis
The app allows to both serve as an offline visualization screen or when performing a new installation or maintenance in the field to see what is going on. Sometimes I need to make sure the OWS is working properly especially during a new installation or maintenance, and most of the time I perform this task alone, in a tower or rooftop where its not suitable to take a laptop with me, therefore having the Arduino module connected via bluetooth allows easier diagnosis and with the app running on the smartphone visualization is very easy, comfortable and I don’t need to get the data to the external service to diagnose if its working… its all in the screen

### Data log and storage 
As mentioned before adding memory to the arduino module was one of the things that increased maintenance requirements for the solution as SD hangs and EEPROM wears out, not to mention that the storage and retrieval of large amounts of data (many days of 1 sample per minute) is a slow and complex process for arduino to properly handle. For this reason the arduino module stores samples for about 15 minutes (configurable) which is the time that eventually android may take to restart if it was off for some reason, and then the android app handles data logging, visualization and upload in a more efficient and stable way while at the same time allowing to store larger periods of time.

### Reduced maintenance 
It all comes down to reducing implementation and maintenance as much as I could since most stations I installed are located in remote places, where I need to request access to the owner and everything has to be done as fast as possible

## Why arduino?
The core reasons I decided to use Arduino are:
* widespread adoption
* huge community 
* documentation
* examples everywhere
* available libraries to achieve common tasks
* low price 
* available in many countries
* powering the arduino is easy and convenient (e.g. directly from USB power bank)
* feeding sensors is very easy as it has available 3.3v and 5v regulated outputs that can feed up to 150mA (from 3.3v) or 400mA (from 5v) when connected via USB
* power drain per pin allows 20mA with a 40mA maximum and 200mA in total 
*Bottom line, I think that it make it easier if you want to change, extend or improve the current arduino module solution*

# Android App
Arduino module connects to an android app via bluetooth, this app will be released in the upcoming months and will be open source too. Please take a look back around Febrary/March 2018.

# Connecting other devices
Since the sensing Arduino module transmits the data every minute you could build your own solution that connects to it via bluetooth and process the data as you wish instead of using the proposed android app. For instance a Raspberry implementation could be a great alternative to achieve this, or you could write a Windows 10 application and connect to the module with your computer, just to mention couple examples. This alternatives however are outside the scope of the current project for the time being.

# Arduino module assembly
Next I depict some guidelines so you can build your own module step by step.

![OWS assembly](https://github.com/panchazo/open-weather-station/blob/master/docs/img/assembly_teaser.jpg)

## Schematics diagram

![OWS schematics](https://github.com/panchazo/open-weather-station/blob/master/docs/img/circuit_diagram.png)

## List of materials

The following is the full list of materials needed to implement the station with all its features. In the next sections I will instruct how to assembly the OWS arduino module considering the ideal scenario with all the components, however the module will still work if you don’t connect all sensors, so, if you only want to measure wind speed for instance you can simply ignore the rain, light or pressure sensors.

| Item                                                                                                                                | Quantity | Use                                   |
|-------------------------------------------------------------------------------------------------------------------------------------|----------|---------------------------------------|
| Red or Green led                                                                                                                    | 1        | status light                          |
| 18 kOhm resistor                                                                                                                    | 2        | anemometer and rain debounce circuit  |
| 12 kOhm resistor                                                                                                                    | 2        | anemometer and rain debounce circuit  |
| 10 kOhm resistor                                                                                                                    | 1        | windvane                              |
| 220 Ohm resistor                                                                                                                    | 1        | status led                            |
| 2.2 Kohm resistor                                                                                                                   | 1        | bluetooth                             |
| 1 kOhm resistor                                                                                                                     | 1        | bluetooth                             |
| dual rj11 jack [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/rj11_socket.jpg)                                                                                                                     | 1        | anemometer, wind vane and rain cables |
| diode                                                                                                                               | 2        | anemometer and rain debounce circuit  |
| 1μF capacitor                                                                                                                        | 2        | anemometer and rain debounce circuit  |
| NPN transistor                                                                                                                      | 1        | bluetooth                             |
| Single side copper PCB 5cm x 7cm or bigger [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/pcb_single_side_copper.jpg)                                                                                          | 1        | pcb shield to connect components      |
| Arduino Uno R3                                                                                                                     | 1        | procesing unit                        |
| BH1750 Light sensor                                                                                                                | 1        | llight                                |
| BME280 pressure, temperature, humidity sensor                                                                                       | 1        | pressure, temperature, humidity       |
| HC05 Bluetooth module                                                                                                               | 1        | bluetooth communications              |
| WS 1080 replacement anemometer sensor  [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/ws1080_anemometer.jpg)                                                                                              | 1        | wind speed                            |
| WS 1080 replacement wind vane sensor [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/ws1080_windvane.jpg)                                                                                               | 1        | wind direction angle                  |
| WS 1080 replacement rain gauge sensor [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/ws1080_rain_gauge.jpg)                                                                                               | 1        | rain                                  |
| standard male header pins pack [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/header_pins.jpg)                                                                                                           | 1        | connecting sensor wires                  |
| standard female header pins pack [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/female_header_pins.jpg)                                                                                                           | 1        | connecting bluetooth module                  |
| female to female jumper wires pack [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/female_female_jumper_wires.jpg)                                                                                                 | 1        | wiring sensors                        |
| portable power bank charger 5000mAh or similar that operates without user intervention - must NOT need a button push for it to work | 1        | OWS power backup for Arduino          |
| 15x10x10cm or similar, outdoor plastic housing                                                                                      | 1        | protect the OWS module from elements  |
| 110/220v dual usb wall socket charger module [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/dual_usb_wall_socket_charger_module.jpg)                                                                                       | 1        | OWS usb power source                  |
| 110v/220v power cable [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/110-220_power_cable.jpg)                                                                                                              | 1        | power                                 |
| WS 1080 anemometer and wind vane plastic support [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/ws1080_anemometer_windvane_support.jpg)                                                                                                                    | 1        | support for wind sensors                          |
| WS 1080 rain gauge plastic  support [(example)](https://github.com/panchazo/open-weather-station/blob/master/docs/img/ws1080_rain_gauge_support.jpg)                                                                                                                   | 1        | support for rain sensors                          |
***
**Please recall that I have included all components needed for a full OWS Arduino module assembly, you can purcharse only those related to the sensors you want to implement or even build your own if you want to reduce some costs (like the wind and rain plastic supports). The Arduino code implementation will still work if only some sensors are connected.**
***

## Soldering & wiring
I have a detailed step by step image galleries that you can use a reference to produce the OWS from zero to 100%: 

* [Step by step galleries](https://github.com/panchazo/open-weather-station/blob/master/docs/img/assembly-step-by-step/)

I will explain aswell how to assembly and arrange the components as a reference for you to start building your own station in the following sections.

### PCB Etching

There are several techniques to produce the board circuit, from manually drawing the circuit with a permanent marker to a tone transfer method (https://www.youtube.com/watch?v=QQupRXEqOz4). Since there are plenty of videos and tutorials on how to do it I will let you choose the method of your preference. In case you go for the tone transfer method, recall it’s very important to use a photo quality glossy paper.

Any case you can use [this PDF sheet](https://github.com/panchazo/open-weather-station/blob/master/docs/pcb%20printing%20sheet.pdf) or use the next copper circuit image to produce the pcb. Both have a size reference in the left side (50mm) you can use to make sure the printer did not alter its original dimensions and recall also that the circuit has already been mirrored for your convenience. 

![OWS pcb](https://github.com/panchazo/open-weather-station/blob/master/docs/img/pcb_copper_mirror.png)



### PCB Drilling

Once you have the board ready drill the holes (I use a 1mm drill). From the diagram and circuit layout the required holes are pretty much self explanatory.

![OWS pcb drilling](https://github.com/panchazo/open-weather-station/blob/master/docs/img/pcb_drilling.jpg)


### Solder surface components

Use the following diagram and the list of components to solder each element to the PCB. There are couple things to keep in mind:
 * The “squares” on each side of the board diagram with the codes __D2, D3, D9, D10, D12, D13, A5, A4, A2, GND and VCC__ must connect to the matching Arduino Uno pins, therefore pay close attention when soldering the male header pins facing down and attaching the board to the Arduino.

* The __bht, bmp, stat, vane, anem and pluv__ are male header pins supposed to face up (the opposite side you connect to arduino) in order to later on wire the sensors. Of course you could solder the sensors directly to the board but in this way you can replace any element in a snap and arrange the components in the housing much easier.

 * the __blue__ is a female header pin to later on plug the HC05, the red circles denote that no connection is needed on those pins so soldering is not actually required

* pay close attention to the capacitors polarity and diodes direction as instructed in the diagram

![OWS pcb](https://github.com/panchazo/open-weather-station/blob/master/docs/img/pcb-components.png)

| Item              | assembly code |
|-------------------|---------------|
| 18 kOhm resistor  | R6, R2        |
| 12 kOhm resistor  | R8, R4        |
| 10 kOhm resistor  | R9            |
| 220 Ohm resistor  | R10           |
| 2.2 Kohm resistor | R11           |
| 1 kOhm resistor   | R12           |
| 1μF electrolytic capacitor   | C1, C2 |
| diode             | D1, D2        |
| NPN Transistor    | NPN           |
| male header pins to connect to arduino (solder facing down, to the arduino uno)       | D2,D3,D9,D10,D12,D13,A2,A4,A5,GND,VCC        |
| male header pins to connect to sensors (solder facing up)       | anem, pluv, stat, bmp, bht, vane |
| female header pins to connect to bluetooth (solder facing up)       | blue |


# Wire and connect sensors
Once you have soldered all the elements it is time to connect to the sensors. 

## Wind speed, wind direction and rain
These sensors, which are replacement parts for the WS1080 weather station, use a telephone line (RJ11 plug) that we will interface to the Arduino OWS module using a dual RJ11 jack.

The anemometer and wind vane use one RJ11 connector where the 2 central pins (red and green cables) connect the anemometer and the 2 outer ones (black and yellow) belong to the wind vane. The rain gauge uses its RJ11 central pins (red and green) to connect to the sensor.

In order to connect the RJ11 Jack to the arduino module use the female jumper wires (cut one of the tips to leave the cable exposed) as illustrated in the step by step gallery images.

## Pressure, temperature & humidity
These three parameters are obtained using the BME280, beware that there are many places selling the BMP280 as if it was the BME, but the BMP will NOT measure humidity so pay close attention to the chip specs before buying. 

Connect the BME chip using the female to female jumper wires to the “bme” male header pin on the module board.

## Light
Light amount measured in lux is obtained using the BH1750 sensor. Connect the chip using the female to female jumper wires to the “bht” male header pin on the module board.

## Bluetooth
The arduino module, as explained before, sends the data using the HC05 Bluetooth module. To connect it to the board simply plug the HC05 to the “blue” female header pins as shown in the following image:

## Status Led
The status led is just a red or green led that blinks to indicate that the station is working. Therefore I recommend placing the led where it is visible. Wire the led with a female jumper to the “stat” male header pin on the module board (recall to connect the shortest leg of the led to the board pin that is connected to ground). 

# Powering arduino module
To power the module connect the Arduino Uno USB cable to the portable power bank, and the power bank to one of the 2 available USB outputs on the wall socket charger module, the remaining output can be used later on to connect the Android usb cable to power the smartphone. If you dont want to use a power bank to keep the module on in the event of power loss then you can simply plug the arduino usb cable directly to the wall socket module.

The wall socket charger module connects to 110/220v to feed power to the module. I recommend wiring it with the 110/220v power cable so it is easier later on to connect it to any power outlet. You could also accomplish the same using a regular usb wall charger, but please be sure to protect it inside the housing as usually chargers will not work well exposed outdoors for a long period of time.

# Housing recommendations
Step by step gallery images illustrate how I arrange the module and all the elements inside the outdoor plastic housing, although you may try your own approach. Just keep in mind that you need to protect all the electronics from the elements (such as strong winds, hailstorms, rain, etc.). Sunlight is very harmful for plastics that are not UV protected so try to use materials that are prepared to be outdoor. 

Some prefer to use the Stevenson screen for this purpose (https://en.wikipedia.org/wiki/Stevenson_screen). 

The BME280 have to be exposed (but protected) to properly measure temperature, pressure and humidity, so it is not advisable to enclose it in a sealed box (likewise the BH1750 to measure light). Also keep in mind the heat that direct sun will produce on the housing and on the sensors as it can raise electronics temperatures a lot. I have tested the OWS exposed to direct sun for several days with an internal housing temperature rounding 60ºC without any issues.

# Load the code and run
First off you need to load the arduino program. Inside the “arduino” project folder you will find another folder called “open-weather-station” where the .ino program file is located. Also, inside the “arduino” folder I have placed the libraries that you will need for the specific sensors, include those libraries in your arduino project (https://www.arduino.cc/en/Guide/Libraries) and it should be ready to compile. 

I did my best to organize, add comments the code and keep the code simple to make it understandable so please read through the code to learn more about how it works. Nevertheless let me provide you some basic hints. 

## About arduino program constants

* __ENABLE_DEBUG_SERIAL_OUTPUT__: if true it will output data to the serial monitor. I recommend using it at first to know if it is working properly and sensors are acquiring data. You can turn it off for production.

* __ARDUINO_AUTOREBOOT_MINUTES__: once the amount of minutes defined in this constant elapses the arduino will auto reboot and reset the bluetooth chip (turn off and on again). This timer can be restarted by sending a command to the module. 

* __SEND_CALCULATED_WIND_SPEED_MS__ and __SEND_CALCULATED_RAIN_MM__: the module will send the cycles per second it counts for anemometer and rain gauge so you can convert this to an actual wind speed or millimeters of rain in the other end and apply your own calibration. If these constants are set to true, the station will perform this calculation internally and send also the calculated windspeed and rain based on default calibrations parameters you can manipulate by changing the following constants:

  * __ANEMOMETER_SPEED_FACTOR__, cup anemometer factor, if you don’t know this value leave it as is
  * __ANEMOMETER_CIRCUMFERENCE_MTS__, the circumference of a full cycle calculated from the cup centerpoint
  * __ANEMOMETER_CYCLES_PER_LOOP__, how many “counts”  generates the anemometer in a full loop, normally it is 2, but could be 1 depending on your sensor
  * __RAIN_BUCKET_MM_PER_CYCLE__, how many mm of rain is equivalent for each count of the sensor
  * __VANE_AD_...__, the wind vane has a set of resistors that vary depending on the wind direction, arduino sends 5v through the sensor and will make an analog to digital (A/D) conversion of the value that ranges from 0 to 1023, depending on the vane orientation the A/D value will change. So the VANE_AD_… values are the matching number for each of the directions and are calibrated for the ws1080 wind vane. When the A/D value is acquired the closest matching VANE_AD… value is used to assign the wind direction.

The rest of the constants are pretty much self explanatory.

## Partial and full data samples
The module will send by default every 5 seconds the partial wind samples, useful for instance to show more “real time” wind data, and then, every minute the module sends all the data with the averaged wind samples, gust, temperature, etc. The partial samples are sent from the “sendWindPartialSample” function and the full samples are sent from the “sendFullSamples” function. Each sensor data is separated by a separator (configured in the constants) and each transmission is terminated with a new line. Take a look at those functions in the arduino code to understand how the samples are labeled and sent. I strongly recommend to enable the debug output constant so you can see the data in the arduino serial output monitor otherwise you will need to connect via bluetooth to see any data.

## Connect to the OWS module via bluetooth
To connect to the module there are several alternatives. I will not enter into details on how to pair and connect to a bluetooth device from your PC, Mac, laptop, Iphone or Android device since there many tutorials that explain that with greater detail (nevertheless in the step-by-step gallery you will find some images where I connect using my PC and Android phone). 

Just keep in mind that the bluetooth device you are using for the Arduino OWS module will be listed as HC05 when being discovered and if a code is requested to pair to the device the HC05 usually uses 1234 or 0000. If you are connected via an Android bluetooth app (e.g. https://play.google.com/store/apps/details?id=project.bluetoothterminal) for instance or even via Putty to your laptop using a bluetooth COM port (http://www.instructables.com/id/Remote-Control-Bluetooth-Arduino-PuTTY/), you will see in your screen the same payload of data you see in the Arduino IDE serial monitor (providing the debug output flag is set to true).

### Sending commands to the OWS module
You can send commands (single uppercase ascii character) to make the module do some things. The arduino function called “readCmdFromBluetooth” implements this feature. For instance it allows to:
* character __R__: restarts the arduino
* character __T__: resets the timer that will make the arduino autoreboot every ARDUINO_AUTOREBOOT_MINUTES
* character __S__: enables the module to send the partial samples
* character __Q__: disables the module to send the partial samples and will only send the full samples every minute
* character __L__: send all the measures stored in modules volatile memory for the past WIND_AVG_MINUTE_LOG_SIZE minutes (recall this log is erased after a module reboot and may have been initialized to zero)

# Coming next, the app
__In the upcoming months I will produce an Android app so you can connect to the stations, monitor the parameters in real time, store the samples for long periods of time and see graphics with its evolution and send the information in real time to cloud services. The app will be released as open source too and you will be able to use it as is (apk) or download the code and make your custom flavor of it. Stay tunned!__
