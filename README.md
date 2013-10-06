mini-vtysh
==========

 A shell which is similar to Cisco IOS CLI , tailored from GNU Zebra.

 GNU [Zebra][0] is free software that manages various IPv4 and IPv6 routing
protocols. It provides a shell which is similar to Cisco IOS CLI--VTY shell.

 So, I just want to tailor the source code(version is 0.95a ), to extract a 
small vtysh for eductaion purpose.

 I modified the configure.ac for removing the unuseless option,removed some 
source file in zebra directory. Certainly, most source file in vtysh
directory were changed.

 I tried my best to extract a clean and simple vtysh, but the result was 
far from satisfactory. Some features that I want to implement, if you know 
how to implement them then please do them for me! 

 Here are some notes or articles ( most of them are wrote in Chinese) for your reference.
 + [Evernote sharing notes][1]

Please rename the README.md to README if some strange error happens!:)


Basic Installation
==================
The simplest way to compile this package is:

  1. 'cd' to the directory containing the package's source code and type
     './configure' to configure the package for your system. 

  2. Type 'make' to compile the package.

  3. Type 'make install' to install the programs and any data files and
     documentation.

  4. You can remove the program binaries and object files from the
     source code directory by typing 'make clean'. 

More information
================
 I did't change or remove the ChangeLog,TODO or other basic files in the 
root directory of the package's source code.See the file README.zebra for 
original readme and other infomation.

[0]: http://www.gnu.org/software/zebra/
[1]: https://www.evernote.com/pub/mengyingchina/simshell
