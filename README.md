INTRODUCTION
'dtvcmd' is a program to send commands via your computer's serial port to a DirecTV(tm) satellite television receiver. With it, you can mimic the functionality of an infrared remote control. Not all DTV receivers support serial communications.

'dtvremote' is a pyGTK GUI for controlling the receiver from the desktop.

PLEASE NOTE: This project has absolutely NOTHING to do with signal theft or circumvention. Do not make inquiries about “hacking” your system. I don't know how nor would I help you if I did.

Direct TV is a trademark of Direct TV, Cartoon Network is a trademark of Cartoon Network, other trademarks are property of their respective owners, water is wet.

CONVENTIONS
I'll say 'Linux' in this document often as that is what I use, but the python language is platform independent it's very likely this script will work on other operating systems.

We're going to assume you know your way around you computer. Knowledge of the python programming language is not required. It's a great language though!

REQUIREMENTS
Direct TV(tm) satellite TV receiver with functional serial interface (not all have them and not all that do work) A PC with an available serial port A custom serial cable with proper pinout A not horribly old release of Python The PySerial? package from pyserial.sourceforge.net installed Python >= 2.3 (www.python.org) pyGTK >= 2.0 (www.pygtk.org)

CUSTOM CABLE
(Need info here)

QUICK INSTALLATION
$ cd ~/bin $ wget http://pydtvcmd.googlecode.com/svn/trunk/dtvcmd $ wget http://pydtvcmd.googlecode.com/svn/trunk/dtvremote $ chmod +x dtvcmd dtvremote

You will probably want to edit 'dtvremote' to set up the correct serial port and preset channel buttons.

You will probably have to add yourself to the 'uucp' group for non-root access to the serial port or CHMOD the port and make it read/write-able for others. You may also have to jump through hoops to make SELinux happy.

COMMAND LINE REFERENCE (for dtvcmd)
-d {device} Always required. Specifies which system device to use for serial communications – i.e. the port the serial cable is connected to. For Linux systems, serial port A is typically /dev/ttyS0 and serial port B is /dev/ttyS1. For Windows, I think using '0' will select port A and '1' will select port B or 'COM1' and 'COM2' may work.

-c {channel}

Set the channel to the value N. This example switches the receiver to Cartoon Network:

dtvcmd -d /dev/ttyS0 -c 296

-m [1|2]

Sets the command mode. Default is '1', old command set. Use '-m 2' to communicate with the receiver using the 'new' command set. As explained by Andy Ellsworth:

“Newer RCA receivers incorporate changes to the serial control protocol, which renders them incompatible with previous versions of the serial control standard. (I originally believed that this was some evil ploy by RCA, but it appears to be a necessary change in order to comply with the APG (Advanced Program Guide) standard from DirecTV--mea culpa.)”

http://www.dar.net/~andy/tivo/rca_dss_serial.html

RCA models DRD4xxRG & RH (among others) seem to use the new command set. If your unit was purchased within the last four years, it probably uses the new commands. Please note: As of this writing, I have NOT tested communications using the '-m 2' option. My receiver uses the old command set so there was much guessing and crossing of fingers.

This example tells the receiver, using the new command set, to switch to Cartoon Network(tm):

dtvcmd -d /dev/ttyS0 -m 2 -c 296

-T Return the receiver's datetime in the format 'MMDDhhmmCCYY.ss'. You can sync your PC's clock with your receiver with the following command (as root):

date dtvcmd.py -d /dev/ttyS0 -T
-p [on|off]

Turns the receiver's power on or off. Unlike the power button on a remote, this is NOT a toggle so sending two power ON request wont turn the receiver off again. If you are capturing TV shows via a cron script, adding '-p on' is probably a good idea. The following example turns the receiver on and switches the channel to Cartoon Network:

dtvcmd -d /dev/ttyS0 -p on -c 296

-i [enable|disable]

Enable or disable whether the receiver should process input from from a real infrared remote control. NOTE: Check if this resets after a power off?

Example:

dtvcmd -d /dev/ttyS0 -i disable

-b {arg}

Mimic pressing the corresponding button. If you want to do multiple button presses, prefix each with a '-b'. Valid buttons are:

0, 1, 2, 3, 4, 5, 6, 7, 8, 9, alt_audio, info, dss, menu_select, guide, up, down, left, right, channel_up, channel_down, & prev_channel

The following example advances the channel twice (and like a remote, it will use your selected favorite list for the channel order):

dtvcmd -d /dev/ttyS0 -b channel_up -b channel_up

-v

Verbose mode. Under normal operation, DTVcmd.py operates in ninja-like silence, only responding to get channel, get signal strength, or get datetime commands. To break the omerta, use -v and it will be all kinds of chatty. Each line begins with “[DTVcmd.py] ...” so you can tell who is complaining in your scripts. Example:

$ dtvcmd -d /dev/ttyS0 -v -m 2 -i disable

Reponds with:

dtvcmd? verbose on dtvcmd? Communicating on device /dev/ttyS0 dtvcmd? Using new command set dtvcmd? IR remote disabled

-t 'message'

Shows text on the screen. On my DRD303A, it ignores any message longer than 15 characters and the message to stay for 5 mins or until I press a button on the remote that changes the display in some fashion. See the '-h' option for removing the text.

Here's a short script that displays a message for X seconds. Let's say you had a program to return caller ID data, then you could use something like this to display who's calling on the TV.

#!/usr/bin/env bash # usage: dtv_showtext.sh “{message}” {duration in seconds} MSG=$1 DELAY=$2 dtvcmd -d /dev/ttyS0 -t “$MSG” sleep $DELAY dtvcmd -d /dev/ttyS0 -h

$ dtv_showtext.sh “555-1212” 5

CREDITS
Big thanks to Kevin Timmerman at www.dtvcontrol.com for the code values.
