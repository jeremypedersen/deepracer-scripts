# DeepRacer Scripts

Please note this scripts are provided as is.... if something breaks through you using it then a) I'm sorry b) fix it and submit a PR ;-)

## dev-build.sh

Runs on the car from `/home/deepracer` and the intention is that can be used to test pull requests / dev code

    chmod +x dev-build.sh
    bash /dev-build.sh

## usb-build.sh

(OSX only)

Requirements:
* https://unetbootin.github.io/ installed
* `factory_reset.zip` unzipped in the same directory (will be downloaded if missing)
* `ubuntu-20.04.1-20.11.13_V1-desktop-amd64.iso` in the same directory (will be downloaded if missing)

Download both from here https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-ubuntu-update-preparation.html

    sudo ./usb-build.sh -d disk2

## tweaks.sh

A script to change a couple of things on the car that I've found useful at events

* Change the hostname
* Change the car console password (**Note:** Update the default password in the script)
* Update Ubuntu
* Update the car software
* Disable IPV6 on network interfaces
* Disable the video stream on the car console by default
* Disable system suspend
* Disable network power saving
* Disable the software update check
* Enable SSH (You've probably already done this)
* Allow multiple logins to the car console
* Increase the car console cookie duration
* Disable Gnome, Bluetooth & CUPS

    sudo ./tweaks.sh -h newhostname -p magicpassword

## reset-usb.sh

Needs to be run on the car to reset USB in the event of a failure of the front USB hub, faster than a full reboot of the car - works ~70% of the time