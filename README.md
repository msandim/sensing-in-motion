Sensing In Motion
===============

Environmental awareness is a big topic these days and more and more DIY projects involving environmental sensing are appearing.

"Sensing In Motion" is a project with the goal of creating an environmental sensing structure/station that reads several parameters, as well as its GPS location, and sends them to a remote server.
This station can also be plugged on any vehicle (during the course of this project we chose a quadcopter).

## Hardware involved
### Sensing module
* **Processing unit:** Arduino Uno R3
* **Communication module:** Arduino GSM/GPRS Shield

##### Sensors used
* **Humidity/Temperature sensor:** [DHT11] (http://www.micro4you.com/files/sensor/DHT11.pdf)
* **Particulate/Dust sensor:** [Shinyei PPD42NS] (http://www.sca-shinyei.com/pdf/PPD42NS.pdf)

### UV module
- Parrot's AR Drone 2.0

##### GPS device
- Parrot's Flight Recorder

## How does it work?
The sensors we're using are connected to Arduino and samples are taken from time to time. Those samples are sent to a remote server (Thingspeak was the chosen one) with HTTP Post using a GPRS communication.

A serial communication is estabilished between the Arduino and the AR Drone 2.0 in order to receive GPS position information to tag each sample taken. The developed protocol is illustrated [here](https://raw.githubusercontent.com/MigueelS/sensinginmotion/master/images/gps%20protocol.png).

## Instalation

#### Wiring
Make sure you follow [this tutorial](https://gist.github.com/maxogden/4152815) to enable the serial communication between the Arduino and the Drone, as well as installing Node.js on the Drone. A most recent version of the AR Drone 2.0 does not have the 10 pin set described in the tutorial, so check the following [schematic](https://raw.githubusercontent.com/MigueelS/sensinginmotion/master/images/6%20pin%20set.png) (check [this](http://forum.parrot.com/ardrone/en/viewtopic.php?id=8148) for more information).

A Logic Level Shifter will be needed to avoid ruining the Drone's serial pins. We used [this one](https://www.sparkfun.com/products/12009). Check [mirumod's schematic](http://mirumod.tk/hw/arduino_nano/MIRUMODNANO019GPSG_new.jpg) to know how to do the wiring.

A complete wiring diagram of our system (including the sensors) is available [here](https://raw.githubusercontent.com/MigueelS/sensinginmotion/master/images/System%20schematic.png). Since there was a need for multiple GND and 5V pins, we used a PCB and soldered 2 pin sets which are connected to Arduino.

#### Arduino Configuration
Note: To avoid conflicts between the Arduino GSM Library and Software Serial, you should comment several code lines of both libraries ([check this](http://purposefulscience.blogspot.pt/2013/06/arduino-gsm-shield-tips.html)).
Make sure you comment the following lines in SoftwareSerial.cpp:

```c
/*
#if defined(PCINT2_vect)
ISR(PCINT2_vect)
{
  SoftwareSerial::handle_interrupt();
}
#endif
*/
```

and the following in GSM3SoftSerial.cpp:

```cpp
/*
#if defined(PCINT0_vect)
ISR(PCINT0_vect)
{
  GSM3SoftSerial::handle_interrupt();
}
#endif

#if defined(PCINT1_vect)
ISR(PCINT1_vect)
{
  GSM3SoftSerial::handle_interrupt();
}
#endif
*/
```

##### Main configuration
If you want to change the pins used, there are several ```#define``` directives you can change/comment on project.ino:

```cpp
/* Temperature and humidity sensor pin configuration */
#define DHT11PIN A1

SoftwareSerial droneSerial(8, 9); // RX pin, TX pin

/* Dust sensor configuration */
#define DUSTPIN1 11 // Pin connected to the sensor's P1
#define DUSTPIN2 10 // Pin connected to the sensor's P2
#define DUSTSAMPLETIME 15000 // Total sample time (in ms)

#define DRONE // Uncomment to send data to the drone's serial port
#define SEND_SERVER // Uncomment to send data to the thingspeak server
```

There's also the possibility of choosing the enabled sensors, by commenting the desired ```#define``` directive:
```cpp
#define TH_SENSOR_ON // Uncomment to activate Temp/Hum sensor
#define DUST_SENSOR_ON // Uncomment to activate Dust sensor
```

Information regarding your SIM card's mobile carrier should be included in ServerConnection.h:
```cpp
#define PINNUMBER "" // Pin number of your SIM Card

// The following directives are related to the GPRS connection
// Please fill with your mobile carrier's correct data
#define GPRS_APN "umts"
#define GPRS_LOGIN ""
#define GPRS_PASSWORD ""
```

#### AR Drone Configuration
First of all, we advise you to disable the Drone's serial port console communication, by changing the file "init.sh" (TODO explanation)

(TODO)
