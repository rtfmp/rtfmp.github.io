---
title: Use input-output redirection (>, >>, |, 2>, etc.)
layout: post
comments: yes
publish: false
---
* Command < file  Get input from file
* Command  file    Same as above
* command  > file   Send output  to file
* command  >> file  Append stdput  to file
* command  2> file  Send error  messages  to file
* command  2>> file   Append  error messages to file
* command  > file 2>&1 Send both output and error to file
* command  >> file 2>&1  Append both output and error  to file
* command1 | command2  Send output of command1 as input to command2
* command   | tee  file   Send output to both screen and file   
* 0 File descriptor for stdin, optional
* 1 File descriptor for stdout, optional
* 2 File descriptor  for stderr

When we  run a Linux command, the output goes  to the screen. This is called standard output (stdin).  If the command was not successful,  error messages appear on  the screen as well. This is called standard error (stderr).  If the command expects an input and  we do not provide it as an argument, it will wait until we enter it using  the keyboard. This is called standard input (stdin).

Explain File Descriptor
In Linux, everything is a file.  The OS assigns a number to an open file called file descriptor. The three standard stream files - stdin, stdout and stderr  are always open and their file descriptors are 0, 1 and 2.

If  we want the input to come from a file or other command and not the keyboard (stdin).  We call this input redirection.  We use "<"  operator . The use of "<"  operator is optional and  the usual usage is to pass the filename as an argument to the command.

We can  send the output of a command  to a file  instead of the screen. This is called stdout redirection.  We use  ">" and ">>" operators.

We can also send  just the error messages  to a file  instead of the screen. This is called stderr redirection.  We use    "2>" and "2>>" for stderr.

When we run a command and  we want to do something more to the output before it goes to the screen,  we pass the output  as input to another command using pipe (very central to Unix Philosophy) represented by "|".  We do this by placing vertical bar "|"  between commands. If we want to display the output to the screen and save it to a file as well, we use   pipe and tee. 

Examples:

Command < file  Get input from file
Command  file    Same as above
command  > file   Send output  to file
command  >> file  Append stdput  to file
command  2> file  Send error  messages  to file
command  2>> file   Append  error messages to file
command  > file 2>&1 Send both output and error to file
command  > file 2>&1  Append both output and error  to file
command1 | command2  Send output of command1 as input to command2
command   | tee  file   Send output to both screen and file   
