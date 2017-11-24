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

Today we are going to be using tricks I have learned from an alum of 42, [qsto](https://github.com/qst0/), as well as the [GNU Make Man.](https://www.gnu.org/software/make/manual/html_node/index.html)

Without further ado, lets jump right into it.

## Variables

```Makefile
NAME = App_name
CC = gcc 									# explicit redefinition of an implicit variable
CFLAGS = -Wall -Wextra -Werror				# explicit redefinition of an implicit variable
MAIN = main.c								# explicit variable
FEATURE_1 = f1_submodule1.c f1_submodule2.c # etc...
FEATURE_2 = f2_submodule1.c f2_submodule2.c # etc...
FILES = $(MAIN) $(FEATURE_1) $(FEATURE_2)   # organization of variables for modularity.

SRC = $(addprefix src/, $(FILES))			# file function
OBJ = $(addprefix obj/, $(FILES:.c=.o))		# file function
```

Variables are pretty self explanatory. You define them in a `VARNAME = value` fashion. 

There are three things of importance to note here. Implicit variables, organization, and file name functions. 

### Implicit Variables

Variables in this context, come in two flavors, implicit and explicit.

Explicit variables are like the ones in the example above. These are self-defined variables, or redefinitions of implicit variables. Read more about them [here](https://www.gnu.org/software/make/manual/html_node/Variables-Simplify.html#Variables-Simplify)

Implicit variables are implied. This means these variables are predefined by make itself. If we were to call `$(CC) -c file.c` in a recipe, make would interpret that as `cc -c file.c`. Check out implicit variables [here](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)

There is a third type of variable, known as an Automatic Variable that we will touch on in the recipes section.

---

### Organization

Organization is key to building something modular. Modularity is useful because it allows you to rewrite less code in the long run, and easily convey what your makefile is doing.

**Tip:** Instead of trying to push all of your files under a single `FILES =` definition, break them up into features that serve a specific purpose. This particularly useful if you only want to compile a section of a library you've made for your program when only need to work with that specific set of tools (i.e. during debugging).

### File Name Functions

File name functions take your organization to the next level, allowing you to play around with your file names and cut down extra typing. 

Take this example:

```Makefile
FILES = main.c validation.c rotate.c interact.c
SRC = $(addprefix src/, $(FILES))
OBJ = $(addprefix obj/, $(FILES:.c=.o))
```
This allows us to quickly reference the files in their source folder directly, while also indirectly referencing the files by their name for use with .o files.

Check out more File Name Functions [here](https://www.gnu.org/software/make/manual/html_node/File-Name-Functions.html)
**Note:** `$(FILES:.c=.o)` is pattern substition for files ending in .c. This allows for indirect reference of the same file names, but with a .o extension. See [Text Functions](https://www.gnu.org/software/make/manual/html_node/Text-Functions.html) for more info.

**I'm breaking for lunch. Rules continue when I return :{P**
## Rules

## Recipes
