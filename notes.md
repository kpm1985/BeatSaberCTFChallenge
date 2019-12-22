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
It looks like it is serialized python bytecode.
Need to reverse this bytecode back into object code that is more readable.

At this point, I need to look at the networking and basic process information of this executable.
A common sense question after running this is "Where are the assets?" 
Are there network operations being performed? 
A dirty way to test is to just disable wireless ior ethernet (inclusive or)

Time currently being wasted by Rocket League

Ok, we got some hints. We need to look at marshal and how it loads byte code
So, we looked at the marshal loads and it takes a byte-like object.
Now we are trying to decode this string into an object. 
We dumped a string representation of this object.

After banging my head, drinking some beer and begging for help we are back again. This time with more time, more patience, more beer and its raining outside. This shit is on lock.

With some help, we just created an object called code and assigned it to the output of marshal.loads. Then we use the dis module to call dis.dis(code) and dump the byte code. Finally, now we can start looking a the next layer.

