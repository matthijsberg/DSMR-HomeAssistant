# DSMR-HomeAssistant
Adjusted version of DSMR code for Marcel Zuidwijk his DSMR sensor board (https://www.zuidwijk.com/product-tag/dsmr/). He wrote a blog on how to get this working in Home Assistant via ESPHome here; https://www.zuidwijk.com/esphome-powered-p1-meter/

This version uses or is based on:
 - Marcel his hardware (tested with the Wemos 3.0 wireless reader)
 - This library on github (No need to download, this lib contains a modified version to add some sensors): https://github.com/nldroid/CustomP1UartComponent
 - This DSMR parser lib (no need to download); https://github.com/matthijskooijman/arduino-dsmr
 - Home Assistant (if you don't know what that is, your in the wrong place)
 - ESP home in HA (if you don't know wat that is, start learning very fast)

# Goals
Get more sensors visible in HA (power swell, etc), different names (to match the values I had when using the wires DSMR interface in HA directly) and ability to enbele MQTT. Again, credit where credit is due, I just combine a lot of work / features that others buid. I only etended the nldroid library to add some sensors (that I think have bee left out deliberately) and use some ESPhome features in the config. Done. 

# How to

## Pre-req.
 - A working Home Assistant config
 - A working ESPHome Add-on (default Add-on in HA)
 - A file editior like VSCode in HA to edit / add files in Home Assistant.
 - A cable between your HA Server and Marcel his DSMR reader for initial flash (following flash actions are done over the air)
 - Coffee. 

## Guide
 - Add the dsmr_p1_sensor-mb.h file to the root of the esph folder in your config dir (/config/esphome/dsmr_p1_sensor-mb.h)
 - create a new node in the ESP home GUI, go over the wizard to fill in some nice things. We'll edit the file afterwards, just make sure that you have:
   - The correct name for the device (since this will be used with mDNS for OTA updates)
   - ESP8266 as the platform
   - The rest depends mostly on you environment I guess
 - When the config is created, make sure it looks like the p1_dsmr_zuidwijk.yaml config in this repo. 
   - the name and file name should be what you defined when creating the sensor
   - I use secrets in my config, if you want that too, create the WiFi, Passwords, etc. in the secrets editor in ESPHome (top right Hamburger menu, Secrets Editior)
 - Change the interface to you USB connection to the sensors (top right in screen) in stead of "Over The Air"
 - Flash the device (WARNING, your old config will be gone!)
 - When flashing completes sucessfully the device should turn green in the overview and you should see data coming in when monitoring the logs.
 - NOTE: When the device is not connected to the WiFi it will reboor every 15 minutes. So when you apply this to a wired version, make sure you adjust the API var in the config to avoid this (see ESPhome docs). 

When you go to Confguration / Integrations the device should show up to be added. In Configuration / Devices in HA and find the sensor and when you open the details you should see all the entities with the DSMR meter values. :-) 

# Bonus
Some additional handy things you can use in HA. 

## Daily / Monthly and Yearly counters
The gas meter shows a sum or all time value only, but does so every 5 minutes. You can create beautifull hourly / daily / monthly / yearly consumptions sensors with HA. Now, this may need some tweaking from your side since my config is so old and scared that I'm not sure why I did certain things. Bear with me;

First create integration sensors in the `sensor` part of your configuration:
```
# Engery meters omdat het in KW niet in KWh wordt weergeven
- platform: integration
  source: sensor.power_consumption
  name: energy_consumed
  round: 2
- platform: integration
  source: sensor.power_production
  name: energy_returned
  round: 2
```

Than add Utility Meter (https://www.home-assistant.io/integrations/utility_meter/) config to your configration file. This will create new meter devices in you HA config that will reset based on the time period you determine. So instead of a all time increasing value you see daily / monthly and yearly consumption values. (if you produce power too, add config lines accordingly)

```
utility_meter:
  #uses integration sensors in sensors.yaml
  daily_energy_consumption:
    source: sensor.energy_consumed
    cycle: daily
  daily_energy_returned:
    source: sensor.energy_returned
    cycle: daily
  daily_gas_consumption:
    source: sensor.gas_consumption
    cycle: daily
  monthly_energy_consumption:
    source: sensor.energy_consumed
    cycle: monthly
  monthly_energy_returned:
    source: sensor.energy_returned
    cycle: monthly
  monthly_gas_consumption:
    source: sensor.gas_consumption
    cycle: monthly
  yearly_energy_consumption:
    source: sensor.energy_consumed
    cycle: yearly
  yearly_energy_returned:
    source: sensor.energy_returned
    cycle: yearly
  yearly_gas_consumption:
    source: sensor.gas_consumption
    cycle: yearly
```

## "Realtime" gas consumption and DSMR 4 hourly value recreation
For Electricity you get a interval value that gice you the usage of electricity at that point in time. For gas, you only get the totalling value im M3. using the meters about can get the usage on for example daily bases, but the smallest value you can use with the "utility_meter" integration in 15 minutes. To build a 15 minutes and hourly (my DSMR 4 meter had that value, and I have a LOT of history in Influx from that) use the folling add-on part for the utility_meter config

```
  quarter-hourly_gas_consumption:
    source: sensor.gas_consumption
    cycle: quarter-hourly
  hourly_gas_consumption:
    source: sensor.gas_consumption
    cycle: hourly
```

## MQTT from Sensor
This is not rocket sience. Some basics, when using ESPhome data is send over a websocket connection to HA. This is the preffered method since some time for integration and overhead reasons apperently. You can still push data over MQTT out too with ESPHome. 

Adding this should get MQTT in all it's basics running for you: # Example configuration entry
```
mqtt:
  broker: 10.0.0.2
  username: livingroom
  password: MyMQTTPassword
```

Now MQTT is not easy, as there are many config options, so from here on i'll point you to the ESPHome docs for it; https://esphome.io/components/mqtt.html . DO read, especially the red box, if you plan on using this.
  
