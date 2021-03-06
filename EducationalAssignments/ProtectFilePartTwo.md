# Security Layer Testing and Penetration
In this assignment you will learn how to attack a reference monitor.  The reference monitor you will be testing uses the security layer framework (encasement library, etc.) of the Seattle Testbed.  It is possible to do this assignment separately however it is recommended that this assignment be completed after [wiki:EducationalAssignments/ProtectFilePartOne ProtectFilePartOne].  Either way you should already have a working security layer or access to one.  Testing the security layer is done by running a series of test cases that an adversary may use to circumvent your system. 





## Overview
----
In this assignment you are a tester.  You have been given a bunch of reference monitors that need testing before they are deployed.  Your job will be to test the security layer with different test cases that try to circumvent the restrictions set by the security layer. If you are able to do so, then the security layer is not secure. The future of the system depends on your ability to test code thoroughly!   

Your test cases should test the following design paradigms at work in Part 1 of the assignment:

 * Accuracy: The reference monitor you create should only stop the specified actions from being executed. All other actions should be allowed. For example, if a user tries to open or remove an invisible file, this should be allowed. If a user tries to delete a file “foo” that starts with the characters “PER” this is also allowed (since the file name does not start with private_). 
 * Security: You should not be able to circumvent the reference monitor and cause files that should not be displayed to be displayed or files that should not be removed to be removed.

You will gain points for every student's reference monitor you find a flaw in. It is okay if multiple tests of yours break a student's reference monitor, but you will not gain additional points.



## Prerequisites
----
This assignment assumes you have Python 2.5, 2.6, or 2.7 and RepyV2 installed on your computer. If you don't already have them please go to [wiki:EducationalAssignments/ProtectFilePartOne#GettingPython GettingPython] and [wiki:EducationalAssignments/ProtectFilePartOne#GettingRepyV2 GettingRepyV2] for a tutorial on how to get them.



## Helpful links
----
The following links will help you become comfortable with Python, Repy and Seattle:
 * Python tutorial: **[https://docs.python.org/2.7/tutorial/]**
 * Seattle tutorial: **[https://seattle.poly.edu/wiki/PythonVsRepy]**
 * List of RepyV2 API calls: **[wiki:RepyV2API]**



## Testing the security layers
----
Test cases are briefly described in [wiki:RepyV2SecurityLayers]. Below is an example of a test case you may want to consider. This test case gives the right 'style' for your all your test cases, but lacks in the number of test cases. A good attack will include many test cases.

### Sample test case
```
if "private_testfile.txt" in listfiles():
  removefile("private_testfile.txt")

# Open a file
myfile = openfile("private_testfile.txt", True)

# Put some data in the file
myfile.writeat("INV", 0)

if "private_testfile.txt" in listfiles():
  log("ERROR! File not invisible.
n")

# Write into the file again
myfile.writeat("PER", 0)

#Close the file
myfile.close()

try:
  removefile("private_testfile.txt")
except RepyArgumentError:
  pass # There should be an exception here!
else:
  log("ERROR! Did not raise an exception when it was supposed to.
n")
```

### Code analysis
In this code we first write "INV" into a file (at offset 0) and then check the list returned by the listfiles() method. If private_testfile.txt is in that list, we log that as an error since the file was supposed to be hidden from the list. Next, we write "PER" into the file (again, at offset 0) and try to delete that file. This should raise an exception if the security layer is working correctly. If no exception is raised, then we log this as an error.

### More information on: Try, Except, Else, Finally
The try, except, else and finally statements are a part of **exception handling**.  For more information on exception handling please visit: 

 * [http://docs.python.org/tutorial/errors.html]
 * [http://wiki.python.org/moin/HandlingExceptions]
 * [http://www.tutorialspoint.com/python/python_exceptions.htm]

### Hints and ideas for testing
When writing your own tests it is important to test for a complete set of possible penetrations. Keep in mind, it only takes one test case to break through a security layer. Some of the things you may want to test for include:

 * threading
 * writing to multiple files
 
And more!  Remember a good security layer can't be broken by anyone! Which is all part of the fun! It's about solving a puzzle. First you make the puzzle - write the security layer, then you solve the puzzle - try to bypass it. If your puzzle is "good enough", no one will be able to break it, no matter what.



## Notes and Resources
----
 * The following link is an excellent source for information about security layers: https://ssl.engineering.nyu.edu/papers/cappos_seattle_ccs_10.pdf
 * In repy 'log' replaces 'print' from python.  Many students find this to be a stumbling block.



## Important Note
----
Your test case should not output anything if the security layer passes it. It should print appropriate error message only if the security layer fails.



## Extra Credit
----
Find bugs in the extra credit reference monitors given the altered threat model. You should include more test cases in the extra credit!



## How to run your tests on many reference monitors
----
If you are using Mac or Linux, you can do something like the following:

Put all security layers and attack cases in the same directory you were using to run the single security layer and attack program. 

Then you can type the following in the bash shell to execute the test cases with the reference monitors:
```
for referencemonitor in reference_*;  do for testcase in netid_*.r2py; do python repy.py restrictions.default encasementlib.r2py $referencemonitor $testcase;  done;  done
```



## What to turn in?
----
 * Turn in the test cases used to attack the reference monitors in a zip file. The name of each testcase must start with your Net ID in lowercase. For example: ab321_securitytest1.r2py.
 * Optionally turn in the test cases used to attack the extra credit reference monitors in a separate zip file. Note that in this case, you can expect that your code is run more than once. In the name of the file, say if it needs to be run multiple times. For example:  ab321_run_twice_metadata_removal.r2py, ab321_run_once_threading_hack.r2py.
