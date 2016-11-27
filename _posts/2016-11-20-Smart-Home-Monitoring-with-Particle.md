---
layout: post
title: Smart Home Monitoring with Particle Spark Core
---

In this article we are going to use Particle Core (ex-Spark) board to build a simple IoT project. We will use a humidity and temperature sensor (DHT22), sent the data to the Particle cloud and then visualise the measurements in real time.

Particle core is a little board build around a Cortex M3 MCU and the WiFi CC33000 SoC from Texas Instruments. It connects automatically to the Particle cloud platform and it is instantly accesible from the web.

![Spark Core]({{site.url}}/assets/images/2016-11-20-Smart-Home-Monitoring-with-Particle/spark-core.jpg){:class="img-responsive"}

**Hardware Configuration**

For this project we are going to use the following components:
 - Particle Spark Core Board [(link)](https://store.particle.io/)
 - DHT22 temperature/humidity sensor [(link)](https://www.adafruit.com/product/385)
 - 10K resistor
 - Breadboard
 - Jumper Wires

Below are a schematic with all the different components of this project:

![Breadboard View]({{site.url}}/assets/images/2016-11-20-Smart-Home-Monitoring-with-Particle/humidity_breadboard.png){:class="img-responsive"}

The DHT22 sensor has 4 pins. The left pin is the VCC and the right one the GND. Pin2 is the DataOut and Pin3 is not used. We connect Pin2 to the D3 pin of the Particle Core and  we also place the 10K resistor between Pin1 and Pin2 of the sensor.


![Complete Circuit]({{site.url}}/assets/images/2016-11-20-Smart-Home-Monitoring-with-Particle/humidity-circuit2.jpg){:class="img-responsive"}


**The code**

Particle has a web [IDE](https://build.particle.io) that can be used to write and flash code onto particle devices. Additionally particle offers a Command Line Interface (CLI) to interact with devices and the particle cloud from the terminal. Find more information about how to install an use the CLI [here](https://github.com/spark/particle-cli).

We are going to use Adafruit_DHT library for our project. If you are using  the web IDE then you can easily find and add the library from "libraries" menu. If you are using the CLI instead then download "Adafruit_DHT.cpp" and "Adafruit_DHT.h" from [this repository](https://github.com/russgrue/Adafruit_DHT_Library/tree/master/firmware)  and save this files inside the same directory with the code below:

```c
#include "Adafruit_DHT.h"

// DHT parameters
#define DHTPIN 3
#define DHTTYPE DHT22

// Variables
int temperature;
int humidity;

// Create an instance of DHT sensor
DHT dht(DHTPIN, DHTTYPE);

void setup() {

    // Initialize DHT sensor
    dht.begin();
}

void loop() {

    // Temperature measurement
    temperature = dht.getTempCelcius();

    // Humidity measurement
    humidity = dht.getHumidity();

    // Use publish() function to send the data every 2 seconds on the Particle cloud platform:
    Spark.publish("temperature", String(temperature) + " °C");
    delay(2000);
    Spark.publish("humidity", String(humidity) + "%");
    delay(2000);

}

```
**Notes**

Before flashing the code we need to know the device id:
```shell
$ particle find
```

and then use the command below to compile and flash the source code:

```bash
$ particle flash 0123456789ABCDEFGHI dht22.ino Adafruit_DHT.cpp Adafruit_DHT.h
```

Change 0123456789ABCDEFGHI with your device id.



**Visualizing the data**

The publish() function we use in our code allow us to sent data directly to the Particle cloud platform. We can see a live visualization of this data by going there:

[https://console.particle.io/logs](https://console.particle.io/logs)


![Console Visualization]({{site.url}}/assets/images/2016-11-20-Smart-Home-Monitoring-with-Particle/console2.png)
