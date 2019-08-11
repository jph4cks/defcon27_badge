# defcon27_badge
How I hacked mine..

Why?   
-----------------------------------  
Defcon is about making friends. I was very excited about hacking a badge to get a black badge, but in the process of trying to hack it, you figure out that you are not meant to do it on your own. The idea is to learn something new and make new friends. So I made some friends in the hacking village hacking who showed me everything I needed to do it on my own. I learned how to get all the hardware and software ready to be able to connect to my badge and do some cool stuff.   

When I finished hacking my badge, I found out there was no more price than making friends, so I decided to help everyone else get their badge to 100%. I dedicated probably 90% of my Defcon time to just hack my badge and make more friends by helping them hack theirs.    

I hope these notes will help you do the same and help others so we can all get our badges to 100%.   

How?   
-----------------------------------  
So what is the goal of DEFCON27 Badge? Connecting and helping people. Especially with all the ones that have a magic token. And its really hard to connect to all those special tokens twice, so.. me and my friends found a better way. First, just hack your badge to get it to 100% or find someone who already completed all the quests "Very unlikely" and use it to simulate other badges by updating the transmissions over a serial port.   

You need to connect to your badge micro and using a debugger, modify the memory location at 0x1ffffcdc to value 7. The caveat is that this information is not stored in the non-volatile RAM, so when it reboots, you will lose your badge state. The solution is to use two badges. With the first one, you can use it to simulate other badges and get another badge to 100%. Once it's done, you now have a legit 100% badge which will still work after reset.   

So, how do we hack it in the first place?   

This is not the only way to do it but, first, you need to get your hands on some hardware.   

Hardware you might need:
-----------------------------------  
I got this board which I think it's awesome!   
Black Magic Probe V2.1  
https://1bitsquared.com/collections/embedded-hardware/products/black-magic-probe   

Very simple to connect and use, even in windows10. xD   
You can get up to speed by checking this link:  
https://github.com/blacksphere/blackmagic/wiki/Getting-Started  

JTAG Cable.
http://www.tag-connect.com/TC2050-IDC-NL-050-ALL -> JTAG Cable since the one that comes with blackmagic wont work (too big)  
https://i.redd.it/twggkji0haf31.jpg -> Here pinout to know how to connect  

Source code:
----------------------------------- 
Here is the firmware of the DEFCON27 Badge on the Defcon Media server.   
https://media.defcon.org/DEF%20CON%2027/DEF%20CON%2027%20badge.rar  

Interesting files to look at:   
1.- The actual slides with Joe Grand presentation:    
 https://media.defcon.org/DEF%20CON%2027/DEF%20CON%2027%20badge/DEFCON-27-Joe-Grand-Badge.pdf  
 http://www.grandideastudio.com/defcon-27-badge/  

2.- The actual source code used to program the badges:  
https://media.defcon.org/DEF%20CON%2027/DEF%20CON%2027%20badge/Firmware/source/dc27_badge.c  

3.- The memory map file  
https://media.defcon.org/DEF%20CON%2027/DEF%20CON%2027%20badge/Firmware/Debug/dc27_badge.map  

You can easily identify that there is a variable called Badge State, and if you look into the memory map the address is 0x1ffffcdc.  

Hacking the micro:
----------------------------------- 
to do this, first get your blackmagic hardware, then install this software.   
https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm  

Then use the installed GDB (Debugger), you should find it at. "C:\Program Files (x86)\GNU Tools ARM Embedded\8 2019-q3-update\bin\" or something similar.   

Then connect your badge using JTAG Cable. 

Then from the command line execute:  
arm-none-eabi-gdb.exe  
then type:   
"target  extended-remote COM6" where COM6 is the Serial interface detected once you connect your blackmagic.  
Remote debugging using COM6  
then type: "monitor swdp_scan" and you will see something like:  

Target voltage: 1.8V  
Available Targets:  
No. Att Driver  
 1      ARM Cortex-M  
 2      Kinetis Recovery (MDM-AP)  

Then just type: "attach 1" where 1 is the ARM Cortex-M device.   

Once connected using JTAG, you will see:  

Attaching to Remote target  
(gdb)  

Here you just need to modify that memory space we discovered before by typing:  
"set {char}0x1ffffcdc = 7"  
and then "c" to let the proccesor continue working.   

After that you can disconnect your JTAG cable and the Badge is already 100%   

Now if you connect over the Serial console (UART) you will be able to use 3 new functions: 
A (ASCII Generator)
S (Sound Generator)
U (Update Transmited Packets)

Simulated other badges:
----------------------------------- 
To simulate other badges you just have to cicle throught the following commands that will update the badges types.   

for Human type "U FFFFFFFF0001ff00" then enter then Y to say yes, then enter.   
for Goon type "U FFFFFFFF0101ff00" then enter then Y to say yes, then enter.   
for Speaker type "U FFFFFFFF0201ff00" then enter then Y to say yes, then enter.   
for Vendor type "U FFFFFFFF0301ff00" then enter then Y to say yes, then enter.  
for Press type "U FFFFFFFF0401ff00" then enter then Y to say yes, then enter.  
for Village type "U FFFFFFFF0501ff00" then enter then Y to say yes, then enter.  
for Contest type "U FFFFFFFF0601ff00" then enter then Y to say yes, then enter.  
for Artist type "U FFFFFFFF0701ff00" then enter then Y to say yes, then enter.  
for CFP type "U FFFFFFFF0800ff00" then enter then Y to say yes, then enter.  
for Uber type "U FFFFFFFF0901ff00" then enter then Y to say yes, then enter.  

So if you can do this several times and other badges are near then they will get updated to 100%.   

What I did to make this faster was another VBS script loaded into SecureCRT and then send it over the serial console.   
The timing that worked for me was 500ms between each command.   
