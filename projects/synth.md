# The Tiniest Raspberry Pi Synth
Have you ever heard of MIDI? The _Musical Instrument Digital Interface_ is a well established technology used to tell computers how to play sounds and a staple in moderm music production.
_MIDI controllers_ are probably its most popular application. Simple devices that can't produce sounds on their own, yet are very expressive when coupled with good music production software.
In this guide, we're going to assemble a tiny software sinthesizer using a Raspberry Pi Zero and a few audio devices.

[picture]

## Ingredients
Besides your Raspberry Pi Zero and the usual essential power adapter and micro SD card, you're going to need at the very least:

- A sound interface and speakers (or headphones) to make sounds. Here I'm going with the incredibly fun Pimoroni Speaker pHAT, more on it later.
- A USB MIDI controller to play on. Any type you want, although keyboards, drum pads and guitars work best for this guide. You can even build your own _Monome-like_ device, if you feel exceptionally crafty.
- A USB to micro-USB adapter for your MIDI controller, unless it features a micro USB plug already.

Optionally:

- A network interface, either wi-fi or ethernet. It's necessary if you, like me, prefer to work on the Pi system over the network. If you happen to have a Raspberry Pi Zero W, then great! You already have an embedded wi-fi interface.
- Keyboard and monitor for the first access to your Pi Zero. It's the easiest option to setup your wi-fi and enable remote access. If you know your way around linux, I probably don't have to tell you that you can mount the sd card filesystem on your laptop and make all the configurations you want.
- A USB hub always comes in handy when you're dealing with multiple devices and you're constrained to a single port. If the MIDI controller is your own USB device, you're not going to need a hub.

You can stop here if you decided to use a USB sound interface, but if you're set on following my lead instead, you're also going to need some tools to assemble the GPIO sound card:

- Soldering iron and some solder.
- A 2x20 0.1" male GPIO pin header, like [this one](https://www.adafruit.com/product/2822).

## The sound interface
You probably already noticed that the Pi Zero doesn't have any on-board speakers and if you've ever seen the bigger models, you've also noticed that it doesn't have any audio outputs either.
Unlike the rest of the Raspberry Pi family, in fact, the Zero ships without an integrated audio filter. It's one of the trade-offs of its very compact size.

No worries, we have plenty of options. In this guide, I'm going to be using the Speaker pHAT, a sound card developed by the excellent folks at Pimoroni which connects to the Raspberry Pi through the GPIO pins and communicates over the I2S protocol.
You don't actually need to know what all of that means, but if you're curious there are tons of documentation on the [Raspberry Pi website](https://www.raspberrypi.org/documentation/hardware/raspberrypi/gpio).
I chose the Speker pHAT because it integrates the two main components I'm actually going to need to produce sounds in a single board: a Digital-to-Analog audio Converter, or DAC, and a speaker. Also, since it connects over GPIO, it leaves me free to use the one micro USB port available on the Pi Zero, contributing to the overall lilliputian format.

Oh, and did I mention that it looks _really_ cool? Look at that LED vu meter.
[picture]

Of course the tiny 2W speaker brings some downsides. As per the manifacturer's own admission, we can't expect audiophile quality or loud volumes. I don't mind, though. I want an ultra portable synth, after all.

If you're looking for higher quality, instead, you'll probably want to go with a better GPIO sound card and external speakers or headphones.
If you want to avoid soldering altogheter, a cheap USB soundcard will be prefectly suited for the job.

Let's get started!

## Hardware setup
Assuming you're still with me and committed to the Speaker pHAT idea, or if you chose to use another type of GPIO sound card, you're up for some soldering now. If you're going for the USB audio solution instead, just plug in the sound card and skip this whole section.

Start with soldering the male GPIO pin header to the Raspberry PI Zero, making sure that the pins point upwards, like this.
[picture]

If you find difficult to immobilize the pins while soldering, try wrapping some masking tape around the mounted header. Solder a few pins on the sides, then remove the tape and finish the work.

You'll then need to assembre your Speaker pHAT. Pimoroni provides us with a [simple guide](https://learn.pimoroni.com/tutorial/sandyj/assembling-speaker-phat),
but fundamentally you'll need to solder the female GPIO header included in the kit in just the same way you did for the male header, this time making sure that the socket is facing downwards.
[picture]

Finish up with mounting the speaker and solder its contacts to the board.
[picture]

Connect the pHAT to the Pi and pat yourself on the shoulder. You're done!
[picture]

Coming up next, software.

## System setup
Like any other Raspberry Pi projects, the first thing we need to do is writing the Operating System image onto the SD card. So head over to [raspberrypi.org](https://www.raspberrypi.org/downloads/raspbian) and get the latest Raspbian OS (Jessie, at the time of this writing). The _lite_ version is all we need, in fact a smaller system means more computational resources available to run the software synthesizer smoothly. You'll also find the latest installation guide for your Operating System on the [Raspberry Pi Foundation website](https://www.raspberrypi.org/documentation/installation).
If you wanted to connect to the Pi over the network, follow [these instructions](https://www.raspberrypi.org/documentation/remote-access/ssh) to enable the ssh daemon before ejecting the micro SD card.

### First login
Get that freshly baked SD card into the Pi, connect whatever's needed to get control over the running OS (keyboard and monitor or the network adapter) and finally plug in the power supply. The tiny green LED should light up and you'll see the linux system booting up.
Once you're at the login prompt, enter the default user name - `pi` - and password - `raspberry`. As you access for the first time, you're likely going to get a warning message suggesting you to change the default password for the `pi` user. You know, security.
It isn't a bad idea to do so, especially if your Zero is connected to the network. While we're at it, we can also take care of a few more configuration details.
At the command prompt, run `sudo raspi-config`.
[picture]

Raspi-config is a utility that ships with Rasbian OS which makes tweaking system settings super easy. Go ahead and change that password.
You'll want to adjust a few more things before hitting the `Finish` button.

`Localisation Options` sets the default _locale_. It isn't actually required to change anything here, but I noticed that installing new packages will show you a few warning messages if there isn't a default locale set. Take a minute to set it up, it'll pay back with less verbose installation output.

Since we're going to run our Pi in _headless mode_, meaning without the need to interface directly with the OS, we aren't going to do much with graphical resources, so let's reallocate some of the GPU memory to be of general purpose. From the main raspi-config menu, go to `Advanced Options`, then `Memory Split` and dial the GPU memory down to `16`mb.
[picture]

Hit `Finish` when done, you'll be asked to reboot.

Back at the login prompt, login again as the `pi` user, this time using the new password you just set.

### Installing the Speaker pHAT drivers
Pimoroni provides both drivers and installation instructions for the Speaker pHAT on [their website](https://github.com/pimoroni/speaker-phat).
The setup script will take a few minutes to run and it will ask you to install a few additional packages along the way, like a python library to program the VuMeter LED bar on top of the board and the _python3_ runtime. Some of these things are unnecessary for what we're doing, but it's generally a good idea to install all the options. You'll have a fully consitent environment and you'll be left with additional development libraries that you can always use to play around with the hardware.

Wait for the setup process to complete and then check the settings of your sound card by running `alsamixer`. Turn up the volume a notch or two.
[picture]

### Installing fluidsynth
Time to install the most important piece, the software synth. I chose to use `fluidsynth` for this project because it's open source, very simple to set up and compatible with the `SoundFont2` specifications. This last detail will allow us to benefit from an infinite collection of sound banks from around the internet, or even create our own bank from recorded samples.

This part is easy, good old _apt-get_:
`sudo apt-get update && sudo apt-get install fluidsynth`
[picture]

Another lengthy installation process, mostly due to the numerous dependencies.

Fluidsynth ships with a default _soundfont_ bank called `FluidR3_GM`, where _GM_ stands for the [General MIDI](https://en.wikipedia.org/wiki/General_MIDI) standard, and that gives you quite a few instruments to play with.
When you get tired of them, install the `fluid-soundfont-gs` package or browse one of the many online repositories.

Let's get to the sound. Launch fluidsynth.
`fluidsynth -a alsa -g 1 /usr/share/sounds/sf2/FluidR3_GM.sf2`
The command is simple: start the fluidsynth process, using the _ALSA_ audio driver, with a gain value of `1` (0 is silent, 10 is freaking loud) and loading the _GM_ sound bank. In addition to starting the synthesizer process, this command will also prompt you with the fluidsynth _shell_, which you can use to play the synthesizer _programmatically_.
Feed it the following line, then press enter:
`noteon 0 69 127`
[picture]

Sweet! You see what happened? We told fluidsynth to process the `noteon` signal, which is what MIDI controllers produce when you push the tone keys. The rest of the line are its parameters.
`0` is the MIDI channel. You have 15 of them, all allocated to the Grand Piano instrument for now. Type `inst 1` to see a list of all the instruments available in the sound bank, then `prog` to assign them to a channel. For instance, to get some synth drums on channel 1, type `prog 1 118`.
`69` is the MIDI tone, a number used to encode specific sound frequencies - namely, the musical notes - into a value that can be exchanged over the wire. `69` maps to the note`A4` on your keyboard, `70` to `Bb`, `68` to `G#`, and so on, each number moving over the scale by a semitone.
Finally `127` is the MIDI _velocity_, a parameter used to encode the energy applied on a key. Try the same command again with a velocity of `50`. A softer sound, of course.

Go on and play around with the commands. Once you're ready, press `Ctrl+C` or type `quit` to go back to the sytem prompt and move on to the next step.

### Hooking up the midi controller
Go get your USB MIDI keyboard, drum set or whatever you want and keep it handy, we're going to need it real soon.
Type `aconnect -i` in the command prompt, you'll get back the list of audio devices visible to the system. You're not going to see anything interesting just yet, but if you plug in your USB controller and type the command again, your controller should show up in the list, looking something like this:
```
client 24: 'nanoKEY2' [type=kernel]
    0 'nanoKEY2 MIDI 1 '
```
Now let's start fluidsynth again, this time passing a couple of new parameters:
`fluidsynth -is -a alsa -g 1 /usr/share/sounds/sf2/FluidR3_GM.sf2 &`
The `i` and `s` arguments tell fluidsynth to start in _no shell_ and _server_ mode, so we won't get a command prompt this time. In addition, we added `&` at the end of the command to send the process in the background.

Run `aconnect -i` again, fluidsynth should show up, too, now.
[picture]

Last thing left to do is telling _ALSA_ to route any signals coming from the MIDI controller over to fluidsynth, basically the software equivalent of running a cable from your instrument to an amplifier.
Take a look at the _client numbers_ of both your MIDI controller and fluidsynth.
In my case, I have my nanoKEY2 controller on client `24`, id `0` and fluidsynth is at client `128`, id `0`. To connect them together, I ran `aconnect 24:0 128:0`. You'll need to use the values you see on your setup.

Alright! Time to play some tunes.
[picture]

### Booting up as a synth
There's one last step left: getting rid of all these cables and adapters and configure the Pi to load fluidsynth every time we plug in the power.
Linux systems have many different ways to achieve that, but there's a super easy solution for Raspbian.
`sudo nano /etc/rc.local`
[picture]

`rc.local` is a script executed by the system at the end of every boot cycle, so it can be used for instructions we want to run only once at startup.

You'll want to add these lines to the script, just above the `exit 0` line at the bottom.
```bash
nice -19 fluidsynth -is -a alsa -f /home/pi/fluidsynth.conf /usr/share/sounds/sf2/FluidR3_GM.sf2 &

sleep 10

aconnect 24:0 128:0
```

The first line should be familiar, we're launching fluidsynth, but I'm sure you've noticed some differences.
`nice` is a unix command that alters the priority of execution of a process. You can find more details on its `man` page, but fundamentally it will allow fluidsynth to use more CPU time that it would as a regular process.
We're also providing fluidsynth with its own startup script, a series of commands that the process will run as soon as the synth is ready. It's a very handy trick to pre-load our channels with the sounds we like best.
To create the configuration file, type `nano /home/pi/fluidsynth.conf` and use this example to create the setup you prefer:
```
# channel - soundfont - bank - instrument
select 0 1 0 81   # sawtooth synth
select 1 1 8 24   # ukulele
select 2 1 0 2    # electric piano
gain 1
```

The last two line in `rc.local` should be clear. Wait 10 seconds for the synth to start, then connect it the MIDI controller. Remember to use your own MIDI client id's.

`sudo reboot` and have fun!

## Where to go next
If you enjoy music hacking as much as I do, there are so many ways to keep developing your project.

Improve the system performances and get a more responsive MIDI device. Sound synthesis is a very CPU-intensive process and we just scratched the surface in terms of performances. Try disabling as many unnecessary system services as possible to gain some performance boost and learn how to run real time processes in linux.

Replace fluidsynth with `amsynth` and unleash your inner sound nerd playing with its emulated oscillators and filters.

Try running your Pi Zero from a USB power bank or add a LiPo battery to make your project _really_ portable.

Take a look at Sam Aaron's excellent [SonicPi](http://sonic-pi.net) and [Overtone](http://overtone.github.io), two fantastic music hacking environments for live coding performances.