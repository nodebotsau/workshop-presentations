# BuzzConf 2015: <br/>NodeBots Workshop
<!-- .slide: class="title" -->

November, 14 2015 <!-- .element: class="location" -->

Andrew Fisher @ajfisher<!-- .element: class="author" -->

Notes:

Hi, my name is Andrew Fisher and I'm an interaction developer and I also build
hardware using JavaScript. Our workshop today is going to be very hands on
around building NodeBots - using hardware with JavaScript.

I'm really pleased to be here and running this session at BuzzConf. Today you'll
all leave here having built a robot and got it doing some stuff. We only have
2 hours in this session but your robots are to take with you and you can keep
hacking afterwards.




---

## workshop repository

ajf.io/nodebotsbuzzconf<!-- .element class="bigtext" -->

---

## Agenda

1. Introduction to JS hardware stack
2. Hello World
3. The mbot
4. Hacking
5. More hacking?

Notes:

Today I've split things up a little so you get a basic idea of how the JS
hwardware stack works. We'll finish that with simply blinking an LED. After that
you'll then get into building your mbots and starting to program them,.

---

### Microcontrollers
<!-- .slide: data-background="/images/arduino_nano.jpg" -->

(CC) <!-- .element: class="attribution" -->
[Phil Farugia](#)

Notes:

So we typically when programming something in the real world, we use a microcontroller.
A microcontroller is like a really old computer
which you program in C and you can interact very directly with the circuit
you wire up to it. The microcontroller we'll use today is a type of arduino
made by MakeBlock and the board is called an mcore. But as far as we're concerned
it's really an Arduino uno with a bunch of things added onto it.

---

### Sensors and actuators

* LED
* Motors (x2)
* Ultrasonic sensor
* Reflectance sensor
* NeoPixels (x2)
* Light sensor
* Piezo speaker


Notes:

Now a microcontroller is just a tiny little computer, you need to plug things
into it to do meaningful stuff. Luckily on the mcore there are a bunch of things
already on it, like LEDs, neopixels, light sensors and you can plug other things
in as well like ultrasonic sensors and motors. Lots of fun,

---

### JS ❤ Robotics
<!-- .slide: data-background="/images/robot_love.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[hiperbolica](https://www.flickr.com/photos/hiperbolica/3414999010)

Notes:

Okay so we're all software peeps so that's enough hardware to get you going. So
how do we get JS working with this microcontroller that I said a moment ago
can only use C.

---

## The NodeBots stack

![](/images/logo.png)

Notes:

There are a few different projects that can use JavaScript on hardware now,
however the one we're going to talk about is called nodebots as it's very much
aimed at a NodeJS implementation with hardware. At the core of nodebots are
transport layers to deal with things like talking over USB or wireless or serial
connections and then wrapped around that is a framework called Johnny Five.

---

## Johnny Five
<!-- .slide: data-background="/images/rick.jpg" -->

(C)<!-- .element: class="attribution" -->
[Joanne Daudier](https://twitter.com/jdaudier)

Notes:

Johnny Five was started by this guy, Rick Waldron and there's now many core
contibutors around the world.

---

### The stack

* Controller board (sensors and actuators)
* IO Plugin (communications protocol)
* Johnny Five / NodeJS (application logic)
* WS/HTTP (networking and security protocols)
* Clients (UI, input, visualisation)

Notes:

So this is what the typical JS hardware stack looks like.

We have a board which could have sensors and actuators. Actuators is just a
fancy word for something that does something in the real world - like a motor
or an LED etc. Most controller boards can't run JS yet so we normally need to
put some firmware on them to do what we want.

This talks via a communications protocol to what is called an IO Plugin. IO
plugins are a Johnny Five idea that tries to get hardware to behave in consistent
ways via a protocol. Think of this sort of like HTTP requests and responses - the
client doesn't really care what the server does as long as it responds properly.

Johnny Five gives us hardware abstraction so we can turn motors and LEDs into
JavaScript objects and interact with them. As a side effect we get all of NodeJS
as well so that means we can start doing interesting things like linking up
with our normal web protocols. And then finally we can add clients for things
like UI, input and what not.

---

### Common implementation

* Controller board (Arduino)
* IO Plugin (Firmata over USB)
* Johnny Five / NodeJS (application logic)
* WS/HTTP (networking and security protocols)
* Clients (UI, input, visualisation)

Notes:

So in practice this is what a specific implementation of this stack looks like.
You can see we've got an arduino board in this case and the IO Plugin is
a firmware called Firmata which provides us the interface to the board for Johnny
Five. This is about the most basic and most common stack you can use but you can
see that the bit we are concerned about - being the bit in the middle pretty
much stays the same all the time.

---

## NodeBots hardware

* Servos, Motors, ESCs, Stepper motors
* Accelerometers, Gyroscopes, Compasses, IMUs
* Temperature, Proxitimity, Pressure sensors
* LEDs, NeoPixels, Pixel matrices
* Switches, Joysticks, Buttons
* LCDs

Notes:

In terms of hardware - there is a lot covered in Johnny Five and more core
components are still being added. The intent is to have the majority of the
most common electronics components you're likely to come across available in
the framework and then you can use then to compose bigger objects that then
represent your thing that you're making.

---

### Installation

* Flash board with protocol (eg Firmata)
* npm install johnny-five
* Write code
* ...
* Make an awesome JS Hardware thing

Notes:

So to get up and running, it's pretty much just a case of getting the board dev
environment going. For arduino that's prerry much just download the arduino
IDE and install it. You then put the IO protocol on the board that you need -
for an arduinon that just means using Firmata and then it's an npm install
and you are then writing code.


---

## Getting started:

ajf.io/nodebotsbuzzconf<!-- .element class="bigtext" -->

Notes:

If you haven't already, please make sure you have downloaded or cloned this
repo as we're about to get cracking in it for the rest of the session.

---

### Ex 1: Hello World
<!-- .slide: data-background="/images/hello_world.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[Daniel Novta](http://www.flickr.com/photos/vanf/5210360116)

Notes:

Okay so enough theory, it's time to get stuck in. The first exercise we are
going to do is to just make sure you understand the stack and have it all working.

Hardware often doesn't have a screen so hello world is blinking an LED.

Pull out your mcore board and connect it up.

---

### EX 1: Install mbot firmata

interchange install \<br/>
git+https://github.com/Makeblock-official/mbot_nodebots \<br/>
-a uno -p 'port' --firmata=usb

Notes:

We're going to use a very new technique to install things called interchange.
Interchange helps install different firmatas and firmwares for you on various
devices. If you want to find out more, check out nodebots interchange on npm.

So you'll need to make sure you installed your driver, the directions for which
are in the gist I linked you to before. After that run this instruction and
you should get everything download and flash to the board. This instruction
is in the gist readme as well so you can copy and paste it from there. 

---

### Ex 1: JS Code

```
var five = require("johnny-five");

var board = new five.Board();

board.on("ready", function() {

    var led = five.Led({pin:13});

    led.blink(1000);

});
```

Notes:

Now we have that, we need to talk to it from Johnny Five. You can see the code
in the repo. Explain code here.

---

### Ex 1: Run

```
node led.js
```

Notes:

Now run node and you should get a nice blinking LED.

---

## Time for droids?
<!-- .slide: data-background="/images/droids.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[⣫⣤⣇⣤](http://www.flickr.com/photos/donsolo/3768623542/)

Notes:

Okay so you've made an LED blink. Now it's time to build your robot.

---

## Going further

* Use HCSR04 ultrasonic sensor to detect distance
* Light up your neopixels based on an input
* measure the light at different points and record it
* Go wireless if you're brave using the bluetooth

---

## Resources

* johnny-five.io
* gitter.im/rwaldron/johnny-five
* ajf.io/nodebotsbuzzconf
* github.com/Makeblock-official/mbot_nodebots
* hackmelbourne.org

Notes:

So if you want to do more and look for more info, here's some places to do so.

---

# BuzzConf 2015: <br/>NodeBots Workshop
<!-- .slide: class="title" -->

November, 14 2015 <!-- .element: class="location" -->

Andrew Fisher @ajfisher<!-- .element: class="author" -->

