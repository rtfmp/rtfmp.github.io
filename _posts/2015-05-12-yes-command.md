---
layout: post
title: The yes command
published: false
---

The yes command prints it's argument repeatedly until killed. If no argument is given, it will print y until killed.
It seeemed useless right? Well, not exactly. If we have a script or a command that requires an answer Yes/No to continue, we can use the yes command to provide the answer.

    yes No | script or command

This is especially handy if you have to run the script repetively. Most commands today has  built in capability to do this by way of -f (force) or -y  option. In the absense of this, we can  pipe yes into the command.

yes may sound like a simple command but it has inspired a 12 pages page academic paper- [The Trivial Program "yes"](http://nickm.com/trope_tank/TROPE-12-01.pdf). In Linux, yes is a part of coreutils. Read:

    man yes
    info coreutils 'yes invocation'


This is from an early [man page of yes from 2.11BSD](http://minnie.tuhs.org/cgi-bin/utree.pl?file=2.11BSD/man/cat1/yes.0).

    YES(1)		    UNIX Programmer's Manual		   YES(1)
    
    
    
    NAME
         yes - be repetitively affirmative
    
    SYNOPSIS
         yes [ _e_x_p_l_e_t_i_v_e ]
    
    DESCRIPTION
         _Y_e_s repeatedly outputs "y", or if _e_x_p_l_e_t_i_v_e is given, that
         is output repeatedly.  Termination is by rubout.
    
    Printed 11/26/99	 April 29, 1985

