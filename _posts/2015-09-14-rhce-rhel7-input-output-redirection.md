---
title: Input-output redirection 
layout: post
comments: yes
---
	command < file Gets input from file
	command   file Gets input from file
	command > file Sends output to file
	command >> file Appends output to file
	command 2> file Sends error to file
	command 2>> file Sppends error to file
	command > file 2>&1 Sends output and error to file
	command >> file 2>>&1 Sppends output and error to file
	command1 | command2 Output of command1 becomes the input of command2
	command | tee file Sends output to both file and screen
	0,1,and 2 are the file descriptor for standard input, output and error respectively
	
	
When you run a command, input is expected from the standard input which is the keyboard. The output and error messages go the standard output which is the screen. Input can come from a file or other command. This is called input redirection.

We can send the output to a file instead of the screen. This is called output redirection. We can do the same with error. We use piping to send the output of a command as input to another command.
