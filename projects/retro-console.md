Raspberry Pi Zero Gaming Console
================================

Description
-------------------
Using [RetroPie][retro-pie-site], an Operating System dedicated to retro-gaming on the Raspberry Pi, you can assemble a console to-go for on-the-go gaming.

Time estimate
-------------
1 hour

Difficulty
----------
Easy

Ingredients
-----------
#### Physical things
- 1x Raspberry Pi Zero
- 1x HDMI cable
- 1x HDMI display (most modern televisions have an HDMI port)
- 1x mini HDMI to HDMI adapter
- 1x micro USB AC power plug
- 1x micro USB cable
- 1x micro USB to USB adapter
- 1x USB controller (I use a XBox 360 Controller with the wireless PC USB adapter)
- 1x keyboard (optional, but recommended for setup)
- 1x USB hub (optional)
- 1x USB wireless adapter (optional)

#### Virtual things
- [RetroPie for Raspberry Pi Zero][retro-pie-install]
- [Win32DiskImager][win32-disk-imager-download] (Windows only)
- [ApplePi Baker][applepi-baker-download] (Mac only)

#### Knowledge things
- How to download and manipulate files on your computer
- How to extract .gz files on your computer
- Configuration of your WiFi access point (optional)

Tutorial (for Windows)
----------------------
**Note:** if you get stuck, [RetroPie][retro-pie-site] also has a guide [here][retro-pie-guide]

#### Step 1 - Download and Prepare RetroPie
(Raspberry Pi has an image installation guide covering more options, [here][generic-image-install-guide])

- Click the download button for "Raspberry Pi 0/1"
- Extract the file you just downloaded (ends with ".gz"); you should end up with an image file (ends with ".img"). Save the image file somewhere you can get to it in the next step.
- Download and install [Win32DiskImager][win32-disk-imager-download] (Windows only) or [ApplePi Baker][applepi-baker-download] (Mac only)

#### Step 2 - Install RetroPie
- Insert your microSD card (via SD adapter if needed) into your computer
- **(Windows)**

> TODO: Finish writing

- **(Mac)** Open ApplePi Baker and look for the "Pi-Crust" section. Make sure your SD card is selected. **IMPORTANT:** be careful not to select any other drive - all the data on the selected drive will be overwritten and lost!
- **(Mac)** Now look for the "Pi-Ingredients" section. Load the image file you downloaded, and click the "Restore Backup" Button

#### Step 3 - Plugging in your cables and power on RetroPie
- Insert your microSD card into your Pi Zero:

> TODO: Photo needed

- Plug your micro USB to USB adapter into the micro USB port on the Pi Zero labeled "USB":

> TODO: Photo needed

- (optional) If you have a USB hub that you'd like to use, plug it into the USB adapter now. *Note:* If you have a USB wireless adapter and would like to use it to give your console Internet capabilities, you'll need a USB hub, so that you can plug in the wireless adapter at the same time as your controller and/or keyboard (plug the USB wireless adapter into the USB hub)

> TODO: Photo needed

- (optional) If you have a keyboard you'd like to use, plug it into the USB Hub or adapter now. Otherwise, plug your USB controller into the USB hub or adapter
- Plug in your mini HDMI to HDMI adapter into the port on the Pi Zero labeled "HDMI":

> TODO: Photo needed

- Connect your Pi to the HDMI port of your HDMI display with an HDMI cable
- Alright, this next step will actually power on and boot the Raspberry Pi (as soon as you plug in), so double check that everything so far is plugged in and your display is set to the correct HDMI input.
- Plug in the micro USB AC power plug into the micro USB port on the Pi Zero labeled "PWR IN":

> TODO: Photo needed

- After a few seconds, you should see the RetroPie load screen:

> TODO: Photo needed


#### Step 4 - Configuring RetroPie
> TODO: Finish writing



[applepi-baker-download]: https://www.tweaking4all.com/?wpfb_dl=94
[generic-image-install-guide]: https://www.raspberrypi.org/documentation/installation/installing-images/
[retro-pie-download]: https://retropie.org.uk/download/
[retro-pie-guide]: https://retropie.org.uk/docs/First-Installation/
[retro-pie-install]: https://github.com/RetroPie/RetroPie-Setup/releases/download/4.1/retropie-4.1-rpi1_zero.img.gz
[retro-pie-site]: https://retropie.org.uk/
[win32-disk-imager-download]: https://sourceforge.net/projects/win32diskimager/files/latest/download
