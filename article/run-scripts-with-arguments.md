# Running a batch file or bash script with arguments

Passing arguments to a batch file (i.e. Windows) is different from doing it with a bash script (i.e. MacOS or Linux). 

## Environment
* Windows 7 and MacOS High Sierra (10.13.5)
* Python 3.7

## Code
The code below illustrates how to run a script on Windows and MacOS.

### Windows
```python
import subprocess

command = ['run.bat', 'arg1', 'arg2']
completedprocess = subprocess.run(command)
```

run.bat
```
@echo off
echo %1
echo %2
```

### MacOS
```python
import subprocess

command = ['./run.sh arg1 arg2']
completedprocess = subprocess.run(command, shell=True)
```

run.sh
```
echo $1
echo $2
```

## Code Explained
Notice two differences:
* On Windows, the command and arguments is a list. On MacOS, command and arguments is one string.
* shell=True is declared on MacOS

The [Python documentation](https://docs.python.org/3.7/library/subprocess.html#subprocess.Popen)  explains the reason for the above differences. 

According to the documentation, shell=true is not required to run batch files.
> On Windows with shell=True, the COMSPEC environment variable specifies the default shell. The only time you need to specify shell=True on Windows is when the command you wish to execute is built into the shell (e.g. dir or copy). You do not need shell=True to run a batch file or console-based executable.

On MacOS, without shell=True an exception is thrown. The documentation states that the command and arguments is one string. If it is a list, the additional items are treated as arguments to the shell and not to the command.
> On POSIX with shell=True, the shell defaults to /bin/sh. If args is a string, the string specifies the command to execute through the shell. This means that the string must be formatted exactly as it would be when typed at the shell prompt. This includes, for example, quoting or backslash escaping filenames with spaces in them. If args is a sequence, the first item specifies the command string, and any additional items will be treated as additional arguments to the shell itself. 


