---
layout: post
title: "Installing Arch Linux on a Samsung Ativ Book 8 (NP880Z5E)"
description: "A few tips and tricks to help when installing Arch (or some other
distro) on this laptop"
tags: [linux, arch]
category: Linux
comments: true
imagefeature: arch-cover.png
---

I recently purchased a new Samsung Ativ book 8 from [BestBuy][1]. Here I'll
document a few tips and tricks I found when installing both Ubuntu and Arch on
my new laptop.

Despite using Arch Linux for the past few years, I wanted something quick and easy to get up and running so I went back to Ubuntu. There is already some information available on this process available [here][2] so I started going through the guide, disabled fast boot and secure boot in the BIOS, booted from USB, installed Ubuntu with default options. On reboot, I could no longer boot Windows from grub (Invalid EFI file). This was fixed by installing boot repair on Ubuntu and running it with the default settings.

When I got to this point I was already finding Ubuntu frustrating and was unhappy with the general responsiveness of the machine so I ordered an SSD on Amazon and figured I&#8217;d start again with a fresh install of Arch Linux. I ordered the [250GB Samsung SSD Evo][3] which seemed to have the best price/performance ratio. So here I will document the process of installing Arch.

### Arch Linux

Since the [Arch Linux][4] wiki is such a brilliant resource, I won&#8217;t repeat the same material here. I followed the [Beginner&#8217;s Guide][5] throughout the process and will only outline any deviations or enhancements from the guide. I will also detail the basic configuration of the system at some point, mainly for my own reference but perhaps it will be helpful to someone else.

#### Installation

The installation went fairly smoothly although I did end up doing it twice. At first I tried to install everything using legacy BIOS compatibility mode, unfortunately it seems that this particular version of UEFI doesn&#8217;t really support this very well (I was using a GPT formatted disk so this could have been the problem). Another user had the same issue on a Samsung Book 7 [here][6] and found little resolution.

After retrying the installation with an ESC partition created for UEFI and installing GRUB, everything worked fine (just remember to *modprobe efivars* before running grub-install or *exit* from chroot and do it). On boot I installed X, the requisite video driver ([xf86-video-intel][7]) and i3 window manager, things were starting to look familiar. Wireless was very simple, no firmware needed for the standard card.

#### Touchpad
The previously linked guide suggests installing a modified Elantech touchpad driver as per this post however I simply installed the standard xf86-input-synaptics package and set up the config file with some personal preferences. The config file is located in /etc/X11/xorg.conf.d/50-synaptics.conf and my version is as follows:

{% highlight INI %}

# Additional options may be added in the form of
#   Option "OptionName" "value"
#
Section "InputClass"
        Identifier "touchpad catchall"
        Driver "synaptics"
        MatchIsTouchpad "on"
                Option "TapButton1" "1"
                Option "TapButton2" "2"
                Option "TapButton3" "3"
                Option "HorizTwoFingerScroll" "on"
                Option "VertTwoFingerScroll" "on"
 
                #Enable single tap right click
                Option "RBCornerButton" "3"
 
                #Enable natural scrolling
                Option "VertScrollDelta" "-50"
                Option "HorizScrollDelta" "-200"
 
                #Disable touchpad while typing
                Option "PalmDetect" "1"
                Option "PalmMinWidth" "3"
 
 
# This option is recommend on all Linux systems using evdev, but cannot be
# enabled by default. See the following link for details:
# http://who-t.blogspot.com/2010/11/how-to-ignore-configuration-errors.html
        MatchDevicePath "/dev/input/event*"
EndSection
 
Section "InputClass"
        Identifier "touchpad ignore duplicates"
        MatchIsTouchpad "on"
        MatchOS "Linux"
        MatchDevicePath "/dev/input/mouse*"
        Option "Ignore" "on"
EndSection
 
# This option enables the bottom right corner to be a right button on
# non-synaptics clickpads.
# This option is only interpreted by clickpads.
Section "InputClass"
        Identifier "Default clickpad buttons"
        MatchDriver "synaptics"
        Option "SoftButtonAreas" "50% 0 82% 0 0 0 0 0"
#       To disable the bottom edge area so the buttons only work as buttons,
#       not for movement, set the AreaBottomEdge
#       Option "AreaBottomEdge" "82%"
EndSection

{% endhighlight %}

More information on configuring the synaptics driver can be found [here](https://wiki.archlinux.org/index.php/Touchpad_Synaptics) on the Archwiki.

More information on configuring the synaptics driver can be found [here][9] on the Archwiki.

#### Samsung Tools

There are a number of controls for the laptop which can be operated using the keyboard&#8217;s fn buttons. These include screen brightness keyboard backlight and fan speed. Unfortunately these functions require low level manipulation of the samsung firmware, so someone wrote a useful package for use on Samsung laptops, this allows things like fan control etc. Unfortunately this kernel module caused some serious problems on laptops which were booted using EFI and the kernel dev&#8217;s fix was to actually disable the functionality on any laptop booted with EFI. So if you are booting in EFI there is currently no way to control the fan but if you are booting in legacy mode you might be in luck. I haven&#8217;t actually been able to get this to work yet but I will update the article as I make progress. The samsung-tools package in AUR contains the requisite items.

There is a frustrating lack of information available on the samsung-laptop module, the bug which bricked laptops first appeared in January 2013 and it is unclear whether a fix exists yet. Both Samsung and Ubuntu and staying quiet on that front.

#### Volume Buttons

The volume buttons on the keyboard can be used to adjust the volume by finding the keycode from *xev* and using that to call a script and adjust the volume. 

Since I am using i3, this is fairly simple to do, just add the following lines to your i3 configuration

{% highlight INI %}
#Laptop  Audio Controls
bindcode 121 exec amixer sset Master toggle
bindcode 122 exec amixer sset Master 2dB-
bindcode 123 exec amixer sset Master 2dB+a
{% endhighlight %}


#### Display Backlight

To begin with I found no way to modify the screen&#8217;s brightness, I am unsure exactly what I did to allow this but I believe it was fixed when I installed the samsung-tools package. In any case, if you have that package you should see three files in /sys/class/backlight: acpi\_video0, acpi\_video1 and intel_backlight. The backlight can then be adjusted with

{% highlight bash %}
xbacklight -inc 10
xbacklight -dec 10
{% endhighlight %}

This can again be tied to the function keys by adding the following to your i3 configuration:

{% highlight bash %}
#Laptop Brightness Controls
bindcode 232 exec xbacklight -dec 10
bindcode 233 exec xbacklight -inc 10
{% endhighlight %}

#### Keyboard Backlight
After I reinstalled Arch with an MBR instead of GPT and booted in legacy BIOS mode, I could modprobe samsung-laptop so I installed the samsung-tools-git module from the AUR and the keyboard backlight started to work after a few restarts. It can be controlled using the following script:
{% highlight bash %}

#Turn off the backlight
echo 0 > /sys/devices/platform/samsung/leds/samsung::kbd_backlight/brightness
 
#Turn on the backlight full brightness
echo 4 > /sys/devices/platform/samsung/leds/samsung::kbd_backlight/brightness
{% endhighlight %}

So it should be possible to link this script to the keyboard backlight controls however it seems that this is under the control of the samsung-tools module. When I use xev it doesn’t register any keypress on pressing these buttons. I will update this if I find a solution for this.

I found [this][10] thread very helpful when troubleshooting the backlight although ultimately didn&#8217;t use anything from it. It may be possible to use this to register the keypresses properly.

#### Discrete GPU
At this point, I noticed the laptop was running incredibly hot and looking at powerstat I found it was using about 45W! The laptop uses ATI&#8217;s hybrid graphics which is poorly supported in Linux. Currently the best thing to do is to turn off the ATI card and solely use Intel&#8217;s integrated graphics. This can be done using the acpi_call-git package using the instructions on the [Arch wiki][11]. In my case, I added the following line to /etc/tmpfiles.d/acpi_call.conf:

{% highlight bash %}
w /proc/acpi/call - - - - \_SB.PCI0.PEG0.PEGP.SGOF
{% endhighlight %}

Unfortunately this causes the laptop to crash on boot so I am currently just using the turn\_off\_gpu.sh script on every boot.

That brings power consumption down to about 10-15 watts and the laptop is much cooler. This is far better but there&#8217;s probably still more to do. The CPU seems to still be running at full speed and the fan button doesn&#8217;t appear to do anything other than change the status in samsung-tools. I&#8217;d imagine this is a samsung-tools problem. The battery seems to last for about 5 hours with light use in its current state with the GPU turned off.

As I find more solutions I will add the updates here.

*UPDATE*: There appear to be a few problems with using acpi call to disable the Radeon 8770M, resume on suspend takes about 10 seconds which seems to be related. It may be possible to solve this by installing the proprietary ATI catalyst drivers however I haven&#8217;t tried this yet.

*UPDATE 2*: The GPU power management seems to have been fixed in new versions
of the kernel, I would suggest not using acpi-call.

 [1]: http://www.bestbuy.com/site/Samsung---ATIV-Book-8-15.6%26%2334%3B-Touch-Screen-Laptop---8GB-Memory---1TB-Hard-Drive---Mineral-Ash-Black/8965053.p?id=1218957864443&skuId=8965053 "Samsung Ativ Book 8 at Best Buy"
 [2]: http://forum.notebookreview.com/samsung/723676-linux-series-7-2013-ativ-book-8-a.html
 [3]: http://www.amazon.com/Samsung-Electronics-EVO-Series-2-5-Inch-MZ-7TE250BW/dp/B00E3W1726/ "Amazon Samsung 840 Evo 250GB"
 [4]: https://wiki.archlinux.org/ "Archlinux Wiki"
 [5]: https://wiki.archlinux.org/index.php/Beginners%27_Guide "Arch Linux Beginner's Guide"
 [6]: https://bbs.archlinux.org/viewtopic.php?id=168853 "Arch Forum - BIOS issue"
 [7]: https://www.archlinux.org/packages/?name=xf86-video-intel "xf86-video-intel package"
 [8]: http://ubuntuforums.org/showthread.php?t=2111236&#038;p=12692065#post12692065 "Instructions for fixing touchpad"
 [9]: https://wiki.archlinux.org/index.php/Touchpad_Synaptics "Arch Wiki on Synaptic's Touchpad"
 [10]: http://forums.linuxmint.com/viewtopic.php?f=49&#038;t=135602 "Linux Mint Forum Troubleshooting Keyboard Backlight"
 [11]: https://wiki.archlinux.org/index.php/Hybrid_graphics "Arch Wiki on hybrid Graphics"


