# ObfuscatorSG
An AutoIt obfuscator, written in AutoIt


Big Rule #1: Anything that can be done can be undone (sometimes trivially).

Big Rule #2: I don't guarantee this will work with every script.  Particularly, but not limited to, you having bad coding practices.


Requirements:
-------------
- You must be using the latest release (or beta) of AutoIt.
- The script you wish to obfuscate must first be stripped (via Au3Stripper) using, at a minimum, the "/rm" switch.  I may change this one day, but since stripping only takes a moment, don't count on it.
- You are *strongly* advised to run Au3Check (with all switches) on your script and fix any reported problems prior to obfuscating.


Instructions:
-------------
- Strip your script, using the "/rm" switch.
- Point ObfuscatorSG to the stripped script.  The output will be automatically named "<file>_obfuscated.au3".  Your primary and stripped files will remain untouched.
- If you opt for the Anti-debug option, you *must* compile the obfuscated script to run it.  That's part of what this utility does.
- When compiling, change whatever compiling/resource options you like, but leave Au3Check, Au3Stripper, and Tidy disabled.
- I do not recommend the Anti-VM option, even though it is provided.  In recent times, it is far more common for people to use sandbox/virtual environments for a variety of reasons.


Goal:
-----
All software is subject to analysis, particularly interpreted languages like AutoIt.  Since there are a variety of utilities that will extract your script, as-is, from the 'compiled' executable, the goal is to make analysis of the script a hassle.  No solution will stop those with skill, determination, and time on their hands.  But we can at least attempt to raise the bar in terms of who fits that bill.

Several commercial (paid) solutions, with regard to AutoIt, lean heavily on adding junk code, formulaically encoding characters, etc.  Some of their methods are clever.  The problem is that it's still formulaic, and once the method is written it isn't changed.  This has resulted in several utilities/scripts which have little difficulty picking-apart large portions of a script.  Now, while the method(s) I've chosen to pursue are not ground-breaking, complicated, or even slightly clever...they are at least different in some respects.  Rather than trying to be fancy, I've focused on making analyzing a script a pain in the butt.  After all, that's really the best we can hope to do (see Big Rule #1).  To wit:

What it does:
-------------
- Like other solutions, it will rename functions (and their respective calls) and variables.  This is done with '1's and 'l's of varying length.  Functions are prefixed with '_' and variables with '$_'.  Additionally, the entries are shuffled with each run.  Since they have a similar naming convention, and are of varying length, anyone trying to do simplistic search & replace operations will wreck the script unless careful (see Big Rule #1).
- Nearly all native functions (macros, 'FileWrite', 'MsgBox', 'UBound', etc.) will be assigned to variables (same as above) and any calls to those functions will be replaced with those assignations.  Again, this won't stop someone with enough determination and skill (Big Rule #1), but will require more work on their part and prevents anyone from seeing exactly what your script is doing at first, or even second, glance.  I have no idea why commercial solutions haven't employed this simple trick in addition to their others.  Some of their methods are complicated, but you can still easily see what they're doing ('BitRotate', 'StringReverse', etc.).  Make the person reversing your script work for it!
- String encoding:  Like several other solutions, ObfuscatorSG will encode your strings into AscW/ChrW numercial representations.  However, here's where I decided to go further.  Many methods encode strings (typically with RC4) and create a custom function with a decryption key/process to reveal the original at run-time.  Well, that's trivially easy to undo once you flesh-out the key, which is hard-coded in the script.  As a former sneaky government employee, I've always found it best to hide in plain sight.  So here's what I decided to do:  After converting a string into the AscW representation, for each character I randomly pull from an array of numercial formulas (each AscW character, 32 to 128, currently has 50 formulas each from which to draw) which result in the desired number.  Example: AscW(32), which is <space>, might be changed to AscW(18^3/81-40).  Furthermore, as mentioned previously, native functions are assigned to variables, so it would further result in being displayed like so:  $_1ll1(18^3/81-40).  This applies to each and every use of a particular character, even on the same line.  If you have a line with seven 't's, *each* 't' is likely to have a different formula to solve for.  The theoretical maximum number of unique formulas which might appear in your script is 4800.  If someone's adept with Regular Expressions, it's conceivable they can single-out the formulas, and with some extra work...perhaps even solve for them.  But, we just added a lot more work for them to perform.  No more easy search & replace for an ASCII code.
- Extending on the string encoding method, I've chosen to use the mathematical formula tactic for common integers (+=1, -3, =1, =0, >1, etc.), and also for any array subscripts your script might contain.  Each number is, again, randomly represented by one of 50 formulas.  More work (hopefully) for the person trying to analyze your script.
 - Anti-debug & anti-VM:  Nothing clever here.  The anti-debug option will add a compilation check + a windows DLL call to determine if a debugger is present (if you select this option, you will have to compile your obfuscated script in order to run it).  The anti-VM option (though I don't recommend using it) checks for common processes associated with virtual environments.  Most solutions which add these capabilities tend to "Exit" when found.  Well, someone analyzing your script might immediately look for that keyword to zero-in on the checks.  ObfuscatorSG does something different and it's entirely harmless.  We just don't want to make things too easy for people to find.  Also, all functionality associated with these checks is obfuscated in the same manner as the rest of the script, including the DLL call.


Potential problems:
-------------------
- AutoIt has a 4096 character limit per line.  Because of the method used to encode strings, any line containing strings is going to end-up much larger than it was previously.  If your lines containing strings consist of fewer than, say, ~300 characters...you're probably okay.  If they're longer than that, you're exhibiting bad coding practices.  Write shorter lines.


Suggestions:
------------
- I'm all ears if your suggestion is well thought-out and expressed.


Reporting a problem:
--------------------
- Any reports must contain a *minimally* functional, unobfuscated example (complete with necessary includes) that reproduces the problem when obfuscated.  The example must successfully run, without any changes or warnings/errors (so please run au3check with all switches before submitting).  We are, after all, talking about an obfuscator so I'm not likely to spend any time trying to guess where you might be encountering a problem.  Make it easy on me and I'll be happy to help.
