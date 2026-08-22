# openwrt-epson-dm-d110
A very simple init script and set of instructions by which to set up an epson DM-D110 customer pole display as a vaccuum-fluorescent display clock attached to your openwrt device.

<img width="4080" height="3072" alt="A customer pole display showing 15:07 atop a beige PC tower, connected by black cable to a nearby wireless access point." src="https://github.com/user-attachments/assets/d3d7802c-6ca0-421d-abb4-4358c3687ce0" />

I've set this up on a pair of TPLink Archer C7's and decided to share the instructions all in one place for anyone who... wants a customer pole display clock in their living room, I guess.

You'll need to add kernel modules for the FTDI USB Serial interface in the display, and `stty` to re-configure the serial port. Drop the script in, enable it, and add an entry to your crontab to periodically reset the clock (it will drift otherwise!).

## Setup Instructions
1. Install the packages `kmod-usb-serial-ftdi`, `coreutils-stty`, and all dependencies thereof
2. Copy `zzz-dmd110setup` into `/etc/init.d/`
3. Run `/etc/init.d/zzz-dmd110setup enable`
4. Run `crontab -e` and add the line `0 0 * * * /etc/init.d/zzz-dmd110setup.sh sclock`

## Disable instructions
1. Run `crontab -e` and remove the aforementioned entry
2. Run `/etc/init.d/zzz-dmd110setup disable`

## Troubleshooting Steps
Sometimes you set it all up and it doesn't work. This is probably because this script is very specific to my system's universal serial bus layout.
1. Install `usbutils` package
2. Run `lsusb` and take note of the entry `Bus [BBB] Device [DDD]: ID [VVVV]:[PPPP] SEIKO EPSON USB Edition of DM-D110`
3. edit `zzz-dmd110setup` to replace `1208` with `[VVVV]`, `0780` with `[PPPP]`, `001` with `[BBB]`, and `002` with `[DDD]`
4. Either adjust baudrate of display via DIP switch to 19200 OR adjust baudrate set by stty in the script
5. ¯\_(ツ)_/¯

## Notes
The DM-D110's time counter is not very accurate, and will drift over time -- for accuracy's sake, I've included instructions to re-start the clock at midnight for calibration. Adjust the time in the crontab entry accordingly if you're a night owl and don't want to experience interruptions!

The name is prefixed with `zzz-` because we need this to launch once NTP client has gotten time and set time zone, and for organization's sake I preferred to have it the last init script run. You do need to keep start at 99, but feel free to adjust the name if you really don't mind this launching before `bootcount` and `urandom` (per the OpenWRT wiki, the init scripts with the same start value will launch in alphabetical order).
