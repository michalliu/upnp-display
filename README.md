LCD Display showing DNLA/UPnP player status
-------------------------------------------

This little program is a UPnP control point that connects to a UPnP/DNLA
renderer anywhere in your local network.
It listens for changes in the state of the player (Title, Album etc.,
Play/Paused/Stop) and displays it on a 16x2 LCD display (These displays are
cheap, you get them for less than $3 on eBay).

### Connect the Hardware

Before we can see anything, we need to connect the LCD display.
You need
   - One HD44780 compatible 16x2 LCD display ( _very_ common and cheap display)
   - One female 13x1 header connector: one row with 13 contacts to plug into
     one row of the Raspberry Pi GPIO header.
   - Cable and soldering iron (Of course, you can do it the breadboard way
     if you like)

First: identify the pins on the LCD display. They typically have 16 connectors
(sometimes 14 when they don't have a backlight). Pin 1 is usually closer to the
edge of the board. Often marked with a '1' or a dot.

We want to connect them to the outer row of the GPIO connector, which are 13
pins. The GPIO connector _P1_ has two rows, the pins are counted in
zig-zag, so this means that we're connecting to the _even_ pins (P1-02 .. P1-26,
see http://elinux.org/RPi_Low-level_peripherals for reference).

If you put the Raspberry Pi in front of you, with the GPIO pins facing you,
then P1-02 is to your right, P1-26 is to your left.

Connect
   - **LCD 1** _(GND)_ to **GPIO Pin P1-06** (3nd from right, GND)
   - **LCD 2** _(+5V)_ to **GPIO Pin P1-04** (2nd from right, +5V)
     _Note, the first two wires are 'crossed'_
   - **LCD 3** _(contrast)_ to **LCD 1** (GND)
       _the contrast is controllable with a resistor, but connect to GND is just
        fine_
   - **LCD 4** _(RS)_ to **GPIO Pin P1-08** (4th from right, Bit 14)
   - **LCD 5** _(R/-W)_ to **LCD 1** _We only write to the display,
      so we set this pin to GND_
   - **LCD 6** _(Clock or Enable)_ to **GPIO Pin P1-12** (6th from right, Bit 18)
   - LCD 7 to LCD 10 are _not connected_
   - **LCD 11** _(Data 4)_ to **GPIO Pin P1-16** (8th from right, Bit 23)
   - **LCD 12** _(Data 5)_ to **GPIO Pin P1-18** (9th from right, Bit 24)
   - **LCD 13** _(Data 6)_ to **GPIO Pin P1-22** (11th from right, Bit 25)
   - **LCD 14** _(Data 7)_ to **GPIO Pin P1-24** (12th from right, Bit 8)

Cross check: With this connection, you should end up with the following
pins not connected:
   - LCD Pin 7, 8, 9, 10 (and 15, 16 if it has these pins)
   - GPIO P1-02, P1-10, P1-14, P1-20, P1-26 (seen from the right, this
     is pin 1, 5, 7, 10, 13).

I would suggest to first connect short cables to all LCD pins that need to be
connected, then connect them right to left to the 13x1 header. The first two
cables end up crossing over, all others are nicely sequenced.

### Compile the program

You need to have libupnp installed

    sudo apt-get install libupnp-dev

Get the source. If this is your first time using git, you first need to install
it:

    sudo apt-get install git

.. Then check out the source:

    git clone https://github.com/hzeller/upnp-display.git

.. Now you can compile it
   
    cd upnp-display
    make


### Start the program

You need to start the program as root, as it needs to access the GPIO pins:

    sudo ./upnp-display

In the LCD display, it should now print that it is waiting for any renderer;
once it found a renderer, it will display the title/album playing.

If you have multiple renderers in your network, you can select the particular
one you're interested in with the `-n` option:

    sudo ./upnp-display -n "Living Room"

### Compatiblity

This should work with all renderers, that do proper eventing of variable
changes. This program does not, at this time, actively query the renderer
but expects it to transmit changes according to the UPnP eventing standard.

Right now, this is tested with
[gmrender-resurrect](http://github.com/hzeller/gmrender-resurrect)
... which is perfect in the network or even run on the same machine.