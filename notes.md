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

With some help, we just created an object called code and assigned it to the output of marshal.loads. Then we use the dis module to call dis.dis(code) and dump the byte code. Finally, now we can start looking a the next layer.

The next step is to take this byte code and turn it back into Python to make it more readable. Fortunately uncompyle6 can do this for us on the Mac also. So lets dump the byte code and try to use it with uncompyle6.

Skipping this for a moment and just reading the original beatsaber.py file.
When looking at the source we see the success function displays the flag. The flag is probably decrypted by winning levels but I don’t want to play this game to get the flag.

Since a cool person @KerberToast offered some help we made some progress. The current goal is to just see where the program is calling the
Core.py object we dumped.

Looking closer at the bytecode in the dumped file we created there is a object called Core. This object has a function called levelup. Looking at the code in this function it calls a function unwrap with an argument called seq. If you complete enough levels it loads a variable called data and returns. Otherwise it increments the level counter and increases the beat length or something and continues.

In the beatsaber.py file there is a function called move_blocks. It does some calculations then calls self.core.levelup(key). You cant just null out the blocks as playing and destroying them triggers the creation of a key. So we cant just bypass that check and make all blocks gone and hope to win. We will need to find a way to cheat the game and actually win. My current plan is to find the hit calculations and patch a compare there so that the block is always destroyed. This will require patching that weird marshaled code and screwing with patching python. As I read I am hoping to get lucky and see a spot in the python with source code so I can avoid using more tools.
