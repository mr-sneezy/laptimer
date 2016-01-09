# laptimer:  Arduino magnetic laptimer for karting

**Table of Contents** 

- [Purpose](#purpose)
- [Features](#features)
- [Materials and Wiring](#materials-and-wiring)
- [Operation](#operation)
  - [Single sector mode](#single-sector-mode)
  - [Multi sector mode](#multi-sector-mode)
- [Future improvements and notes](#future-improvements-and-notes)

## Purpose
From time to time I like to race my friends in hired gokarts. After the race we usually get a transscript of the individual lap times achieved. I just really can not remember if it was due to traffic or early breaking that lap 6 was slow, thus I have a hard time improving my racing skills. In my mind the cure is to have the lap times instantly.

After some research I found out that most gokart tacks in Europe have 1 to 3 magnetic strips placed in the track. I figured that an Arduino with a readswitch and a display should be able to register each passage over a magnetic strip and display the time between each passage.

This build is made for the purpose to mount in a hired gokart, but it could be used as a permanent installation if you own your own kart, and possible also for motorcycles even this is not the main focus.

## Features
* Configurable number of sectors between 1 and 3
* Interrupt driven
* Multiple display options

## Materials and Wiring
* Arduino Uno
* [Arduino LCD Keypad Shield](https://www.dfrobot.com/wiki/index.php?title=Arduino_LCD_KeyPad_Shield_(SKU:_DFR0009))
* Reed switch
* 2 magnets and glue
* Old 3 wire power cord
* Plastic lab tube or similar
* 9V Battery and Adapter

![Overview](https://raw.githubusercontent.com/SeniorWizard/laptimer/master/laptimer_overview.jpg)


The keys are attached to `A0` and the display uses `D4` to `D10` when attached to the Arduino.

The reed switch has ground to `GND` an vin to `3V3` and digital out to `D2` - the last part is important as this is the first of the two only pins which can be used to attach an interrupt on an Arduino uno. 

## Operation

Mount the lap timer by attaching the display to the steering wheel using the magnets, place the reed-switch in the bottom of the kart as near the ground as possible.

Holding down `select` for two seconds open configuration mode where `up` or `right` increases the number of sectors by one and `down`or `left` decreases the number by one. Pressing `select` again stores the chosen value in flash and will be read and used as default after boot.

![Configuration](https://raw.githubusercontent.com/SeniorWizard/laptimer/master/laptimer_config.jpg)

After boot or configuration changes the data is wiped and the lap timer goes into `WAIT` mode that is waiting for first passage of a magnet strip which indicates we are starting the first lap.

The display will differ dependent on whether the tack only has one magnet strip and each passage corresponds to a lap or it has several (up to 3) where split times will be shown.

### Single sector mode
![single sector](https://raw.githubusercontent.com/SeniorWizard/laptimer/master/laptimer_single_sector.jpg)

First line of the display shows:
* Last laptime
* Last difference 
* current status

Second line shows:
* best laptime
* current lap

On the image is shown the first 3 laps.

* Initially we are waiting for the start, status are `WAIT` and all times are undefined signalled with `*`
* When we cross the strip the first time the internal timer is started, and the status is changed to `RACE` and the lap number is now `1`
* When we pass the second time the first lap is completed. Both last lap and best lap is recorded as `10.95` and the lap counter is incremented
* Next passage is done after `12.91` a slower time indicated by the difference of `+1.96`
* Next passage is done after `10.73` a new best by `-0.22` also indicated by the status as well as the display blinking

### Multi sector mode
![multi sector](https://raw.githubusercontent.com/SeniorWizard/laptimer/master/laptimer_multi_sector.jpg)

First line of the display shows:
* Last sector time
* last sector time compared to previous best 
* current status

Second line shows:
* current rolling lap time, that is last full lap with reference to the current sector. If we are on a track with 3 sectors and pass start-finish line it would be the sum of sector1+sector2+sector3 as expected. When we complete sector1 the new sector time will be used instead thus giving a pseudo lap time corresponding to sector 1.
* last rolling lap time compared to previous best 
* current lap and current sector

On the image is shown 4 passages completing the second lap to illustrate the idea:
* first sector is completed in `18.42` `2.08` slower than the first passage
* second sector is completed in `19.78` `5.55` slower than the first passage, before we were `1.00` behind thus now we are `6.55` slower on the rolling lap
* third sector also slower, note that we are now more than 10 seconds slower indicated by `+9.99` as we pass the start-finish line to lap 3
* first sector is then completed in `14.40`, a new best for this sector by `-1.94`, while our previous slow sector 2 and 3 still makes our pseudo lap time slower by almost 10 seconds.

## Future improvements and notes

It is a bit clumsy and heavy, and there are a lot of vibrations in a gokart. Make use of a smaller and lighter display and a Arduino nano would help a lot. Alternative just using a buzzer and some kind of beep/knock system could be almost as effective and remove the needs for both wire and display (eg one short beep for each tenth faster, and long beep for slower).
