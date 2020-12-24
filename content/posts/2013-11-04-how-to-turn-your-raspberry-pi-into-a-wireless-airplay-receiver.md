---
title:  "How to Turn Your Raspberry Pi Into a Wireless AirPlay Reciever"
date:   2013-11-04
---

A while ago I had some brilliant idea to do something, so I bought two [Raspberry Pi's](http://www.raspberrypi.org/). Because I never follow up on things, they were still sitting in their packaging nearly a year later. Then someone brought one into [ArtBarn Labs](http://artbarnlabs.com/), where I work, and we started using it as an AirPlay receiver. It was pretty cool, so I demoed it at [DemoCampGuelph](http://www.democampguelph.com/). But it took a while to get working, so I figured I'd document the process.

At DemoCamp, I only showed it off using ethernet, although I told people you could do it wirelessly. Out of the box, the Raspberry Pi only has ethernet, so I had to figure that out. This is a guide to how I did the whole process, step by step, since I had to compile my instructions from a few different locations. Let's get started!

## Things you'll need

- A Raspberry Pi
- A WiFi adapter. This guide will assume you are using the [TP-LINK TL-WN725N](http://www.amazon.ca/TP-LINK-TL-WN725N-Wireless-Adapter-Miniature/dp/B008IFXQFU/) since that's what I used.
- An SD card with Raspian on it. Don't worry, I'll tell you how to do that too.
- A USB keyboard and mouse in order to configure the Raspberry Pi.
- A micro USB cable and some power source to plug it into.
- A monitor to connect the Raspberry Pi to.
- An HDMI cable to connect to the monitor. Yes, I'm really spelling out the detail this much.

I'm also going to assume you're using a Mac, but that is really only important for the first step, and I'm sure you can find alternative instructions elsewhere. I may even tell you where.

## Step 1: Get Raspian

You'll need to download it, unzip it, and write it to an SD card.

1. <span>[Get Raspian](http://www.raspberrypi.org/downloads). I used this specific version: `2013-09-25-wheezy-raspbian.zip`</span>
2. <span>Get it on an SD card. There are instructions for every operating system [here](http://elinux.org/RPi_Easy_SD_Card_Setup). Since I'm giving specific instructions for my setup, I used [RPi-sd card builder](http://alltheware.wordpress.com/2012/12/11/easiest-way-sd-card-setup/). Follow the instructions on that page and it should be good to go. FYI I'm running Mavericks and this program worked fine.</span>
3. <span>Put the SD card in the Pi!</span>

## Step 2: Setup Some Stuff on The Pi

Plug in your mouse and keyboard to the Pi. Also make sure you plugin an ethernet cable, and HDMI, blah blah blah I think you get it. Obviously plug in the micro USB as well so you get some power. When you turn it on you should see something like the following:

![Picture of raspi-config](/images/posts/raspi-config.png)

Do the following:

1. <span>Expand root partition to fill SD card. Unless you did weird things like partition your SD card and you don't want to use the full space. But seriously... why would you do that?</span>
2. <span>Setup SSH. Select the ssh option, hit enter, and follow the prompts.</span>
3. <span>Select start desktop on boot for boot behaviour.</span>
4. <span>Hit &lt;Finish&gt;</span>

Ok cool. So now we've got some initial setup. It probably asked you if you wanted to reboot or it just did it for you, I don't remember exactly. If you ran into errors, well you're probably screwed. Or you know, you could google it.

## Step 3: Update ALL THE THINGS!

Ok now we're going to upgrade everything because that's what the cool kids are doing. Of course, doing this means that this guide might not be of much help at some point in the future. But what the hell. Maybe I'll even update this thing at some point (not likely).

So do this:

{{<highlight bash>}}
sudo apt-get update
sudo apt-get upgrade
{{</highlight>}}

That should take a while. When it's done, do this:

{{<highlight bash>}}
sudo rpi-update
{{</highlight>}}

That should also take a while.

...

All done? Good, now let's install a bunch of stuff. I don't know what it does, but I copied and pasted it off the internet, so how bad can it be? By the way, I got this stuff from a Lifehacker guide: [Turn a Raspberry Pi Into an AirPlay Receiver for Streaming Music in Your Living Room](http://lifehacker.com/5978594/turn-a-raspberry-pi-into-an-airplay-receiver-for-streaming-music-in-your-living-room). Title sounds an awful lot like mine doesn't it? I'm 90% just ripping off their article. 

{{<highlight bash>}}
sudo apt-get install git libao-dev libssl-dev libcrypt-openssl-rsa-perl libio-socket-inet6-perl libwww-perl avahi-utils libmodule-build-perl
{{</highlight>}}

Ok, so now we've installed a bunch of stuff, hopefully we didn't mess shit up.

## Step 4: Get the WiFi thing all the kids are talking about.

Now, you've got that USB WiFi thing right? Good. Leave it wherever it is. Unless it's plugged into the Raspberry Pi, then you need to remove that. We're gonna do some stuff so your Raspberry Pi can actually know that there is a USB adapter plugged in because it didn't recognize mine at first.

This next bit I found on a Raspberry Pi forum post: [Wifi setup with the TP Link WN725N](http://www.raspberrypi.org/phpBB3/viewtopic.php?t=57426).

Type this in your terminal so we know what version of Raspian we're using:

{{<highlight bash>}}
uname -a
{{</highlight>}}

Mine says this:
{{<highlight bash>}}
Linux raspberrypi 3.6.11+ #557 PREEMPT Wed Oct 2 18:49:09 BST 2013 armv6l GNU/Linux
{{</highlight>}}

OK, now do this:
{{<highlight bash>}}
wget https://dl.dropboxusercontent.com/u/80256631/8188eu-20130830.tar.gz
tar -zxvf 8188eu-20130830.tar.gz
sudo install -p -m 644 8188eu.ko /lib/modules/3.6.11+/kernel/drivers/net/wireless
sudo insmod /lib/modules/3.6.11+/kernel/drivers/net/wireless/8188eu.ko
sudo depmod -a
{{</highlight>}}

If `uname -a` returned something different than mine did, you'll want to visit that forum link and see which driver to install.

So now if you've rebooted you should be able to launch "WIFI Config" from your desktop and you should see `wlan0` in your adapter dropdown. From here you should be able to find your desired network and connect. You can find more detailed instructions [here](http://thepihut.com/pages/how-to-setup-wifi-on-your-raspberry-pi-raspbian).

If everything went well you should be able to disconnect your Pi from ethernet and connect over WiFi only! If it didn't work... hopefully you know how to use Google well.

## Step 5: Shairport!

So now we can finally get to that awesome AirPlay receiver program. This is from that [Lifehacker article](http://lifehacker.com/5978594/turn-a-raspberry-pi-into-an-airplay-receiver-for-streaming-music-in-your-living-room) again.

Let's do this:

{{<highlight bash>}}
git clone https://github.com/hendrikw82/shairport.git
cd shairport
make
{{</highlight>}}

We still have more to do, but we should be able to see if this thing at least works now. Do the following:

{{<highlight bash>}}
./shairport.pl -a AirPi
{{</highlight>}}

You can replace AirPi with anything you want. For example I have KitchenPi and LivingPi.

It didn't work right? If it did, you can ignore this next part. If you got an error along the lines of `Can't locate Net/SDP.pm` then we need to run the following:

{{<highlight bash>}}
cpan install Net::SDP
{{</highlight>}}

More details about that last thing can be found on StackExchange: [Problem installing Net::SDP](http://raspberrypi.stackexchange.com/questions/5310/problem-installing-netsdp).

OK, now you should be able to actually do that thing we tried to do:

{{<highlight bash>}}
./shairport.pl -a LiedToMeTheFirstTimePi
{{</highlight>}}

Hooray it works! You should be able to see LiedToMeTheFirstTimePi as an AirPlay source from your Mac or iOS device.

You could keep running that command everytime you booted your Pi in order to make it work, but you probably want to have it happen automatically right?

Once again, we'll copy stuff from the Lifehacker article:

{{<highlight bash>}}
cd shairport
make install
cp shairport.init.sample /etc/init.d/shairport
cd /etc/init.d
chmod a+x shairport
update-rc.d shairport defaults
{{</highlight>}}

Then we need to add it as a launch item.

{{<highlight bash>}}
sudo nano shairport
{{</highlight>}}

From Lifehacker: "This loads up Shairport file we need to edit. Look through the file for the "DAEMON_ARGS" line, and change it so it looks like this":

{{<highlight bash>}}
DAEMON_ARGS="-w $PIDFILE -a AirPi"
{{</highlight>}}

Again, change AirPi to whatever you want it to be called when you view a list of AirPlay receivers.

Save the file (ctrl+x) and you're good!

## Conclusion

Hopefully that all went fine for you, but in case it didn't you should be able to find your solution elsewhere on the internet. If I could be a little bit of help to you then I've done my job.

Here's a list of resources that I used:

1. <span>[Lifehacker: Turn a Raspberry Pi Into an AirPlay Receiver for Streaming Music in Your Living Room](http://lifehacker.com/5978594/turn-a-raspberry-pi-into-an-airplay-receiver-for-streaming-music-in-your-living-room)</span>
2. <span>[Wifi setup with the TP Link WN725N](http://www.raspberrypi.org/phpBB3/viewtopic.php?t=57426)
3. <span>[Problem installing Net::SDP](http://raspberrypi.stackexchange.com/questions/5310/problem-installing-netsdp)</span>
4. <span>[Google](http://google.com)</span>











