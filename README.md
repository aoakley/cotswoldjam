# cotswoldjam
Command-line utilities for the Raspberry Pi, of particular interest to Raspberry Jam organisers &amp; educators

![Cotswold Jam logo](http://cotswoldjam.org/assets/images/cotswoldjam-logo-128x80.png)
http://www.cotswoldjam.org

## Disclaimer

1. This is my first attempt at a Github repository. I may mess things up.
2. I have only tested these scripts on my own machines. I may mess YOUR COMPUTER up. The SD card shrinking and duplication utilities, in particular, MIGHT COMPLETELY TRASH YOUR MACHINE by overwriting, for example, your root or boot partitions. I **STRONGLY** recommend testing these on a virtual machine until this repo is in a mature state (current state: not mature). You can use USB port pass-through to test this on VMs; for example the Extension Pack on Virtualbox will enable USB2 & USB3 ports at full speed (by default Virtualbox supports only USB1 speeds). Alternatively, test on a real machine for which you have tried and tested backups.

## id2hostname

A utility for setting a (hopefully unique) hostname based on some (hopefully unique) serial number, which is typically the micro SD card ID (CID) but might be MAC address or some other hardware serial identifier. For example, you want to teach a tutorial on networking and make use of Zeroconf/Avahi .local/.lan domain names, so you want each Raspberry Pi in your class to have a different hostname. You create an image that runs id2hostname on first boot and hey presto, rpi-a13d.local can now ping rpi-5e7f.local . You can also have a specific list of specific names (e.g. pupil names, team names) and host that list in /etc/ or on a website.

## massdd

For mass duplication of removable drives, typically micro SD cards. Firstly this provides some safety by not allowing you to accidentally overwrite your root / boot / home partition! (Oh, come on, we've all done that.) Next it allows you to specify a range of drives, so that rather than writing out if=/dev/sde if=/dev/sdf if=/dev/sdg if=/dev/sdg etc. etc. etc... you can instead just specify a range e..h .

I could write a huge essay on the best ways to duplicate SD cards en masse, but the key thing is to use a USB3 host controller. Bear in mind that most USB2 ports on a PC will run from one single USB2 host controller chip, thereby sharing USB2's limited bandwidth. Whereas if you buy yourself a 10-port USB3 hub and connect it to a single USB3 port, then plug in ten USB2 SD card reader/writers, you will not even begin to max out the huge bandwidth of the USB3 host controller.

## raspbian-shrink

This takes a Raspbian image file, and shrinks it to a minimum size. Suppose you have an 8GB SD card image with only 4.4GB used. This utility will shrink that .img file down to just over 4.4GB. Not only that, but the tiny (configurable) amount of free space is zero-filled, this making it compress better (for example, with zip or tar xvzf).

raspbian-shrink will work with any image that contains two partitions, of which the second is a Linux ext2/3/4 partition. So for example it should work with Ubuntu Mate images. It **will not work with NOOBS** because that creates, oooh, at least 5 partitions, and potentially a lot more if you dual-boot other OSes.

# A note about mass duplicating SD cards

When you're duplicating 50 SD cards at a time, like we do at Cotswold Jam, USB3 and shrunken image files make a huge difference. Previously we were spending 3-4 hours copying cards one at a time on three USB2 laptops. Now we spend just over an hour duplicating ten at a time on one USB3 PC.

