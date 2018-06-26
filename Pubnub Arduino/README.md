# PubNub Publish on Arduino with Ethernet






```c++
#include "DHT.h"
#define DHTPIN A0     // what pin we're connected to
#define DHTTYPE DHT22
#include <SPI.h>
#include <Ethernet.h>
#include <PubNub.h>
#include <aJSON.h>

DHT dht(DHTPIN, DHTTYPE);

byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };

const int pubLedPin = 9;
char pubkey[] = "pub-c-1ac1b45b-0c9f-498e-9521-d4edfa40f04d";
char subkey[] = "sub-c-0e344d0e-6eeb-11e8-a49b-66b3abd5adf6";
char channel[] = "pubnub-mapbox";
int lat = 48;//48.534625, 9.175335
int lng = 9;
void setup()
{
  pinMode(pubLedPin, OUTPUT);
  digitalWrite(pubLedPin, LOW);

  Serial.begin(9600);
  Serial.println("DHTxx test!");

    dht.begin();
  Serial.println("Serial set up");

  while (!Ethernet.begin(mac)) {
    Serial.println("Ethernet setup error");
    delay(1000);
  }
  Serial.println("Ethernet set up");

  PubNub.begin(pubkey, subkey);
  Serial.println("PubNub set up");
}

void flash(int ledPin)
{
  
  for (int i = 0; i < 3; i++) {
    digitalWrite(ledPin, HIGH);
    delay(100);
    digitalWrite(ledPin, LOW);
    delay(100);
  }
}

void loop()
{
   float h = dht.readHumidity();
   float t = dht.readTemperature();

    // check if returns are valid, if they are NaN (not a number) then something went wrong!
    if (isnan(t) || isnan(h))
    {
        Serial.println("Failed to read from DHT");
    }
    else
    {
      //Print Sensor Data
        Serial.print("Humidity: ");
        Serial.print(h);
        Serial.print(" %\t");
        Serial.print("Temperature: ");
        Serial.print(t);
        Serial.println(" *C");

        //->Trying to store data as Json
        aJsonObject *root,*fmt;
        root=aJson.createObject();  
        aJson.addItemToObject(root, "temp", aJson.createItem(t));
        aJson.addItemToObject(root, "latlng", fmt = aJson.createObject());
        aJson.addNumberToObject(fmt,"lat",    33);
        aJson.addNumberToObject(fmt,"lng",   9);

          }


   Ethernet.maintain();

    EthernetClient *client;

    //->Hardcoded string to mock publish data
    char latlngmsg[124] = "[{\"latlng\":[48.534625, 9.175335],\"temp\":[11.11";

    sprintf(latlngmsg + strlen(latlngmsg), "%d", t );

  strcat(latlngmsg, ", 4545.78]}]");





  Serial.print("publishing message: ");
  Serial.println(latlngmsg);

  //->Pubnub Publish -What is the exact message type?
  client = PubNub.publish(channel, latlngmsg);
  if (!client) {
    Serial.println("publishing error");
  } else {
    flash(pubLedPin);
    client->stop();
  }

  delay(5000);
}
```
