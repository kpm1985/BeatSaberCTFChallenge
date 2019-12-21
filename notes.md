First clone the repository
Read the readme, nothing very useful (no hints)
The only other file is a .py file, read it.
Note, the beatsaber.py file says ubuntu but mine won't work with python-tk
Grab a mac. Since I have an old macbook pro this is ok.
I have 3.7 on my box but its easy to install, even I didn't screw it up.

Read the source. BODY is some byte encoded string. It's quite frankly ****ing huge.
There is a call to marshal.loads which operates on this string.
The string is also decoded. Then it is executed with exec.

Now the goal is to get an understanding of what that string is.
It looks like it is serialzed python bytecode.
Need to reverse this bytecode back into object code that is more readable.

At this point, I need to look at the networking and basic process information of this executable.
A common sense question after running this is "Where are the assets?" 
Are there network operations being perfomed? 
A dirty way to test is to just disable wireless ior ethernet (inclusive or)

Time currently being wasted by Rocket League

