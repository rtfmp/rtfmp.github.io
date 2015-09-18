---
title: Use input-output redirection 
layout: post
comments: yes
published: false
---

*command < file  Get input from file
*command  file    Same as above
*command  > file   Send output  to file
*command  >> file  Append stdput  to file
*command  2> file  Send error  messages  to file
*command  2>> file   Append  error messages to file
*command  > file 2>&1 Send both output and error to file
*command  >> file 2>&1  Append both output and error  to file
*command1 | command2  Send output of command1 as input to command2
*command   | tee  file   Send output to both screen and file   
*0 File descriptor for stdin, optional
*1 File descriptor for stdout, optional
*2 File descriptor  for stderr

