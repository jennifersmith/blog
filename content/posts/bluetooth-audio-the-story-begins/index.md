+++
title = 'Bluetooth Audio: The story begins'
date = 2023-12-01T14:44:54+11:00
draft = true
+++

We've recently been setting up multi-room audio at home which probably is a whole post of its own if I get round to it. Anyway, as part of this we had need to engage the services of a reliable but relatively ancient UE bluetooth speaker.


{{< figure src="ue-mobile-boombox-productpage.png" title="UE Mobile Boombox" caption="From ancient times (2013)... ours is green by the way."
alt="A screenshot of a product page for the Logitech UE Mobile Boombox speaker"
attrlink="https://web.archive.org/web/20130213023845/http://ue.logitech.com/en-au/wireless-speakers/mobile-boombox" attr="archive.org" >}}


When I say reliable, I probably more mean "It's still powering on after ten years". 


We paired the UE Mobile Boombox bluetooth speaker (aka 'btgreen') with the Raspberry Pi 4B (aka 'tvpi') used as a kind of multipurpose house server/media player/build server. When tvpi started playing audio through btgreen, the sound got very choppy - cutting out and glitching. Worse still, the poor tvpi was choking on having to write a load of journal logs out to disk.

It seemed to be that our log files were filling up with this message:

```
2023-08-26T11:06:04.998385+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.174161+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.194030+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.198000+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.202029+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.214054+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.226041+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)
2023-08-26T11:06:05.246022+1000 tvpi kernel: Bluetooth: Unexpected continuation frame (len 0)


```

These messages seemed to be fairly regularly arriving in the logs:

{{< figure src="perminute.svg" alt="Plot of message count by minute for a range of 0 to 90" title="Log messages per minute" caption=`Count of "Unexpected continuation frame (len 0)" messages per minute over an hour long sample period from journald logs` >}}


Seemed to be a spike around the start and end of 5000 and 3000 messages per minute respectively. Otherwise we seem to be around the 2000 messages a minute mark for half the feed and then get up to 2500 messages per minute.

(Note this is a historic sample - I can't fully remember if it was taken during playing a single stream, stopping, starting, multiple devices etc so it would be good to replace it with a more reliable reproduction at some point.)


A few additional observations:

+ Playing audio worked fine from the various laptops and mobile devices we checked with including a Debian-based laptop, MacBook, Android phones etc. No log messages, decent enough sound from the little speakers. 
+ Playing audio from the Pi to a more modern Bluetooth speaker worked well
+ Playing audio from the Pi to other devices seemed to not cause the log message to be generated with the same volume and consistency as btgreen though we didn't look into this super closely yet
+ The log messages only showed up when we were actually playing audio, not just while connected
+ While we discovered the problem using our full multiroom setup (at the time: Pipewire, Mopidy, Snapcast), we could recreate it just using aplay to play an audio file via Pipewire

Anyway, this is only an introductory post. I hope to go into further detail on:

+ Where the error came from and how it was 'temporarily' fixed on tvpi
+ Other Bluetooth/Raspberry PI/Hardware issues
+ My thrilling journey down the Bluetooth ~~rabbit hole~~ stack
+ How searching the Internet for the error was both insightful and infuriating

