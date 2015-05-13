---
layout: post
title: The yes command and a bit of its history
---

The yes command prints it's argument repeatedly until killed. If no argument is given, it will print y until killed.
It seeemed useless right? Well, not exactly. If we have a script or a command that expects an answer Yes/No to continue, we can use the yes command to provide the answer.

    yes No | script or command

This is especially handy if you have to run the script repetively. Most commands today has  built in capability to do this by way of -f (force) or -y  option. In the absense of this, we can  pipe yes into the command.

yes may sound like a simple command but it  inspired a 12 pages page academic paper- [The Trivial Program "yes"](http://nickm.com/trope_tank/TROPE-12-01.pdf). 

In Linux, yes is a part of coreutils. Read:

    man yes
    info coreutils 'yes invocation'


This is from an early [man page of yes from 2.9BSD](http://minnie.tuhs.org/cgi-bin/utree.pl?file=2.11BSD/man/cat1/yes.).

    YES(1)              UNIX Programmer's Manual               YES(1)
    
    NAME
         yes - be repetitively affirmative
    
    SYNOPSIS
         yes [ expletive ]
    
    DESCRIPTION
         Yes repeatedly outputs y, or if expletive is given, that is
         output repeatedly.  Termination is by rubout.
    
    BUGS
         Boring.
    
    Printed 12/7/82

The C code of yes command from [2.9BSD](http://minnie.tuhs.org/cgi-bin/utree.pl?file=2.9BSD/usr/src/cmd/yes.c) is quite simple.

    main(argc, argv)
    char **argv;
    {
    	for (;;)
    		printf("%s\n", argc>1? argv[1]: "y");
    }
    
A printf function in an infinite for loop that prints "y" if not command line argunment is give. However, the current GNU implemenation is little more complex than this, see the source code [here]((http://nickm.com/trope_tank/TROPE-12-01.pdf)).
