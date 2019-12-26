First clone the repository
Read the readme, nothing very useful (no hints)


The only other file is a beatsaber.py file. Because this was created by @malwareunicorn
this is not going to do any harm so just run it. Its a game, ok. Now read the python code.

Use the source. BODY is some byte encoded string. It's quite huge.
There is a call to marshal.loads which operates on this string.
The string is also decoded. Then it is executed with exec.

Now the goal is to get an understanding of what that string is.
It looks like it is serialized python bytecode.
Need to reverse this bytecode back into object code that is more readable.

At this point, we need to look at the networking and basic process information of this executable.
A common sense question after running this is "Where are the assets?"
Are there network operations being performed?
A dirty way to test is to just disable wireless ior ethernet (inclusive or)
This ended up being a waste of time.

Time currently being wasted by Rocket League. Everyone needs a break sometimes.

Ok, we got some hints. We need to look at marshal and how it loads byte code
So, we looked at the marshal loads and it takes a byte-like object.
Now we are trying to decode this string into an object.
We dumped a string representation of this object.

With some help, we just created an object called code and assigned it to the output of marshal.loads. Then we use the dis module to call dis.dis(code) and dump the byte code. Finally, now we can start looking a the next layer. We will call this dump core.py. This is what the beatsaber code calls it too, so thats appropriate.

The next step is to take this byte code and turn it back into Python to make it more readable. Fortunately uncompyle6 can do this for us on the Mac also. So lets dump the byte code and try to use it with uncompyle6.

Skipping this for a moment and just reading the original beatsaber.py file.
When looking at the source we see the success function displays the flag. The flag is probably decrypted by winning levels but I don’t want to play this game to get the flag.

Since a cool person @KerberToast offered some help we made some progress. The current goal is to just see where the program is calling the
Core.py object we dumped.

Looking closer at the bytecode in the dumped file we created there is a object called Core. This object has a function called levelup. Looking at the code in this function it calls a function unwrap with an argument called seq. If you complete enough levels it loads a variable called data and returns. Otherwise it increments the level counter and increases the beat length or something and continues.

In the beatsaber.py file there is a function called move_blocks. It does some calculations then calls self.core.levelup(key). You cant just null out the blocks as playing and destroying them triggers the creation of a key. So we cant just bypass that check and make all blocks gone and hope to win. We will need to find a way to cheat the game and actually win. My current plan is to find the hit calculations and patch a compare there so that the block is always destroyed. This will require patching that weird marshaled code and screwing with patching python. As I read I am hoping to get lucky and see a spot in the python with source code so I can avoid using more tools.

As we look at the code in another pass, we notice the blocks when hit append a key to a buffer of bytes. So you cannot simply just kill all blocks, and the order matters. What I eneded up doing was checking in the function move_blocks and adding some code there. When move_blocks starts, it sweeps the array of blocks and if any of them have a .y1 position of 470 they get called block_hit(block.key). I patched that function to always be a hit.

Last after the level is over we call self.play so we dont have to click in between each level. But this is now slow and painful, so we look at the source again. We don't increase the block speed, but choose to kill the call to sleep instead in the while loop (#?). With this approach it takes about 20 minutes for the game to beat itself but it runs fast enough to be entertaining watching. I plan on writing a better way later but the flag is dumped at the end to a flag.txt file and it is the correct flag.

This writeup is temporary. I'm going to do a better job with links and line numbers. Plus better structure in general this is so informal.

In the end before I get too proper and delete a bunch of stuff, I want to say thanks so much to @KerberToast and @malwareunicorn
Amanda created the CTF and I was actually able to solve it (I made it more difficult). Its so cool to solve, as my first CTF, one that is
from an industry icon. And for @KerberToast it was so cool to meet someone who took the time to help me. I am normally pretty smart,
but this felt a lot like a "first" to me and it was silly how hard I made it on myself. Anyways, the help is appreciated immensely as is the CTF itself and these two have my deep gratitude. It was a fun ctf I took lots of breaks and drinks it was really fun. And I did learn a lot and some good new skills so its a definite win. Best gift.

Took the time today to add a programatic solver. All it does is strip out the logic from the original game and just eliminates all the graphics and game code. With this 
new approach it takes seconds to get the flag instead of almost 15 minutes. Still I kinda miss watching the blocks race toward certain death but this was only like a 20 minute
hack job in python.
