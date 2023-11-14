https://www.image-line.com/fl-studio-learning/fl-studio-online-manual/html/midi_scripting.htm

https://forum.image-line.com/viewforum.php?f=1994

Available modules:

    playlist
    channels
    mixer
    patterns
    arrangement
    ui
    transport
    device
    plugins
    general
    launchMapPages 

Steps to modify this file for your personal use:

Name the file as device_somename.py, replacing xxxNAMExxx with what you want to call your device. No spaces.

Edit the first line in the file, a comment in the form:

    # name=xxxNAMExxx

Replace xxxNAMExxx with a name for your device.  This will be what shows up in the list of midi controller scripts.

Next,find out what message each pad sends in CC mode.

Most controllers have some sort of layout editor software that will show you these values.

Or you can use MIDI-OX.

Or go to the FL Studio menu "OPTIONS" -> "Debugging Log" to get a window where you can press a pad and see the info.  The CC number is the second number. It is shown as an octal integer.  To convert octal integer to a normal base-10, use a python console and type the octal value N prefixed by 0x and hit enter.  Example:

Debugging log shows these lines when you press a pad:

    AkaiLPD8:  B0  19  00  Control Change: 
    Not handled  Absolute 0%

The second number is 19.

In a python console, type 0x19

    >>>0x19
	
(And press enter) The result is:

    25

(25 is octal 19 shown in base-10)


In the section at the top of the file labeled "CC Assignments", uncomment the lines for banks and pads you want to have scripted functions for.

Replace ``xxx`` with the base-10 integer for the CC value that each pad sends.

If bank 1 pad 1 sends CC number 25, change this:

    # bank_1_pad_1 = xxx

to this:

    bank_1_pad_1 = 25

Leave these lines alone:

    #{{{1 dictionary of events and decorator function
    events = dict()

    def e(data1):
        def data1event(func):
            events[data1] = func
            return func
        return data1event
    # }}}1

(e is a decorator function used to prefix a callback and hook it up to the appropriate CC message.)

Skip past those lines to the bank/pad you want to modify.  Some examples are provided but you may change the function names and bodies.

    # @e(bank_1_pad_1)
    # def stop(event):
    #     print("Stop")
    #     transport.stop()
    #     event.handled = True
	
We'll uncomment those lines:


    @e(bank_1_pad_1)
    def stop(event):
        print("Stop")
        transport.stop()
        event.handled = True

If stop is what you want bank 1 pad 1 (CC# 25) to do, you're done. Save the file.  If you want it to do something else, such as undo for example, change as follows:

    @e(bank_1_pad_1)
    def undo(event):
        print("Undo")
        general.undo()
        event.handled = True


The file goes in ...\Documents\Image-Line\FL Studio\Settings\Hardware\devicename\device_devicename.py.  You will have to create the folder "devicename".

