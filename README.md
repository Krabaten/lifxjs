# JS library for LIFX bulbs

Some experimentation with trying to interpret the network protocol for [LIFX
bulbs](http://lifx.co).  This is very much observational, so is most likely
incorrect or inaccurate, although it seems to work for me.  You have been
warned.

This is based on my setup of 2 bulbs in a single group.  That may or may not
affect the network protocol.

Ideally, I'll end up with a nice little NodeJS library which can be used to
build apps with.

## Files

There are 2 files:

  * lifx.js which is the actual library
  * cli.js which is an example CLI app using the library

My observations of the network protocol are documented in [this
doc](Protocol.md).  Hopefully LIFXLabs will release an official spec for the
network protocol which will make this a lot easier.

## Usage

The file [cli.js](cli.js) is a working example.

There are 2 fundamental objects in the library; a "gateway" object which
represents the wifi interface (and can do things like discovery of the other
bulbs, send messages to all bulbs etc) and a "bulb" object which is useful for
sending commands to individual bulbs.

To begin with, you must include the library (I've not yet made this work
through NPM, apologies) and then call lifx.Gateway.discoverAndInit with a
callback which will be called with an object representing the gateway.

```JavaScript
var lifx = require('./lifx');

lifx.Gateway.discoverAndInit(function(err, gw) {
	if (err) {
		console.log("Err " + err);
	} else {
		// Do something here
	}
});
```

Once you have the gateway object, you can use it to send commands to all of the
bulbs in the mesh network:

```Javascript
gw.lightsOn();
gw.lightsOff();
gw.lightsColour(hue,    saturation, luminance, whiteColour, fadeTime);
gw.lightsColour(0xd49e, 0xffff,     0x028f,    0x0dac,      0x0513);
```

The params are always 16-bit numbers, which get their bytes shuffled around
before being sent over the network (see the [protocol](Protocol.md) doc for the
underlying details).

* hue represents the colour to use, and is manipulated by the colour wheel in
  the iPhone app.
* saturation represents how much of the colour to use, and is generally either
  0xffff (if using the "Colors" screen in the iPhone app) or 0x0000 (if using
  the "Whites" screen in the iPhone app).
* luminance is how bright the bulbs should be.
* whiteColour is something like the colour temperatue, and is the wheel used in
  the "Whites" screen in the iPhone app.
* fadeTime says how quickly the bulbs should move to the given state, and seem
  to roughly be in milliseconds.  Use 0 for an immediate change.

Sending commands to individual bulbs is not yet implemented...

