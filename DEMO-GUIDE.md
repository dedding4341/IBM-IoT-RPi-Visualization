Dennis Eddington
Raspberry Pi + Arduino IoT Dashboard Demo

Note: This document will detail the steps taken to setup a demonstration that utilizes the vast IoT potential between the powerful combination of a Raspberry Pi 3 and Arduino MKR 1000.

Required Hardware:

Arduino MKR 1000
Raspberry Pi 3
BMP183 Pressure Sensor
SHT-31D Sensor
TSL 2561 Sensor
DS3231 Precision I2C RTC
Breadboard
16GB SD Card

Download Raspbian Jessie With Desktop and flash the Image onto the 16GB SD Card

Download Raspbian Jessie: https://www.raspberrypi.org/downloads/raspbian/
Download Etcher: https://etcher.io/

Follow the first link to be taken to raspberrypi’s download page. From here you can select either Jessie with Desktop or Jessie Lite. Feel free to select whichever operating system suits your tastes. While that download is happening, ensure that you follow the second link to download AND install Etcher onto your machine. Etcher is a software that is utilized to burn images to both SD cards and USB drives, safely and easily. Once the download of the Raspbian Image and the installation of Etcher are completed open Etcher and locate the download location of the Image, select the drive that contains the information held on the SD Card and flash the image onto the SD card. By the end of this step you should have a bootable SD card that contains the Raspbian Jessie OS.

Insert the 16GB SD Card into the Raspberry Pi 3 (RPi3)

On the bottom of your RPi3 device there should be a small slot, insert the SD Card into the slot until it is secured there.

**Note**

At this point, if you would like to verify the installation of the OS and the SD Card, supply power to the RPi3. Using the USB ports, connect a keyboard and mouse. Using an HDMI cable and monitor, connect the monitor and the RPi3. If you completed the above steps properly you will be able to see the OS displayed on your monitor. Disconnect all attachments after.




Correctly place provided sensors into Arduino MKR 1000 board

Since the Watson IoT Kit has come disassembled, you need to be sure that all sensors are slotted appropriately to receive power. The way that I determined this is through trial and error. Below is a picture showing the correct sensor placement (this is the configuration I found to work best with the kit I was sent):




Power the RPi3 (Raspberry Pi 3) and connect it to the internet

Reconnect the RPi3 to the power source. The RPi3 comes with both Bluetooth and Wifi built in, so you will be able to directly connect to the Internet using the GUI of the Raspbian OS that you installed. If you do not desire to use this method however, you can opt to utilize an Ethernet Cable instead to connect to the internet.

Once connected to the internet determine the IP Address of your RPi3

This is a very simple process, open the Terminal application on your RPi3 and type the following: sudo ifconfig

Find the section labeled as “wlan0”. The IP address of your device will be listed in a similar fashion to “inet addr: xxx.xxx.x.xx”

Take note of this number as you will need it later in order to use Virtual Network Computing (VNC) with your RPi3 device.

Enable the VNC Server on your RPi3

The first step to take is to ensure that the VNC Connect is properly updated on your device. Use the following commands:

sudo apt-get update
sudo apt-get install real-vnc-server realvnc-vnc-viewer

After this you’ll want to enable the VNC Server by using raspi-config:

sudo raspi-config

Using arrow keys, navigate down to “Interfacing Options”, navigate to “P3 VNC” and then select “<Yes>” when prompted as to whether you would like to enable the VNC Server


Establish a direct connection between your RPi3 and Desktop/Laptop using the VNC Server

Download VNC Viewer: https://www.realvnc.com/en/download/viewer

Use the link provided to download and install VNC Viewer onto your machine. This will allow you to access the GUI of the RPi3 and maneuver around it virtually. Once installed it will prompt you to enter the IP address of the device you would like to connect to, enter the IP of the RPi3 device here. It will then prompt for a user ID and password, enter the default user and pass of the RPi3 device:

User: pi
Pass: raspberry

**Note**

From this point, it is acceptable to use either the RPi3 directly or to use the VNC. Whichever you prefer!

Install Informix

Download Informix: https://www-01.ibm.com/marketing/iwm/iwm/web/reg/pick.do?source=ifxids&S_TACT=109HF36W&lang=en_US

Follow the link above and ensure that it is the correct version, “Informix Developer Edition for Linux ARM v6 32 (Raspberry Pi)”. When prompted, enter your IBM ID and password (just create an account if you do not already have one) and fill out the following information accordingly. Once the download is completed create the following directory on your RPi3 /home/pi/iot-gateway-kit-depend: 

mkdir /home/pi/iot-gateway-kit-depend
cd /home/pi/iot-gateway-kit-depend
cp /home/pi/Downloads/ids.12.10.UC5DE.Linux-ARM6.tar .

Install the IBM IoT Gateway Kit onto your RPi3 device

Using terminal, navigate to the directory that you created in the previous step.

cd /home/pi/iot-gateway-kit-depend

Now we will clone the IBM IoT Gateway Kit from github into this folder using the following command:

git clone https://github.com/IBM-IoT/iot-gateway-kit
At this point you should know have both iot-gateway-kit-depend and iot-gateway-kit in your /home/pi directory. At this point simply run the install script included in the top-level of the iot-gateway-kit folder.

. /home/pi/iot-gateway-kit/iot_install

Create/Log in to your IBM Bluemix account

Sign-up for Bluemix: https://console.bluemix.net/registration/

This is a simple process, go to the link listed above and either sign into your account or create an account if you have no already done so.

Install the Arduino IDE onto the RPi3

Download Ardunio Linux ARM versison: https://www.arduino.cc/en/Main/Software 

This step will take a bit of time depending on the network you are using. To install the Arduino IDE onto your RPi3 device go to the link listed above and download the Linux ARM version of the Arduino IDE. Following successful download extract the folder and then navigate to the extracted folder using terminal. Enter the following into terminal:

sudo ./install.sh

This will install the software to your RPi3 device and it will be located on the desktop.

Configure the Arduino IDE to connect to your Arduino MKR 1000

To do this open the Arduino IDE and select Tools>Board>Boards Manager and enter Arduino MKR 1000. Download and install the software package that comes up. After this is completed, connect the Arduino MKR 1000 to the RPi3 device using a micro-USB connection. You will now be able to select the proper board and port (/dev/ttyACM0).

**Note**

Ensure that you have enabled serial communication by using

sudo raspi-config

and scrolling into “5 Interfacing Options” and select “P6 Serial” and then select “<Yes>”

Install required libraries

At this point it is now necessary to install the libraries that we will be working with in the creation of this sketch. Go to Sketch>Include Library>Manage Libraries. Search TSL2561 and install both libraries published by Adafruit. Search SHT31 and install the one library published by Adafruit. Search BMP183 and install both libraries published by Adafruit. Search unified and install the Adafruit Unified Sensor library published by Adafruit.

Use the following code to test your sensors for the Sketch

#include <SPI.h>
#include <Arduino.h>
#include <Wire.h>
#include "Adafruit_SHT31.h"
#include <Adafruit_Sensor.h>
#include <Adafruit_TSL2561_U.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP183_U.h>
#define BMP183_CS   10
Adafruit_TSL2561_Unified lightSens = Adafruit_TSL2561_Unified(TSL2561_ADDR_FLOAT, 12345);
Adafruit_SHT31 tempSens = Adafruit_SHT31();
Adafruit_BMP183_Unified bmp = Adafruit_BMP183_Unified(BMP183_CS);  // use hardware SPI
//Configuration method for TSL2561
void configureSensorTSL(void) {
  //Using auto gain in order to swap between x1 and x16 automatically
  lightSens.enableAutoRange(true);
  
  /* Changing integration time will increase resolution of TSL data (more accurate reading) */
  lightSens.setIntegrationTime(TSL2561_INTEGRATIONTIME_13MS);      /* fast but low resolution */
  

  /* Update these values depending on what you've set above! */  
  Serial.println("------------------------------------");
  Serial.print  ("Gain:         "); 
  Serial.println("Auto");
  Serial.print  ("Timing:       "); 
  Serial.println("13 ms");
  Serial.println("------------------------------------");
}

void setup() {
  // Start on 9600 baud
  Serial.begin(9600);
  delay(2000);

  //Start setup for SHT31 device
  Serial.println("Confirming SHT31");
  delay(2000);
  if (!tempSens.begin(0x44)) {   //If not found, let the user know that the device is not located
    Serial.println("SHT31 could not be located");
    while(1);
  }
  Serial.println("SHT31 Confirmed!");
  delay(2000);

  //Upon success finding SHT31, search for TSL2561
  Serial.println("Confirming TSL2561");
  delay(2000);
  if(!lightSens.begin())
  {    
    Serial.print("TSL 2561 could not be located");
    while(1);
  }
  Serial.println("TSL2561 Confirmed");
  delay(2000);

  Serial.println("Beginning TSL2561 Setup");
  delay(2000);
  //Setup TSL!
  configureSensorTSL();
  delay(2000);
  Serial.println("Setup Complete");

}

void loop() {
  //Retrieve temperature and humidity readings from sensors
  float temp = tempSens.readTemperature();
  float humidity = tempSens.readHumidity();

  if (! isnan(temp)) {  //If NaN then throw an error
    Serial.print("Temperature *C = "); Serial.println(temp);
  } else { 
    Serial.println("Failed to read temperature!");
  }
  
  if (! isnan(humidity)) {  //If NaN then throw an error
    Serial.print("Humidity % = "); Serial.println(humidity);
  } else { 
    Serial.println("Failed to read humidity!");
  }

  //Retrieve brightness readings from sensor
  sensors_event_t event;
  lightSens.getEvent(&event);
 
  /* Display the results (light is measured in lux) */
  if (event.light) {
    Serial.print("Lux = "); Serial.println(event.light); 
  } else {
    Serial.println("Sensor overload"); //According to manual, if zero, chances are the sensor is overloaded
  }
  Serial.println();

  
  delay(1000);

}

After Successfully testing your sensors use the following code

#include <SPI.h>
#include <Arduino.h>
#include <Wire.h>
#include "Adafruit_SHT31.h"
#include <Adafruit_Sensor.h>
#include <Adafruit_TSL2561_U.h>

#include <Adafruit_Sensor.h>
#include <Adafruit_BMP183_U.h>
#define BMP183_CS   10

Adafruit_TSL2561_Unified lightSens = Adafruit_TSL2561_Unified(TSL2561_ADDR_FLOAT, 12345);
Adafruit_SHT31 tempSens = Adafruit_SHT31();
Adafruit_BMP183_Unified bmp = Adafruit_BMP183_Unified(BMP183_CS);  // use hardware SPI


//Configuration method for TSL2561
void configureSensorTSL(void)
{
  //Using auto gain in order to swap between x1 and x16 automatically
  lightSens.enableAutoRange(true);
  
  /* Changing integration time will increase resolution of TSL data (more accurate reading) */
  lightSens.setIntegrationTime(TSL2561_INTEGRATIONTIME_13MS);      /* fast but low resolution */
}
void setup() {
  // Start on 9600
  Serial.begin(9600);
  delay(2000);

  //Start setup for SHT31 device
  delay(2000);
  if (!tempSens.begin(0x44)) {   //If not found, let the user know that the device is not located
    Serial.println("SHT31 could not be located");
    while(1);
  }
  delay(2000);

  //Upon success finding SHT31, search for TSL2561
  delay(2000);
  if(!lightSens.begin())
  {    
    Serial.print("TSL 2561 could not be located");
    while(1);
  }
  delay(2000);
  delay(2000);
  //Setup TSL!
  configureSensorTSL();
  delay(2000);
}

void loop() {
  //Retrieve temperature and humidity readings from sensors
  float temp = tempSens.readTemperature();
  float humidity = tempSens.readHumidity();

  if (! isnan(temp)) {  //If NaN then throw an error
    Serial.print("temperature:"); Serial.print(temp); Serial.print(“,”);
  } else { 
    Serial.println("Failed to read temperature!");
  }
  
  if (! isnan(humidity)) {  //If NaN then throw an error
    Serial.print("humidity:"); Serial.print(humidity); Serial.print(“,”);
  } else { 
    Serial.println("Failed to read humidity!");
  }

  //Retrieve brightness readings from sensor
  sensors_event_t event;
  lightSens.getEvent(&event);
 
  /* Display the results (light is measured in lux) */
  if (event.light) {
    Serial.print("lux:"); Serial.print(event.light); 
  } else {
    Serial.println("Sensor overload"); //According to manual, if zero, chances are the sensor is overloaded
  }
  Serial.println();
  delay(1000);

}

This code is similar however it has a different output format for JSON formatting later. Leave this sketch running as the application we build will read the incoming data from serial ports.

Register your RPi3 as a Gateway in the IBM Watson IoT Platform

IoT Link: https://ibm.biz/watsoniotp

By this point you should already be connected to Bluemix so follow the link above to start connecting your RPi3 to the IoT Platform as a Gateway device.

Select any name for your service, this name must be unique so it may take a try or two until you choose a valid service name. After this step has been completed go to the sidebar and find the Devices tab, select it. Choose “+ Add Device” and select “Create device type” on the window that appears. Then select “Create gateway type”. Name the device and then provide a description to help identify the device e.g: “Gateway”.  On the next screen simply select “Next”. And then hit “Next again”. In the metadata section we will write the rule for how our data should be interpreted as it is received from the RPi3. Enter the following:

{“key”:”val”}

Now select create. You’ll notice that if you go to Device Types the Device Type you just created is now listed there. Now we create the Gateway Device, start by reselected “+ Add Device”.  Select the Device Type that we created in the last step and click “Next”. At this point enter a Device ID that will distinguish the Device you are creating. Continue to select next until you get to the Device Credentials page, write this information down as it cannot be recovered later. Return to the main devices page and you will notice that the new Gateway you created is now there.

Connect the RPi3 to the Waton IoT Platform

First setup the Raspbian environment with Maven and Git using the following commands:

sudo apt-get update
sudo apt-get install maven

Following this, use git to clone the project and maven to build the project as follows:

git clone https://github.com/dedding4341/IBM-IoT-RPi-Gateway
cd IBM-IoT-RPi-Gateway/java/gateway-samples
mvn clean package

Now head back to the root directory that contains the pom.xml file. Run the following commands to start the service:

Export MAVEN_OPTS = -Djava.library.path=/usr/lib/jni
mvn exec:java –Dexec.mainClass=”com.ibm.iotf.sample.client.gateway.SampleRasPiGateway”

At this point you will notice that a new device has automatically been registered into IoT platform as Arduino01 and is sending information from the attached sensors to the platform to be analyzed.


Connect Visualization Webserver to communicate with IoT Platform

Now that we are successfully sending sensor data to the IoT platform we must create a way to clearly display/access the sensor data without having to pry through multiple JSON Objects. Start by going to your home directory and typing the following command to clone the repository:

git clone https://github.com/dedding4341/IBM-IoT-RPi-Visualization

Use terminal to navigate to the root of the cloned folder and enter the following commands to start the service:

npm install
npm start

Open a browser and go to http://localhost:7000/ and login using the API key and Authentication token generated in the previous steps. You are now able to see real time visualization of the data streaming in from your devices!








Conclusion

We have covered how to create a powerful IoT device using IBM’s Watson IoT Platform. Imagine the power of using such a small device in a company/factory setting where it can monitor multiple aspects and send you updates and alerts in real-time. This will optimize worker performance and quality of products while minimizing the risk presented to employees.




