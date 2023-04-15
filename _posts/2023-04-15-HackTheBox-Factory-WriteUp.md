---
title: HackTheBox Factory WriteUp
published: true
---

# Hack The Box Factory Write Up

Earlier today after recovering my account on HackTheBox i decided to go ahead an do some challenges hardware specific in which this one capture my eye :

`` "Our infrastructure is under attack! The HMI interface went offline and we lost control of some critical PLCs in our ICS system. Moments after the attack started we managed to identify the target but did not have time to respond. The water storage facility's high/low sensors are corrupted thus setting the PLC into a halt state. We need to regain control and empty the water tank before it overflows. Our field operative has set a remote connection directly with the serial network of the system." ``

![Hack The Box](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/Factory-img2.png)

So After firing up the machine i decided to do some intial evalution as to what are material that were provided to us as well as tried a telnet connection to the given ip which gave me this status window.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/Factory-img1.png)

A close inspection show 3 Options one is to get the status of system and last to exit out of this window session.
Later on i decided to do some more test and saw the status of the system is stuck on automode with in_valve set to 1 (enable for non technical peeps) and out_valve is set to 0 so in order to gain flag we have to some how set this auto_mode to 0 and set manual mode on now next interesting thing i saw is the modbus command.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/Factory-img4.png)

So in our given documents from HTB we see a Remote ICS Plant where it explains the working of the remote ICS how the MODBUS command is sent to the Target from the Host.
There is a central laptop that tells us how the modbus RTU network fetches host command and send it correctly to PLC-1.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/IF_setup.png)

To understand the Water Storage Facility PLC we need to understand how it is working which could be understood by looking into Ladder Logic given to us.
![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder.png)

Now over here there are 4 logic we need to look in inorder to get our flag these circuit explains how we can set out coil.
![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder1.png)
In This Auto mode can be triggered when start is true or auto_mode is true and manual_mode_control is false.
It gives us idea in order to shut of auto mode we somehow need to set manual_mode_control to true which in return set our manual mode on because in bottom it shows how normally closed contact of auto mode sets manual mode on.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder2.png)

second is this one where it explains how to enable stop in stop out which would be usefuel later on for us.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder3.png)
![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder4.png)
![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/PLC_Ladder5.png)

Looking at above ladder now we can understand how we can enable in_valve and out valve so lets get to working 
![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/Factory-img3.png)
So in this we can see how a modbus command is structured so 52 is the address of Slave the Targetand 05 is the write single coil and 00 05 is the physical address to the coil
FF 00 sets the output value On (1).
For this we don't have to calculate CRC as CRC is handled by hosting machine laptop2.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/modexec1.png)
Running the command 1 to get status we can see how our automode is set to manual mode.

![](https://raw.githubusercontent.com/piyushS3V3N/piyushS3V3N.github.io/main/assets/factory/won.png)

I had a lot of fun solving this machine although it is in easy range the amount of learning it required the learning curve was so steep i really enjoyed it.


Signing out Z3R0P1