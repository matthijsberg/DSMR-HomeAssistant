# DSMR-HomeAssistant
Adjusted version of DSMR code for Marcel Zuidwijk his DSMR sensor board (https://www.zuidwijk.com/product-tag/dsmr/). He wrote a blog on how to get this working in Home Assistant via ESPHome here; https://www.zuidwijk.com/esphome-powered-p1-meter/

This version uses or is based on:
 - Marcel his hardware (tested with the Wemos 3.0 wireless reader)
 - This library on github (No need to download, this lib contains a modified version to add some sensors): https://github.com/nldroid/CustomP1UartComponent
 - This DSMR parser lib (no need to download); https://github.com/matthijskooijman/arduino-dsmr
 - Home Assistant (if you don't know what that is, your in the wrong place)
 - ESP home in HA (if you don't know wat that is, start learning very fast)

# Goals
Get more sensors visible in HA (power swell, etc) and ability to enbele MQTT. Again, credit where credit is due, I just combine a lot of work / features that others buid. I only etended the nldroid library to add some sensors (that I think have bee left out deliberately) and use some ESPhome features in the config. Done. 

# How to

## Pre-req.
 - A working Home Assistant config
 - A working ESPHome Add-on (default Add-on in HA)
 - A file editior like VSCode in HA to edit / add files in Home Assistant.
 - A cable between your HA Server and Marcel his DSMR reader for initial flash (following flash actions are done over the air)
 - Coffee. 

## Guide
 - Add the dsmr_p1_sensor-mb.h file to the root of the esph folder in your config dir (/config/esphome/dsmr_p1_sensor-mb.h)
 - create a new node in the ESP home GUI, go over the wizard to fill in some nice things.
