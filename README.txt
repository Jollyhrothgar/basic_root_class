This module is designed to show you how to write your own class, compile it to your own
library, and load and run that library in root with a macro.

***Standard Warning***:
This tutorial was written by Mike Beaumier. I am a graduate student at UCR,
and am not a programmer by training. These tutorials may not represent the
best programming style, or standard coding conventions - though I am in a
continual process of self-teaching programming, coding conventions, and trying
to become a better programmer.

These tools may make your life easier or harder, but I hope they answer your
questions.

tl;dr:
	You may build your code from any directory, but my convention is to have a
	dedicated build directory. Some people like to have a centralized location
	for installing their libraries (mapped to $MYINSTALL/lib), but this
	example will use a local library folder.

	To build (assuming you are starting from this module's root directory,
	lets say that you have checked your code out to: /direct/phenix+u/beaumim/tutorials/basic_root_class/)

	Run Commands:
		cd build
		../src/autogen.sh --prefix=/direct/phenix+u/beaumim/tutorials/basic_root_class
		make install

	now you should have a library, installed to
	/direct/phenix+u/beaumim/tutorials/basic_root_class/lib which you can then
	load in a root macro, or from root:

	root[0] gSystem->load("/direct/phenix+u/beaumim/tutorials/basic_root_class/lib/libSimpleClass.so");
	root[1] SimpleClass myClassInstance;
	
	Directory Structure
	build  -> where you build the code (run make, make install, etc)
	src    -> source code
	lib    -> where you instal the compiled library
	macros -> optional - root macros where you can call and run your code via
	          interactive (CNT) root sessions

The basic contents of this module:

GNU Autoconf, Autotools, libtoolize:
	Makefile.am
	configure.ac
		These files are used by gnu autotools and libtoolize to preparre to 
		build (i.e. properly link and compile) your code. Makefile.am and
		configure.ac are actually "macros". A macro is a series of commmands which
		are parsed by an interprer and converted into actions - in this case,
		these macros are written in the m4 macro language. The structure of
		Makefile.am and configure.ac are not really discussed here, because they
		are a bit mysterious to me. Suffice to say, that, when your program
		becomes too complex to compile by typing in commands like "g++ main.cpp
		class_1.cpp class_2.cpp -o executable.exe", and requires linking to
		compiled libraries, you need a tool to help do all this for you - like a
		Makefile. If the code becomes too complex to write a makefile, you then
		need to have a macro which is interpreted and makes your makefile for you
		- which in this case is gnu autotools. So, Makefile.am and configure.ac
		are m4 macros which are interpreted and will properly create a makefile.

Custom Scripts:
	autogen.sh
		autogen.sh is a script which runs in order to set up the build
		environment. It sets up the build diretory by linking the neccessary
		autotools and libtoolize scripts and sources. It also sets the default
		library install location to a custom directory when called with
		--prefix=<your desired directory, typically $MYINSTALL> 

	fullClean.sh
		I like have an extra script which will clean up an improperly
		uninstalled build. Typically, one cleans up a build with "make clean"
		but sometimes this fails (in my experiance) so I have a script to
		finish the job.

Source Files:
	SimpleClass.C
		This is the standard C++ class definition, (source file) for the class
		"SimpleClass"
	SimpleClass.h
		This is the standard C++ class declaration (header file) for the class
		"SimpleClass"
	SimpleClassLinkDef.h
		This file is required for SimpleClass to run interactively in ROOT.
		Essentially, it tells root that SimpleClass is a C++ class, and then
		root will use this to create a Dictionary file, which is generated
		automatically when the code is built.
	dontIncludeMe.h
		This header contains whatever constants and template functions that
		you want to use in your classes, but may not want to compile into the
		library itself. It doens't have to be called "dontIncludeMe.h", and
		whether or not a given header is built into the library is dependant
		on setting this option in the Makefile.am
