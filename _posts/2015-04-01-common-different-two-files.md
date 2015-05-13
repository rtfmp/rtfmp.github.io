---
layout: post
title: Find the common and different lines between two files
comments: true
---

A co-worker was working on patching. He had two files containing a list of hostnames. He wanted to filter out hosts appearing in both files. I gave him a few lines of for loop and grep which did the job. A little later he discovered he could use `-f` parameter in `grep`. 

    [rtfmp@centos7 ~]$ cat file1
    host1
    host2
    host3
    host4
    host5
    host6
    host7
    host8
    host9
    host10
    [rtfmp@centos7 ~]$ cat file2
    host3
    host4
    host5
    host6
    host7
    [rtfmp@centos7 ~]$ grep -f file1 file2
    host3
    host4
    host5
    host6
    host7
    [rtfmp@centos7 ~]$

Another co-worker was working on something else and he needed the opposite. Print all lines that  is not common between files. I gave him this:

    for i in `cat file1`
    do
      grep $i file2 > /dev/null
      if [ $? -ne 0 ]
      then
        echo $i >> file3
      fi
    done

Sample output:

    [rtfmp@centos7 ~]$ for i in `cat file1`
    > do
    > grep $i file2 > /dev/null
    > if [ $? -ne 0 ]
    > then
    > echo $i >> file3
    > fi
    > done
    [rtfmp@centos7 ~]$ cat file3
    host1
    host2
    host8
    host9
    host10
    [rtfmp@centos7 ~]$

This filters all lines that are in file1 but not in file2. Switch the file names to do the opposite.
