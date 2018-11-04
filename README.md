/**
 * Simple ESP8266 server compliant with Mozilla's proposed WoT API
 * Based on the HelloServer example
 *
 * This Source Code Form is subject to the terms of the Mozilla Public
 * License, v. 2.0. If a copy of the MPL was not distributed with this
 * file, You can obtain one at http://mozilla.org/MPL/2.0/.
 */

#include <Arduino.h>
#include "Thing.h"
#include "WebThingAdapter.h"
#include <EthernetWebThingAdapter.h>

//TODO: Hardcode your wifi credentials here (and keep it private)
const char* ssid = "ANKITAPPS";
const char* password = "MOT";

#if defined(LED_BUILTIN)
const int ledPin = 14;
const int ledPin12d6 = 12;
#else
const int ledPin = 14;  // manully configure LED pin
const int ledPin12d6 = 12;  // manully configure LED pin
#endif

WebThingAdapter adapter("w25");

ThingDevice led("led", "Built-in LED", "onOffSwitch");
ThingProperty ledOn("on", "", BOOLEAN);

ThingDevice led2("led2", "Built-in LED-2 ", "onOffSwitch2");
ThingProperty led2On("on", "", BOOLEAN);
 
ThingPropertyValue value;

bool lastOn = false;
bool lastOn2 = false;

void setup(void){
  Serial.println("INSIDE SETUP");
  pinMode(ledPin, OUTPUT);
  pinMode(ledPin12d6,OUTPUT);
  // digitalWrite(ledPin, LOW); // active low led
 //   digitalWrite(ledPin12d6, LOW); // active low led
//  digitalWrite(ledPin, HIGH);
  Serial.begin(115200);
  
  Serial.println("");
  Serial.print("Connecting to \"");
  Serial.print(ssid);
  Serial.println("\"");
  
#if defined(ESP8266) || defined(ESP32)
  WiFi.mode(WIFI_STA);
#endif
  WiFi.begin(ssid, password);
  Serial.println("");

  // Wait for connection
  bool blink = true;
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  //  digitalWrite(ledPin, blink ? LOW : HIGH); // active low led
    blink = !blink;
  }
  //digitalWrite(ledPin, HIGH); // active low led

  Serial.println("");
  Serial.print("Connected to ");
  Serial.println(ssid);
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

Serial.println("LED PROPERTY ");
  led.addProperty(&ledOn);
  led2.addProperty(&led2On);

  Serial.println("ADDING ADAPTER");
  adapter.addDevice(&led);
  adapter.addDevice(&led2);
  
  adapter.begin();

  
  Serial.println("HTTP server started");
  Serial.print("http://");
  Serial.print(WiFi.localIP());
  Serial.print("/things/");
  Serial.println(led.id);
  Serial.print("http://");
  Serial.print(WiFi.localIP());
  Serial.print("/things/");
  Serial.println(led2.id);
}

void loop(void){
  Serial.println("INSIDE LOOP ");
  
  
  bool on = ledOn.getValue().boolean;
  digitalWrite(ledPin, on ? LOW : HIGH); // active low led
  if (on != lastOn) {
    Serial.print(led.id);
    Serial.print(": ");
    Serial.println(on);
  }
  lastOn = on;

  

    bool on2 = led2On.getValue().boolean;
    Serial.println(on2);
  digitalWrite(ledPin12d6, on2 ? LOW : HIGH); // active low led
  if (on2 != lastOn2) {
    Serial.print(led2.id);
    Serial.print(": ");
    Serial.println(on2);
  }
  lastOn2 = on2;
  adapter.update();
 
  

   
  
   
   
}
