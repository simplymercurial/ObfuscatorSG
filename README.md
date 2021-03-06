# ObfuscatorSG
An AutoIt obfuscator, written in AutoIt

Big Rule #1: Anything that can be done can be undone (sometimes trivially).


Requirements:
-------------
- You must be using the latest release (or beta) of AutoIt.
- You are *strongly* advised to run Au3Check on your script (with all switches) and fix any reported problems prior to obfuscating.


Instructions:
-------------
- Point ObfuscatorSG to your script.  The output will be automatically named "(file)_obfuscated.au3".
- If you opt for the anti-debug feature, you *must* compile the obfuscated script in order to run it.
- When compiling, change whatever compiling/resource options you like, but leave Au3Check, Au3Stripper, and Tidy disabled.
- I do not recommend the Anti-VM option, even though it is provided.  In recent times, it is far more common for people to use sandbox/virtual environments for a variety of reasons.


Goal:
-----
All software is subject to analysis, particularly interpreted languages like AutoIt.  Since there are a variety of utilities that will extract your script, as-is, from the 'compiled' executable, the goal is to make analysis of the script a hassle.  No solution will stop those with skill, determination, and time on their hands.  But we can at least attempt to raise the bar in terms of who fits that bill.

Several commercial (paid) solutions lean heavily on adding junk code, formulaically encoding characters, etc.  Some of their methods are clever.  The problem is that it's still formulaic, and once the method is written it isn't changed.  This has resulted in several utilities which have little difficulty picking-apart large portions of a script.  Now, while the methods I've chosen to pursue are not ground-breaking, they are different in many respects.  Rather than trying to be fancy, I've focused on making analysis of a script a colossal pain in the butt.  To wit:


What it does:
-------------
- First and foremost, ObfuscatorSG will strip your script using ideal parameters (your original .au3 file will not be touched).  The latest Au3Stripper is included, will be extracted to a temporary directory, and will be removed when you exit.  One distinct advantage of working from a stripped script is that all of the 'included' AutoIt functions will be obfuscated along with your own code.  This makes it much more difficult to distinguish between run-of-the-mill code and yours.
- Like other solutions, ObfuscatorSG will rename functions (and their respective calls) and variables.  This is done with '1's and 'l's of varying length.  Additionally, the 1/l enumerations are shuffled with each run.  Since they have a similar naming convention, and are of varying length, anyone trying to do simplistic search & replace operations will wreck the script unless careful (see Big Rule #1).
- Nearly all native functions (macros, 'FileWrite', 'MsgBox', 'UBound', etc.) will be assigned to variables (same as above) and any calls to those functions will be replaced with those assignations.  Again, this won't stop someone with enough determination and skill, but will require more work on their part and prevents anyone from seeing exactly what your script is doing at first, or even second, glance.
- String encoding:  Like several other solutions, ObfuscatorSG will encode your strings into Ascii numercial representations (32-255).  However, this is taken a couple of steps further.  The resulting Asc numercial representations are then converted to a random selection of 'Bit' operations (BitAnd, BitXOR, etc.) and the corresponding integers which result in the original Asc code (If "Extra encoding" is chosen, the calls themselves are also obfuscated).  This applies to each and every use of a particular character, even on the same line.  If you have a string with seven 't's, *each* 't' will likely use a different Bit operation and also have differing integer representations.  If someone's adept with Regular Expressions, it's conceivable they can single-out the formulas and perhaps even solve for them, but they'll first have to figure-out *which* bit operation is used for each and every character.  Furthermore, a subset of the bit integers are further converted into mathematical formulas (see below), while an additional subset is further converted into more bit-math operations (optional).  The wide variation of lengths/structures should make RegEx analysis/replacement a nightmare.
- Extending on the string encoding method, I've chosen to use a mathematical formula tactic for common integers (+=1, -3, =1, =0, >1, etc.), and also for any array subscripts your script might contain.  Each number is changed, randomly, into one of 50 mathematical formulas for each integer (the result of some linear equations I wrote).  Optionally, portions of these formulas will be further converted into additional formulas.
- Removes spaces from operators/delimiters to reduce character usage per line (see "Potential problems" below).
- Anti-debug & anti-VM:  Nothing clever here.  The anti-debug option will add a compilation check + a windows DLL call to determine if a debugger is present. If you select that option, you will have to compile your obfuscated script in order to run it.  The anti-VM option, though I don't recommend using it, checks for common processes associated with virtual environments.  Most solutions which add these capabilities cause a program to "Exit" when found.  Well, someone analyzing your script might immediately look for that keyword to zero-in on the checks.  ObfuscatorSG does something different and it's entirely harmless.  We just don't want to make things too easy for people to find.  Also, all functionality associated with these checks is obfuscated in the same manner as the rest of the script.
- Some red herrings are thrown-in for good measure.
- All functions, including AutoIt's UDFs, will be grouped together and sorted by their newly-randomized names (now optional).
- Last, but not least, ObfuscatorSG will check the length of each line of obfuscated code to see if any line exceeds 4095 characters (an inherent limitation for AutoIt source code. See below).  The line(s) which exceed this count, if any, will be displayed.  The reported line numbers refer to the exact line of the *obfuscated* script.


Potential problems:
-------------------
- AutoIt has a 4095 character limit per line.  Because of the method used to encode strings, any line containing them is going to end-up much larger than it was previously.  If your lines containing strings consist of fewer than, say, ~300 characters...you're probably okay.  If your obfuscated script is barely exceeding the limit, you can try running the obfuscator again and hope some of the functions/variables pick-up shorter enumerations.  However, the correct solution is to rewrite that portion of code.  You will be informed which lines, if any, exceed this threshold.


Suggestions:
------------
- Open an issue and select "Feature request."


Reporting a problem:
--------------------
All non-trivial bug reports should contain a *minimal* unobfuscated example (with any necessary includes) that reproduces the problem when obfuscated.


Notable changes:
----------------
- 1.0.0.107: String-intensive scripts experience a slight lag when the Chr() calls are obfuscated.  For this reason, obfuscation of the Chr() function (just the call) has been moved to the "Extra encoding" menu option.  Every other aspect of string encoding remains the same, regardless of options.
- 1.0.0.102: Common integer replacement is further obfuscated by converting a subset of the mathematical formulas into additional formulas (optional).
- 1.0.0.93:  Third round of string obfuscation is now optional.  Function sorting is now optional (for troubleshooting).
- 1.0.0.89:  After obfuscation, all functions will be grouped together and sorted by their newly-randomized names
- 1.0.0.87:  Ensure certain functions commonly associated with strings pick-up a shorter, but still randomized, enumeration to mitigate changes in 1.0.0.82.
- 1.0.0.82:  In a third round of string obfuscation, a subset of the bit math integers will be converted into random mathematical formulas the same way other, common integers are.  An additional subset is further converted into *more* bit-math operations.
- 1.0.0.68:  Added post-obfuscation line length check and notification of which line(s), if any, exceed 4095 characters (AutoIt limitation).
