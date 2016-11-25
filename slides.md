# Internet of Tents Workshop
<!-- .slide: class="title" -->

BuzzConf: November, 26 2016 <!-- .element: class="location" -->

Andrew Fisher @ajfisher<!-- .element: class="author" -->

Notes:

Hi, my name is Andrew Fisher and I'm an interaction developer and I also build
hardware using JavaScript and other web related technologies.

Our session this afternoon is for 2 hours though we can continue to build after
for those that want to keep going.

---

### Building the Internet of Tents
<!-- .slide: data-background="/images/internet_tents.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[JoJo Tyhurst](https://www.flickr.com/photos/59960371@N06/7461131356/)

Notes:

Okay so this workshop is going to be very hands on after a quick intro to the
various parts of the tech you'll be using. After that, you'll be building a physical,
Internet connected thing - using hardware and javascript.

We have plenty of gear you can use here, likewise you're welcome to sign out
equipment to borrow until tomorrow and we do have some kit you can purchase as
well (and yes, we do take cards).

---

## workshop repository

github.com/nodebotsau/internetoftents<!-- .element class="bigtext" -->

---

## Agenda

1. The IoT stack
2. Hello, Internet
3. Building a Thing

Notes:

Today I've split things up a little in order to get you a basic idea of how
the hardware stack works. From there I'll give you a demo and show you some
code of how to build a simple Thing. After that we'll get into building your
own devices for the rest of the session.

---

## Building Things

* Electronics / Board
* Network
* Power
* Security
* Connectivity

Notes:

When working with Internet enabled physical things theres a few aspects we need
to consider which I've broadly outlined here.

What are the electronics you're using? Is it an arduino or a Raspberry Pi or
some other board?

How are you connected to the
network and how are you maintaining that connection?

As we're talking about electronics, how are you getting power?

What type of security do you need and finally what are you connecting to?

Let's quickly cover each of these and talk about them in the context of what
we're doing here at BuzzConf.

---

### Electronics
<!-- .slide: data-background="/images/arduino_nano.jpg" -->

(CC) <!-- .element: class="attribution" -->
[Phil Farugia](#)

Notes:

When we're building things to go in the real world we're typically using a
microcontroller. However these tend to be fairly low spec - they aren't like
a real PC with loads of RAM and a full OS.

Usually you're programming them in C in order to do very low level activities
such as turning pins on and off based on some criteria or external input.

Some boards have radio chips in them like some of the ones we'll be using
today but others, like this arduino don't, so you'll need to add a method for it
to talk to the outside world. Which brings me to the Network.

---

### Network
<!-- .slide: data-background="/images/networking.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[Norlando Pobre](https://www.flickr.com/photos/npobre/8437956869/)

Notes:

An Internet Thing won't do much if it's not connected to some kind of network.
Most often people think of WiFi and that's mostly what we'll be using. This is
because it's relatively ubiquitous and the chips have got really inexpensive
because they are widely used. But you can also use Bluetooth and connect out
via a phone. You can use GSM directly as well as other methods such as 6lowpan
which allows for meshing networks or LoRa which allows low speed but long range
communications. Likewise, you can use plain old Wired ethernet.

Obviously, standard network protocols are your friend and at least start with
something simple enough to get going.

But the next thing you have to contend with is power.

---

### Power
<!-- .slide: data-background="/images/power.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[Jim Sher](https://www.flickr.com/photos/blyzz/3246221449/)

Notes:

WiFi is awesome, but gosh it's power hungry. High bandwidth protocols get their
bandwidth by broadcasting at high frequencies using a lot of energy. WiFi also
is a relatively reliable protocol and because of that there's a lot of chatter
back and forth between the router and the station in order to create that
reliability and manage the connection.

If you haven't got mains power though and have to use a battery then a power
hungry protocol isn't that awesome. You can mitigate for this by sleeping the
radio and what not but power will be a constant concern especially if you're
using batteries.

So in terms of power, as I mentioned we can use mains, we can use batteries
and we can also look at ways such as solar to charge up our batteries. Our
nodes here use a mix of these approaches depending on what they are doing.

---

### Security
<!-- .slide: data-background="/images/security.jpg" -->

(CC) Flickr <!-- .element: class="attribution" -->
[Hery Zo Rakotondramanana](https://www.flickr.com/photos/saveoursmile/5433411025/)

Notes:

Okay so just google IoT hacks and you'll see that to date the various vendors
haven't perhaps done the best job of securing their devices. The recent botnet
of IP security cameras doing a DDoS on internet infrastructure wasn't the
finest moment in recent memory.

Obviously everything should start from an assessment of risk and potential
vectors of attack. We could do a whole workshop on this but to discuss what
we're doing for a moment.

No nodes or infrastructure is directly accessible from the Internet. Whilst
someone on the LAN here can see everything in terms of data, none of it is
sufficiently risky that were it made public it would be a problem. Further
we could easily restrict access using authentication and we could make sure
all traffic is over TLS however that will make it harder for all of you. So
that's an obvious improvement that could be made quickly and something you would
do by default in a non-transient environment.

Finally, our nodes only do one thing. So even were they to be compromised in
some fashion they don't allow access to change them so they can't be coerced
into another role such as conducting DOS attacks etc.

Anyway, it's a consideration and if you ever work on this stuff go and employ
a security researcher to come and hack and audit your systems as they are
more than worth the expense.

---

### Connectivity
<!-- .slide: data-background="/images/connectivity.png" -->

(CC) Flickr <!-- .element: class="attribution" -->
[Simon Cockell](https://www.flickr.com/photos/sjcockell/4684828794/)

Notes:

And finally our last consideration is connectivity. How is this different than
the network - well for me this makes the distinction between HOW you communicate
which is the network's job and the WHAT and WHY you're actually doing with your service
which is the connectivity side of things.

Agains there's options. A lot of people opt for things like HTTP and rest because
it's relatively simple and well known. It does mean your Thing needs to either
provide or understand the HTTP stack though which isn't small.

You can also opt for just sending data over TCP / UDP which whilst inelegant is
very practical if you have a very constrained set of data.

However, one of the things that is interesting about IoT is that it's a very
distributed system so having things tightly coupled and always connected between
endpoints and a server doesn't create much resilience. You might have heard
about the pet feeder earlier this year that stopped working over a weekend
because the vendor's server went down.

So the protocol we'll be using is called MQTT and I'll give you a bit more
detail about that next. The reason we use this is because it's well suited to
transient nodes and a distriubuted system that can have actions at
levels both locally and remote.

---

## Hello, Internet
<!-- .slide: data-background="/images/hello_world.jpg" -->

(CC) <!-- .element: class="attribution" -->
[Arduino](https://arduino.cc)

Notes:

Now you've had a crash course on the things we need to think about before we
make a Thing, let's go and build one.

Our thing in this case is going to be really simple - it's the classic
electronics Hello World example of an LED light, however in our case we're
going to make it accessible to the network so you can control it remotely.

---

### Messaging and MQTT

* Message queue
* Self contained payloads
* May take any data including binary

Notes:

Okay so let's quickly talk about MQTT as this will be the basis on which
we're doing everything. MQTT is a message queue that was designed for telemetry
transport - which sounds suspiciously like what we're doing.

It uses messages that are wholly contained payloads of information. If you've
used message queues before this won't be new at all. The idea is to encapsulate
all the information you need in the payload of a message and then let other
services act upon it if it's of interest.

---

### PubSub

```
ESP_e523cd/temp/c
ESP_e523cd/temp/k
ESP_e523cd/pressure/pa
ESP_e523cd/#
#/temp/c
```

Notes:

How do we know it's of interest, well that's where Publication and Subscription
come in.

MQTT has the notion of Topics. You can think of a topic as being like a chat room
and it has an address. This first line is a topic that has been set up for the
node ESP_e5 blah temperature celcius topic. This is like a directory
path so you can have containing collections such as here you can see we have
temp and it has sub topics for both celcius and kelvin in this case.

So the node can publish to the Broker on a topic and they are all flexible so you can
have pretty much whatever you want so long as it's a usable string. I haven't
tested emoji but you could give it a go I guess.

Now on the other side, nodes or other actors can subscribe to a topic or in fact
a set of topics. So if I'm interested in the celcius temperature of this particular
node I can subscribe to receive messages from it whenever one is published.

But say I wanted to look at all messages off this node. It might be a weather
station so it's got more data than just temperature. Here I can use a wildcard
which is the hash character. In this case whenever a message that is below the
level of the ESP... gets published, I will receive it as part of my subscription.

Likewise I could use these wildcards to get all temperature values regardless of
the node so I could do things like aggregation or what not.

So pub sub is very flexibile in this regard. If you've used websockets before
this will look fairly familiar.

---

### Storage

* Store what is of importance
* InfluxDB good for time series data
* Redis, Mongo for KV
* Relational for traditional storage
* MQTT can retain last message to a topic

Notes:

By default MQTT brokers don't usually store the messages, they simply take them
pass them onto the subscribed actors and then remove them from the queue. It's
possible to store them in a range of systems such as Influx DB if you want
time series data but you can also use Redis, Mongo or even just a basic relational
database. Obviously the more data you are generating these can fill up pretty
fast.

One exception to this is using the retain flag on your message. If you publish
a message with the retain flag set, it tells the MQTT server to hold onto a
copy of this message until it's replaced or nulled out. This has the benefit
that for transient nodes that are popping on and off the network they can be
sent the last value of a topic if they are subscribed to it. We use this
feature heavily for our nodes here where we tell them how long to sleep for
and what mode they should be in.

Now we know the basics of MQTT, let's make our system. If you have the repo
prereqs installed and an arduino nearby you can follow along.

---

### Prototype 1 - Arduino + Laptop

* Node Red to do the hard work
* Arduino runs Firmata protocol (same as NodeBots)
* Connection over USB Serial


Notes:

Our first prototype is going to be pretty simple. It's our LED here connected
to a standard Arduino and we'll have that connected to my laptop. In this
case my laptop is providing the connectivity and network access to the arduino
which I'm using over USB.

To make this a quick prototype I'm going to use NodeRed which allows me to
use a browser and some flow based programming to join some parts together.

So here I have my local node-red server running and I can add a UI element
which turns on and off.

Great so I've proven I can make a simple LED turn on and off with some UI
that's a good first step.

---

### Prototype 2 - Wireless node

- node subscribed to `lights/state` topic
- publish message `on` or `off` to topic
- wire to UI or other input

Notes:

Our second prototype will take this further. Now, instead of the arduino
connected over serial, we'll use this ESP8266 wireless microcontroller.

This has an LED on it and is subscribed to the lights/state topic. All I need
to do is send it a message of on or off and the lights should turn on or
off.

So let's try that. Yay it works now we could hook this up to just about anything.

---

### Prototype 3 - JS version

```
CODE
```

Notes:

Let's do a version of this in JS. This time we'll write a little command
line application which can send a message to MQTT to control the lights.

Here you can see some simple code, I parse the option of my command line
and then just publish a message on the appropriate topic after connecting to
the MQTT server.

---

### Prototype 4 - Light activated version

* Sensor on `sensor/light`
* Generates data every second
* can use it in multiple ways

Notes:

Our final example is going to use Node Red again for speed. Basically in
this scenario we're going to have a light sensor give us the ambient light
reading. If it's bright we want to send a message to the MQTT server to turn
the light off and if it's dark then we want it to turn on.

So in a distributed fashion we can do a few things now. First we get the
values of the light every second off it's topic. And as we have that we may as well send that
to a graph so we can see the data.

Then we can put a condition on it saying that if it's over a threshold then
send an MQTT message to the light to go on otherwise turn off.

---

## Make the Internet of Tents
<!-- .slide: data-background="/images/tools.jpg" -->

(CC) Flickr<!-- .element: class="attribution" -->
[Peter Hellberg](https://www.flickr.com/photos/peterhellberg/4572432746/)


Notes:

Okay so that's some very basic examples to get you familiar with working with
Node Red and MQTT and doing it in JS as well. There is heaps you can do with
this.

So from here on you'll be creating your own modules or using the data sources
available on the network to create other services.

---

## Available services

* MQTT Server is at mqtt-server.local, IP ()
* Please publish test stuff to `test/foo`
* Temperature available on `#/temp/c`
* Pressure on `#/pressure/pa`
* Humidity on `#/humidity`
* Slack #iotents channel to post your services and we'll pin them

Notes:

Here's some basic services that we've put together which you can use.

---

## Make your own

* github.com/nodebotsau/internetoftents
* preconfigured ESP Nodes available can do (Temp, Servo and NeoPixels)
* Build your own firmware!

Notes:

The easiest way to get going is to use an arduino on your laptop and get started
posting to MQTT using nodered

Likewise we have nodes preconfigured to do things like move a servo, display
NeoPixels or take temperature readings. So feel free to use them if you want.

Otherwise, build your own firmware using Ardunino and we'll love to see what
you make.

---

## Resources

* mqtt.org
* github.com/nodebotsau/internetoftents
* nodebotsau.io
* hackmelbourne.org

Notes:

So if you want to do more and look for more info, here's some places to do so.

---

# Internet of Tents Workshop
<!-- .slide: class="title" -->

BuzzConf: November, 26 2016 <!-- .element: class="location" -->

Andrew Fisher @ajfisher<!-- .element: class="author" -->
