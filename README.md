# Luminifu
A functional RGB display manager.

The goal of this is to write a very functional (as in function) RGB display manager.
Thanks to this general approach, it can be adapted to nearly every setup.

##Install

####Device
```
cd device/
python3 device.py <IP> <Port>
```
####Brain
First of all, compile!
```
cd brain/
ghc --make brain.hs
```
For all future times, all you need to do is run it!
```
cd brain/
./Main <IP> <Port>
```

##How it works

###Generating frames

`f` is a function that is your perfect colourfull world.
This world will then be sampled at certain points return an array.
The sampling is done through a projection function `p`, so the morphology of the display (say a strip) is the input (for a strip, it would be an array).

To summarize, the display is projected (`s`) into an arbitrary world of color (`f`).

This has multiple advantages: only `s` needs to know the actual details of the display, it then projects this into whatever space `f` is implemented in (line, grid, torus, etc...).
`s` can also be changed to create interesting effects: waves, having it wrap around, shifting or scaling...
Given this freedom, `f` can be almost anything you want it to be!

###Network interface

####Brain side:

0. Brain tries to connect to device
0. Brain waits for device to ask it for the frame at time `t`

####Device side:

0. Once a connection is received, two `t` are sent. This allows for the brain and the client to always have something to work on.
0. Device waits for frames
0. After receiving and displaying the frame, requests the frame associated with time `t`

It is interesting to note that in this protocol, the devices are "servers" and the brain is a "centralized client".

This is implemented right above TCP.
The frames are communicated in hex form, a 3 pixel red to black gradient would be `"#FF0000#880000#000000"`.

##For other devices

Currently, the device only knows how to talk to a 49 pixel strip LPD6803s via SPI.
The brain still needs some work to allow it to send to multiple devices and the client needs to be refactored to allow more devices to be added.

Changing the client should not take much effort, I am less sure about the brain though.
