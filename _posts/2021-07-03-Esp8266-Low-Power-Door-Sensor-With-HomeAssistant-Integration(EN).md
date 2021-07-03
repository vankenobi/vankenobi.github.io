---
layout: post
title: "Esp8266 Low Power Door Sensor With HomeAssistant Integration (EN)"
excerpt: ""
tags: [Esp8266,Door sensor,HomeAssistant,3D Printer,MQTT,DIY Project,Deep Sleep,Transistor]
categories: [Esp8266-01] 
date: 2021-07-03 21:50:00
comments: true
share: true 
image:  
  feature: 
---

<br>

![photo](https://drive.google.com/uc?export=view&id=12pLLNftPSHT9aJkCZGEXDJivoaW0hkL8)

### About This Project

Hi everyone, this project is door sensor project consuming low power and running long time. <br>
I integrated this sensor to my homeassistant home system. Sensor communication is wifi and using mqtt protocol. Sensor is working when magnet leave  then connecting to mqtt broker  finally it is sending  current battery status and  "Open" text.
Let's talk about requirements for this project and then project making if you want.  

<br>

### What is HomeAssistant ? 

![photo](https://drive.google.com/uc?export=view&id=14BbUkBHtHntvVoomfbj64dP78iPQQ2n_)

Home assistant is Home Automation Hub. Or Home Automation Software to be more precise. It’s free and open source and fully local alternative to things like HomeBridge and SmartThings. It gives you the power of home automation without the cloud so you won’t depend on internet connection or remote servers. This results in faster operation and reliability. You can visit [this](https://en.wikipedia.org/wiki/Home_Assistant){:target="_blank"} website if you think need more information.

<br>

------------------------------------------------------------------------------------

### Starting To Project

First one we should print the 3d parts of this project. You should access from [here](https://www.thingiverse.com/thing:4899894){:target="_blank"}  the 3d parts of this project if you wondered. By the way at website where i put 3d parts is people is sharing own 3d designs. This website called "Thingiverse" is very useful site for who searching different things and different design. You can find below settings of cura slicer program for this 3d parts. 

#### Cura And Print Settings 

* 0,3mm layer thickness (resolution)
* No Rafts
* No support
* %25 infill
* Filament Material (PLA)
* My 3D printer is Ender 3 Pro but you can use any 3d printer brand 
 
<br>

#### Requirements Of This Project

![photo](https://drive.google.com/uc?export=view&id=13Q2l1tuU9ixBerHXvIdYtLBbRp1cX0dx)

* 1 pcs Esp8266-01
* 1 pcs Reed Switch (Normally Open)
* 2 pcs 2 Pin 2.50mm DIP Pcb Type Header Straight (one female one male)
* 3 pcs M3 6mm screw
* 8 pcs female jumper header (for esp8266 assembly)
* 2 pcs 1 Kohm resistors
* 1 pcs 220 ohm resistor 
* 1 pcs pertinax
* 2 pcs magnet (6,5mm * 11,7mm * 8mm)

<br>

#### Step 1 (Creating the circuit and soldering the components) 

![photo](https://drive.google.com/uc?export=view&id=13wdfkZ2sbM2Q-12x4iJgmFK3kq0KoNYF)

We will combine the above supplied all parts. I combined the all parts on breadboard for try to circuit and i combined the all parts on pertinax after be success. As result it happened a compact thing like  above photo.

<br>

#### Step 2 (How is work this sensor ?)

![photo](https://drive.google.com/uc?export=view&id=1n9hwCUQuWT8c5ZpsxoqSRAOcgD4VrFMe)

This sensor take power from two pcs AA battery. Reed switch closed when magnet leave and then NPN transistor connect gnd pin to reset pin of esp8266. So esp8266 reboots. Esp8266 is going deep sleep after all process is done. Until it wake up again. It is a simple circuit to understand. **I forgot to pull gpio 0 pin to vcc while design it on fritzing. You can pull gpio 0 to vcc pin with 10k resistor.**

<br>

#### Step 3 (How is install mqtt extension on homeassistant ?)

![photo](https://drive.google.com/uc?export=view&id=1tznZQgyxmWzyA9dfksSVu8CUMgj10gy2)

I won't long describe the this point but i have watch very good video about the this step. This video is helpful about installing mqtt on Homeassistant. You should watch [this](https://www.youtube.com/watch?v=dqTn-Gk4Qeo){:target="_blank"} video if you want success for this step.

Below i gave configuration lines for this sensor the configuration.yaml from my homeassistant. 

Door sensor state configuration | Door sensor battery state configuration
:-------------------------:|:-------------------------:
![Battery Status](https://drive.google.com/uc?export=view&id=1lx3viJ77UnNVECp2HlUZLMu7glCfJQgS)  |  ![Status](https://drive.google.com/uc?export=view&id=1ktZwaCo9NGzRPtDDHHgpxqqH7WiX8JiU)

I want say about above sensor configurations. "off_delay" refers to the time taken by the sensor after the "Open" signal is sent, until the "Close" signal is sent. Battery value percent is calculating with esp8266 and then it is sending  to broker as json format. AA battery is generally kill at 2.8 voltage value so "map" method  is  bottom value 2.8 and upper value 3,2 voltage. If you wonder the map method you can acces [this](https://www.arduino.cc/reference/en/language/functions/math/map/){:target="_blank"} link.

--------------------------------------------------------------------------------------------------------

#### Advantage of this sensor

* Sensor power consumption is ultra low (during DeepSleep below 10 microamps)
* It is so cheap (about 5$ ~ 8$)
* Sensor is working so long time (about 3 years)
* You can see battery status on your home panel 
* It is working just with 2 AA battery 

#### Disadvantage of this sensor

* Sending via wifi connection is a little bit slow (just 4 seconds)
* It is sending just "Open" information when door open so not when close 

#### Source Code

```c

#include <PubSubClient.h> // Mqtt için gerekli kütüphane
#include <ESP8266WiFi.h>
#include <ArduinoJson.h>
#include <EEPROM.h>

ADC_MODE(ADC_VCC); // Vcc pininden analog okuma yapabilmek aktif edilir.

//USER CONFIGURED SECTION START//
const char* ssid = "Your Wifi SSID"; 
const char* password = "Your Wifi Password";
const char* mqtt_server = " Your Mqtt Server Ip Address";
const int mqtt_port = 1883; // Mqtt port number
const char *mqtt_user = "Your Mqtt User Name";
const char *mqtt_pass = "Your Mqtt Password";
const char *mqtt_client_name = "Your Client Name"; // for example: Outdoor Door Sensor
const char *mqtt_topic = "Your Sensor Topic"; // for example: outdoor/door_sensor
//USER CONFIGURED SECTION END//

DynamicJsonDocument doc(50);  
char buffer[50];

WiFiClient espClient;
PubSubClient client(espClient); 

//Functions

void setup_wifi()  // Wifi bağlantı fonskiyonu
{
  short int retries = 0;
  while (WiFi.status() != WL_CONNECTED && retries <= 1) 
  {
    WiFi.begin(ssid, password);
    retries++;
  }
}

void reconnect()  // Mqtt sunucusuna bağlanır ve veriyi gönderir.
{
  String battery = String(map(ESP.getVcc(),2400,3125,0,100)).c_str();
  doc["battery"] = battery;
  doc["status"] = "Open";
  serializeJson(doc,buffer);
  while(!client.connected())
  {
    delay(200);
    if(client.connect(mqtt_client_name, mqtt_user, mqtt_pass) == true)
    {
      client.publish(mqtt_topic, buffer, true);
      delay(200);
    }
  }
  ESP.deepSleep(0); // veri gönderildikten sonra modül derin uykuya geçer.
}  

void setup() {
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
}

void loop() 
{
  reconnect();
}


```
<br>

### Result 

![photo](https://drive.google.com/uc?export=view&id=14TEsbQuAkt920GQOSgotyLrLcfacSDn9)

<br>

### Video 

{% include youtube.html id='VyPQYQwkDsM' %}
