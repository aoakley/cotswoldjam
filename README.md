# cotswoldjam
Command-line utilities for the Raspberry Pi, of particular interest to Raspberry Jam organisers &amp; educators.

Public domain. You can copy, change or redistribute these files as you wish without having to ask nor credit us/me. If you want to say thanks, please [make a donation to Cotswold Raspberry Jam](http://paypal.me/cotswoldjam) - thank-you.

![Cotswold Jam logo](http://cotswoldjam.org/assets/images/cotswoldjam-logo-128x80.png)
http://www.cotswoldjam.org

## Disclaimer

1. This is my first attempt at a Github repository. I may mess things up.
2. I have only tested these scripts on my own machines. I may mess YOUR COMPUTER up. The SD card shrinking and duplication utilities, in particular, MIGHT COMPLETELY TRASH YOUR MACHINE by overwriting, for example, your root or boot partitions. I **STRONGLY** recommend testing these on a virtual machine until this repo is in a fully mature state (current state: medium mature; these utilities have been used by half a dozen or more people for several months, and nobody's lost any data yet). You can use USB port pass-through to test this on VMs; for example the Extension Pack on Virtualbox will enable USB2 & USB3 ports at full speed (by default Virtualbox supports only USB1 speeds). Alternatively, test on a real machine for which you have tried and tested backups.

# Command line --help

All of my utilities should display a brief, but hopefully comprehensive, usage guide when you pass the parameter --help.

Furthermore there are loads of comments in the code which should enable you to debug or customise the scripts to your heart's content. At the moment these are all Bash scripts, but I might add some Perl or somesuch at a later date.

## raspbian-shrink

This utility is designed to be run on a Debian/Ubuntu PC (not a Raspberry Pi) as part of preparing an SD card image for duplication or archiving.

This takes a Raspbian image file, and shrinks it to a minimum size. Suppose you have an 8GB SD card image with only 4.4GB used. This utility will shrink that .img file down to just over 4.4GB. Not only that, but the tiny (configurable) amount of free space is zero-filled, thus making it compress better (for example, with zip or tar xvzf).

raspbian-shrink will work with any image that contains two partitions, of which the second is a Linux ext2/3/4 partition. So for example it should work with Ubuntu Mate images. It **will not work with NOOBS** because that creates, oooh, at least 5 partitions, and potentially a lot more if you dual-boot other OSes.

This utility is an extension of the work explained in my article [Shrinking Raspberry Pi SD Card Images](http://aoakley.com/articles/2015-10-09-resizing-sd-images.php).

Note that raspbian-shrink will be pretty slow if you're using a mechanical hard drive; I recommend using an SSD.

As of 23 July 2017, raspbian-shrink now also supports increasing the available free space (i.e. the opposite of shrinking) using the `-f -m 1024` parameters (force, 1024 megabytes free space). I find this useful for when I need, say, 1GB of free space (for example to perform some updates or work with a tutorial project) but I don't want to expand the filesystem to the full, say, 16GB of the card I'm using, to save time re-imaging the card. Using `dcfldd bs=1M statusinterval=16 count=3072 if=/dev/sde of=output.img` I can image just the first 3GB of the card only. Note that **free space calculation is VERY approximate**; not much better than 10% accurate. This is because my script relies on `df` which isn't very accurate either.

## raspbian-mount

A quick utility for mounting Raspbian images on a Debian/Ubuntu PC or Raspberry Pi. Suppose you have a Raspbian image file whatever.img and you need to quickly grab a file from within it. This utility mounts the second partition (the /root/ partiton) on /mnt/loop2 for you. Note that whatever changes you make to the files *will* be retained inside the .img file. Use --help to see full usage (inc. how to mount 1st /boot/ partition and how to specify mount locations) and use --unmount to unmount the partitons.

Note that this uses /dev/loop1 and /dev/loop2 loopback devices. If they're already in use, you'll need to pass -f to force them to be unmounted and unlooped. It would be nice if that were made less belligerent.

## id2hostname

This utility is designed to be run on a Raspberry Pi under Raspbian, but it could run under most Linux environments.

A utility for setting a (hopefully unique) hostname based on some (hopefully unique) serial number, which is typically the micro SD card ID (CID) but might be MAC address or some other hardware serial identifier. You can also have a specific list of specific host names (e.g. pupil names, team names) and hold that list in /etc/ or on a website.

For example, you want to teach a tutorial on networking and make use of Zeroconf/Avahi .local/.lan domain names, so you want each Raspberry Pi in your class to have a different hostname. You create an image that runs id2hostname on first boot and hey presto, rpi-a13d.local can now ping rpi-5e7f.local .

Another example, you have a pupil come to you who wants to take his program home with him. Because you can individually address his Raspberry Pi, you can just scp to his particular machine with Zeroconf, grab the file and email it to his parents without having to leave your desk.

Find out [more about SD card identifiers](http://www.cameramemoryspeed.com/sd-memory-card-faq/reading-sd-card-cid-serial-psn-internal-numbers/).

## massdd

This program is designed to be run on a Debian/Ubuntu PC (not a Raspberry Pi) that is being used to duplicate Raspberry Pi memory cards in bulk.

For mass duplication of removable drives, typically micro SD cards. Firstly this provides some safety by not allowing you to accidentally overwrite your root / boot / home partition! (Oh, come on, we've all done that.) Next it allows you to specify a range of drives, so that rather than writing out if=/dev/sde if=/dev/sdf if=/dev/sdg if=/dev/sdg etc. etc. etc... you can instead just specify a range e..h .

I could write a huge essay on the best ways to duplicate SD cards en masse, but the key thing is to use a USB3 host controller. Bear in mind that most USB2 ports on a PC will run from one single USB2 host controller chip, thereby sharing USB2's limited bandwidth. Whereas if you buy yourself a 10-port USB3 hub and connect it to a single USB3 port, then plug in ten USB2 SD card reader/writers, you will not even begin to max out the huge bandwidth of the USB3 host controller.

# A note about mass duplicating SD cards

First off, ask yourself "do I even need to do this?" **If your classroom has, or can have, a wired network, then [Andrew Mulholland's PiNet](http://pinet.org.uk) is by far the best way to have uniform Raspberry Pi environments with centralised user accounts and centralised storage.**

At Cotswold Jam, we can't use a wired network, because we run in 5 different classrooms across two floors, and our very lovely venue very reasonably forbids us from plugging into their existing wired network. So we prep a standard customised distro of Raspbian with all our workshop/tutorials set up, ready to go. We also sell these ready-made cards to attendees at a tiny markup towards donations. You'd be amazed how few Raspberry Pi users are able to update their cards, let alone download tutorial files.

When you're duplicating 50 SD cards at a time, like we do at Cotswold Jam, USB3 and shrunken image files make a huge difference. Previously we were spending 3-4 hours copying cards one at a time on three USB2 laptops. Now we spend just over an hour duplicating ten at a time on one USB3 PC.

