# MIDI Setup

# TODO formatting
# https://docs.google.com/spreadsheets/d/1m3_4BjN57dsS56sorsvP24aNKYUUQdjAftF5MF5CxXU/edit?usp=sharing

This is a personal filtering program written using Bram Bos' Mozaic (TODO), Audeonic's StreamByter (TODO), and Audeonic's MIDIFire (TODO).

Using MIDIFire as a host, MIDI is input into an iPad using a Novation LaunchKey Mini mkII and a Behringer X-Touch Mini

This MIDI is sent through a MIDIFire Scene (see screenshot - the .mfr file) (TODO) which filters input to provide desired functionality, and outputs expected notes and CC's to an iConnectMIDI2+. The first iConnectMIDI2+ output port is split into five midi outs using a splitter, which goes to the following hardware:
* Channels 1-6 - the six channels of the Volca Drum
* Channel 7 - the Volca FM
* Channel 8 - the Volca Bass
* Channel 10 - the Volca Beats
* Channel 11 - the Volca Sample, using the modifications of Pajen firmware (TODO)
* Channel 12 - the Volca Sample, using the modifications of Pajen firmware (TODO)

In the middle of this is the application Modstep, which can act as a MIDI DAW for input. In order to keep things dynamic, Modstep only takes the immediate input, not the processed input (i.e. arpeggiated notes or notes sent from the programmed phrase)

The program provides the following functionality, documented further by the document here:

* a built in 12 mode (asc, desc, rand, order - all with octaves) arpeggiator which sends notes out based on bpm, or frequency of clock input
* a phrase feature, which allows the user to program in phrases (with rests) using the specified hardware
* the ability to toggle these functions on and off
  * the user is also able to control the velocity, gate, and tempo of the notes from these functions
* a latch feature which sustains played note, or, if the functions are on, the currently playing function
* push control - when a knob is pushed down and turned, outbound CC's are sent, but when the knob push is released, the knob CC from before the knob push is re-sent
  * this allows for non-committal experimentation during live sets
  * there is also a global knob push feature which, when active, is as if all knobs are currently pushed
    * all changes made while global knob push is active are reverted when global knob push is deactivated
* pad mode toggle - this toggles between the Volca Beats (channel 10) and Volca Sample (channel 12) for ten of the pads on the LaunchKey Mini (the final six are constant and used for the six parts of the Volca Drum)
* four MIDI LFO's for each channel that can be set to modulate any of the knobs on the X-Touch mini
  * these LFO's can be chain modulated (from both middle and bottom), inverted, synced to the incoming tempo, restarted at any time, and have controllable rate and depth
  * they also have a full range of LFO types: Sine, Cosine, Square, Triangle, RampUp, RampDown, SH
* importantly, this can all be done *simultaneously* - the user can latch a phrase with lfo modulations, move to the next channel and layer on top of that

In addition to these core features, there are a handful of side features as well:

* the ability to transpose all output from within one octave to two octaves above or below (or mute the transpose sliders to avoid errors)
* active muting of the arpeggiator and phrase functions, as well as the LFO's
* providing an infinite gate time for the arpeggiator and phrase functions, rather than relying on a set time
* the ability to clear a single channel, or all channels at once
* for the Volca Drum, the program offers the option to quantize pitch for either one or both of the current patch's parts

At this point, I wouldn't expect this to be used by anyone else (hence the 'Personal' in the title) but if so, hopefully the full documentation in the linked worksheet above would be enough to grasp which buttons do what, at least.

Please note - at the moment the program currently takes up a large amount of processing power, and can be inconsistent in loading.
Usually loading the scene a couple of times will eventually lead to a successful load, however it may be best and most reliable to remove a channel or two from the mix, which is fairly arbitrary.
Additionally, this program should not produce audible latency. Every once in a while on a load, it will - this is not expected.
Reloading can fix this.

With regards to the process of making this - if I could do it again (and known how easy it would be to use the Apple Developer kit), I would have certainly done that. This was initially written purely in StreamByter alone (~8000 lines of code) and translated / reduced to Mozaic where it (mostly) is now (~2000 lines of code)*. Due to the processing restrictions placed by the software involved, which for the most part was likely not expected to lift as much as it is doing here here, some of the coding ended up being not as pretty or somewhat convoluted. This project would have *greatly* benefitted from the ability to use OOP.

* Overall, while Mozaic was for the most part superior in many ways, StreamByter had it beat on simultaneous MIDI sysex message processing (>32 messages at once), and having the correct sequence of MIDI messages (e.g. Mozaic sends all sysex first, at the same time, then MIDI note on/offs rather than sending in the order dictated (requested?) by the code)(I had to use timers to correct this, which, while necessary, felt sloppy).

I ended up settling on a system in which each channel had its own separate routing (one of the many places OOP would have been helpful), and hardware messages were only processed by the current channel in use by the hardware. In order to communicate between nodes (or instances the AUV3's) I needed to utilize a library of custom sysex messages (see documentation) - each of which represented either a variable or other MIDI message. From my experience, I learned that, to some extent, the most scalable implementation of the program would involve sysex-ifying as many messages as possible, as custom sysex messages can be filtered and comprehended in a much more flexible way than CC's or Note On/Off messages. As it stands, I'm sure there are plenty of ways to improve*, but overall the finished product feels like a fairly polished and effective way of handling everything involved with the tools that were provided. Next time, however, I think the tools should be better selected.

*regarding to the stability of the final product - ideally this would be much, much better. However, provided the extensive functionality that was built out, I spent a large amount of time improving the performance (translating SB to Mozaic, removing filters) and am uncertain there would be much else to be done.
