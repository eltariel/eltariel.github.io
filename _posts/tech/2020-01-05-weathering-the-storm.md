---
layout: post
title: Weathering the Storm
tags: tech iot environment
---

I'm building a thing. Yeah, I know, this isn't surprising for someone like me.

The ongoing thing with the fires in NSW has gotten me interested in air quality
monitoring, as it turns out that breathing can be a little difficult when it's so
damn smoky outside. I've obtained a decent quality face mask to help with that, but
now I'm all curious about just what it is in our air that makes it like breathing
razor blades.

I've decided to build an air quality monitoring station. I know this is not *ever*
going to have an enormous level of accuracy, given that I'm just using cheap
hobbyist-level sensors, but as an indication of what things are like it's going to be
fantastic.

So now the technical bits. I'm still waiting for parts to arrive, but this is what
I'm putting together:
* [Raspberry Pi Zero W][pizero]
* [Pimoroni Enviro + Air Quality][enviro+]
* [PMS5003 particulate matter sensor][PMS5003]

This is cheating a little, as most of the sensors I'm after are already collected
into a pHAT already so it's mostly a matter of software once I've worked out the
best way to put it in an appropriate case and get power to it. But it's entirely
possible that most of the utility for this is in the software anyway.

Speaking of software, I plan to do two things with it. One is a dashboard to let me
keep an eye on my sensors directly. This shouldn't be too complex - aiming for maybe
a current value for each of the sensors as well as some simple statistical magic and
a bit of a graph. Maybe also alerts for when conditions are bad. The second thing is
to publish the data in a way that it's useful for others.

I've discovered [luftdaten.info][luftdaten], which maintains a network of interested people
making their air-quality sensor data available for a mapping project. There's already
a bit of coverage in [Sydney][syd-map] (although not in the CBD) but nothing near my home. I'm
getting a lot of use out of these maps, and of course I want to help out.

I'm planning to put a bunch more info online as I build this, so... stay tuned, I guess?
 
[pizero]: https://www.raspberrypi.org/products/raspberry-pi-zero-w/
[enviro+]: https://shop.pimoroni.com/products/enviro?variant=31155658457171
[PMS5003]: https://shop.pimoroni.com/products/pms5003-particulate-matter-sensor-with-cable
[luftdaten]: https://luftdaten.info/en/home-en/
[syd-map]: https://maps.sensor.community/#10/-33.6135/151.5811