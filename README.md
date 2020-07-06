# ObfuscatorSG
An AutoIt obfuscator, written in AutoIt


Big Rule #1:  Anything that can be done, can be undone (sometimes trivially).


Requirements:
-------------
- You must be using the latest release (or beta) of AutoIt.
- The script you wish to obfuscate must first be stripped (via Au3Stripper) using, at a minimum, the "/rm" switch.  I may change this one day, but since stripping only takes a moment...don't count on it.
- You are *strongly* advised to run Au3Check (with all switches) on your script and fix any reported problems prior to obfuscating.

Quick instructions:
-------------------
- Strip your script, using the "/rm" switch.
- Point ObfuscatorSG to your stripped script.  The output will automatically be named "<file>_obfuscated.au3".  Your primary and stripped files will remain untouched.
- If you opt for the Anti-debug option, you *must* compile your script afterwards to run it.
