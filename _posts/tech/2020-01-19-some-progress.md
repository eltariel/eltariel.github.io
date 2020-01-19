---
layout: post
title: Some Progress
tags: tech iot environment aqm
---

Productive weekend was productive!

I didn't do much in the way of actual development last weekend, still just
using the example code from [Pimoroni's repo](enviroplus). Instead I spent
most of the time tinkering with the hardware setup and a temporary enclosure
so I can actually run the thing outdoors. Then I ran out of time to actually
post anything about it... In the process I've found a couple of little issues:

- The humidity sensor in the BME280 appears to be reading very low, around
  20 - 30% lower than expected. Apparently I need more airflow over the
  sensor.
- Occasionally the PMS5003 returns something the sample code doesn't handle.
  Not sure if this is something in the protocol, a fault in the unit, or I
  made a mess of soldering one or more of the UART pins on the pi.
- The 20Ah battery pack I was going to run this off suddenly refused to take
  a charge. Not related to the sensor, and I had a spare battery pack anyway,
  but still annoying.


At this point, installation of the software has been fairly straightforward.
There's an existing script in the repo for pushing data to luftdaten, so I
didn't even really need to think about that. The only point where I even
really had to *think* was when I had to remember exactly how to write a
systemd unit for running it as a service.

We did manage to (accidentally) verify that the particle sensor works though.
Filling the kitchen with smoke from the oven then letting it all escape from
a window directly below the sensor made for an interesting blip in the logs!
These lines of data were recorded just before the window was opened, and
then about 30 seconds later:

```
{'P2': '5', 'pressure': '100667.08', 'P1': '6', 'temperature': '22.03', 'humidity': '41.33'}
...
{'P2': '278', 'pressure': '100664.61', 'P1': '289', 'temperature': '21.00', 'humidity': '42.86'}
```

P2 is the PM2.5 count, P1 is PM10. Note that PM10 includes all the particles
that count towards PM2.5 as well, as they're a count of particles *below* the
given sizes.

Anyway, here's some pictures!

![building a home](/public/images/2020-01-19-half-housing.JPG)
![temporary sensor house](/public/images/2020-01-19-housing-assembled.JPG) 
![some readings](/public/images/2020-01-19-some-readings.JPG) 
![data on a map!](/public/images/2020-01-19-luftdaten-map.png) 

[enviroplus]: https://github.com/pimoroni/enviroplus-python
