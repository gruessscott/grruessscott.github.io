---
layout: post
title: Converting files between DOS and Unix
category: tech
---

The newline is a special character or sequence of characters that indicates
the end of a line of text and the start of the next new line. On Unix and
Unix-like systems, this is the Line Feed character ('\n', 0x0A). On Windows
(and DOS), this is a Carriage Return followed by a Line feed ('\r\n', 0xD0A).

Here are two quick methods for converting:

# Converting between DOS and Unix with vim

Inside the Vim editor, hit <Esc> to enter command mode and then enter one of
the following *ex* commands (note: 'mac' is for the old Apple II and pre-OSX 
classic Mac OS):

    :w ++ff=dos
    :w ++ff=mac
    :w ++ff=unix

# Using Perl

In Perl, the conversion can be done with a simple regex:

    find . -name \*.js -exec perl -pi -e 's/\r\n/\n/g' {} \;
