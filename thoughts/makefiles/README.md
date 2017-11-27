# What I Learned from Other Developers Makefiles

```
        :::      ::::::::    
      :+:      :+:    :+:    
    +:+ +:+         +:+      
  +#+  +:+       +#+         
+#+#+#+#+#+   +#+            
     #+#    #+#              
    ###   ########.us   Brad Vautour     
```    

### Introduction

When I first started coding in C at 42, Makefiles were a foreign concept to me. In the earliest days my brain felt like a sieve trying to hold onto an onslaught of information that was being hurled at me in such a short amount of time. Most core concepts were solid and stuck with me, but the finer liquid details managed to drip through all the same. The following sections will serve not only as a reference for my own personal use, but as an attempt to unpack what I have learned to help others that may be struggling the same way I did.

## Comprehension steps to success:

I think it is important to note that the key to my understanding in most facets of programming comes from the key concept of `code comprehension`. I think its very important to try to read other peoples work frequently and try to break down what they have done. 

* read each line critically. 
* cross-reference code with documentation. 
* pour over the usual forums.
* ask your peers for help when you are stuck.
* do not be afraid to admit that when you do no know something.
* never put something in your own work that you do not fully understand.

Today we are going to be using tricks I have learned from an alum of 42, [qst0](https://github.com/qst0/), as well as the [GNU Make Man.](https://www.gnu.org/software/make/manual/html_node/index.html)

Without further ado, lets jump right into it.

## Variables

```Makefile
NAME = App_name
CC = gcc	# explicit redefinition of an implicit variable
CFLAGS = -Wall -Wextra -Werror	# explicit redefinition of an implicit variable
MAIN = main.c	# explicit variable
FEATURE_1 = f1_submodule1.c f1_submodule2.c	# etc...
FEATURE_2 = f2_submodule1.c f2_submodule2.c	# etc...
FILES = $(MAIN) $(FEATURE_1) $(FEATURE_2)	# organization of variables for modularity.

SRC = $(addprefix src/, $(FILES))	# file function
OBJ = $(addprefix obj/, $(FILES:.c=.o))	# file function
```

Variables are pretty self explanatory. You define them in a `VARNAME = value` fashion. 

There are three things of importance to note here. Implicit variables, organization, and file name functions. 

#### Implicit Variables

Variables in this context, come in two flavors, implicit and explicit.

Explicit variables are like the ones in the example above. These are self-defined variables, or redefinitions of implicit variables. Read more about them [here](https://www.gnu.org/software/make/manual/html_node/Variables-Simplify.html#Variables-Simplify)

Implicit variables are implied. This means these variables are predefined by make itself. If we were to call `$(CC) -c file.c` in a recipe, make would interpret that as `cc -c file.c` if there was no explicit redefinition of which C compiler I wanted to use. Check out more about implicit variables [here](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)

**Tip:** run `make -p` to see a list of implicits running on your version of make.

There is a third type of variable, known as an Automatic Variable that we will touch on in the recipes section.

---

#### Organization

Organization is key to building something modular. Modularity is useful because it allows you to rewrite less code in the long run, and easily convey what your makefile is doing.

**Tip:** Instead of trying to push all of your files under a single `FILES =` definition, break them up into features that serve a specific purpose. This particularly useful if you only want to compile a section of a library you've made for your program when only need to work with that specific set of tools (i.e. during debugging). This is also very useful when compiling local libraries.

---

#### File Name Functions

File name functions take your organization to the next level, allowing you to play around with your file names and cut down extra typing. 

Take this example:

```Makefile
FILES = main.c validation.c rotate.c interact.c
SRC = $(addprefix src/, $(FILES))
OBJ = $(addprefix obj/, $(FILES:.c=.o))
```
This allows us to quickly reference the files in their source folder directly, while also indirectly referencing the files by their name for use with .o files.

Check out more File Name Functions [here](https://www.gnu.org/software/make/manual/html_node/File-Name-Functions.html).

**Note:** `$(FILES:.c=.o)` is pattern substition for files ending in .c. This allows for indirect reference of the same file names, but with a .o extension. See [Text Functions](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html) for more info.

## Rules

Rules and recipes walk hand in hand. A rule is comprised of three main parts.  A file target or action, a list of prerequisite files or rules to watch for changes, and the recipe to call when the target is out of sync with it's prerequisites.

Here is the syntax:

```Makefile
target or action : prereqs
	recipe...
```

#### Prerequisite Types

There are two types of prereqs. Normal and Order only. 

Normal prereqs work as you might expect. They are linked dependencies that execute in order when they are out of date, calling the recipe of the rule.

Order only prereqs work a little bit differently. They invoke in order, but without forcing an update via dependency like a normal prereq. This is especially useful when calling for the creation of a directory that some files should exist in (for example an objects directory) but does not yet exist. This type of prereq is declared after normal prereqs and are delimited with a `|` pipe. 

`target or action : normal prereq | order only prereq`

Let's look at a basic example of both kinds of prereqs inspired by similar rules I picked up from [qst0](https://github.com/qst0/).

```Makefile
	# this assumes we're talking about the same variables we declared in the above section.
obj:
	mkdir obj

obj/%.o: src/%.c | obj	# this rule states to watch for changes in c files and to ignore the obj directories timestamp changes, unless the timestamp doesnt exist at all. 
	@echo "Building $@"
	# your code to compile .o files in the object directory would go here.
``` 

Theres a few weird symbols that appear in the above example which we'll go more in depth with in the next section. 
I will still run down there meaning here briefly 

the `%` is a wildcard similar to the asterisk glob pattern in shell.

the `@` before the echo silences the line from being put to the stdout.

`$@` is an Automatic Variable

And thats it for rules for now! Onto the real meat and potatoes. Recipes!

## Recipes

Recipes are a pretty big topic to cover. Heres some of the stuff I've found to be the most important.

#### Options

These are the options I most frequently compile with and I consider to be an important part of any makefile. We'll see these options put into practice in the [Putting it All Together](./#piat) section below.

C Options:

**-c:** compile without linking. ultimately used to convert source files to .o

**-o:** outfile.

**-g:** debugging info for use with lldb, gdb, etc.

**-llibrary:** will create a .a file replacing the preceding word after `-l` with lib<name>.a. ex: `-lft = libft.a`

**-Ldir:** directories to search for `-llibrary`

**-Idir:** directory to search for includes.

**-Wall:** enable all warnings about constructions. This will often catch little mistakes in your code before you do.

**-Wextra:** adds additional warnings that are not covered by `-Wall`.

**-Werror:** converts all warnings to errors.

--- 

Make Options:

**-C:** change directory. usually used like `$(MAKE) -C directoryname/ rule`

#### The $(MAKE) Variable

Often used recursively, the $(MAKE) variable is called in conjunction with the -C flag to target a directory that has a makefile inside. These make calls are often referred to as submakes.


## Putting it all Together

I'm currently working on cleaning up my makefiles to reflect the advice i've given here. Once I'm done with that, I'll post a link to a current full example of my makefile!
Thanks for reading! 
