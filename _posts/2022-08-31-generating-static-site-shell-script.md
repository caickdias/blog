---
title: Generating a static site with template shell script
date: 2022-08-31 
categories: [Shell]
tags: [Shell, Linux]     # TAG names should always be lowercase
---

![Bg](/assets/posts/20220831/bg.png){: width="972" height="589" style="max-width: 100%" .normal}

This is a tutorial to generate a static website using Bourne shell(sh) scripting and terminal arguments. You can find the full project [here](https://github.com/caickdias/shell-script-static-site).

In this project we'll create a simple site to show a list of contacts that you can pass typing the arguments or concatenating a text file.

## Prerequisites
---

This was created using Ubuntu 22.04, Sublime Text 3 and Firefox. You only need to know the very basics of shell commands and HTML/CSS.

You can do everything through the terminal, but Sublime Text will make your life easier. You can find installation instructions for Sublime [here](https://www.sublimetext.com/docs/linux_repositories.html). Firefox will be needed to visualize our .html file.

## Project
---

Our final project will look like this:

![Command](/assets/posts/20220831/command.png){: width="972" height="589" style="max-width: 70%" .normal}
![List View](/assets/posts/20220831/list.png){: width="972" height="589" style="max-width: 70%" .normal}

## Sections

- [Creating our shell file](#creating-our-shell-file)
    - echo and read commands
    - writing in index.html file
- [Creating basic html variable](#creating-basic-html-variable)
    - dynamic site's title
    - creating CSS variable
- [Using terminal arguments](#using-terminal-arguments)
    - dynamic content from arguments
    - iterating over arguments
    - using text file content as arguments

## Creating our shell file
---

Let's start by creating our main file who's responsible for generating our html. Navigate to your directory and do the command below to create main.sh file.

```console
touch main.sh
```

Add '#! /bin/sh' to the first line as our script header.

### Echo and read commands

As we'll make this in a 'user-friendly' way, let's use `echo` to show in the terminal a simple message asking the website's name, then we'll use the command `read` to get the input given through terminal and assign it to our created variable. Then we'll use the `echo` again to confirm everything went fine.

```shell
#! /bin/sh

echo "What is your site name?"
read SITE_NAME
echo Site name is $SITE_NAME
```

We can run our script file by typing `sh main.sh`. You should be in the same directory as our file and you should see the message above and the later echo message when you type your site's name.

### Writing in index.html file

Once our site is going to be an html file, we should write our html content in it. For this we're going to use the `echo` and `>` commands. Let's write our site name variable in the html file that we create with the command: `echo "$HTML" > index.html`

> Use a single greater-than sign `>`, otherwise (`>>`) it won't overwrite, but append the file
{: .prompt-warning}

We can see what we've done so far [here](https://github.com/caickdias/blog-posts/tree/5f642223c62950cd0042e8028e0d0929ffe21a0d/static-site-shell-script).

## Creating basic html variable

### Dynamic site's title

Well, we're not here to create plain text files, it's time to start creating our HTML. Create a variable named HTML and put some basic html information in it. 

Here we'll use the syntax `${VAR}` inside our variable text to use our SITE_NAME that we just read through terminal and use it as our site's name. Save it and run `sh main.sh` again and open our index.html file in the browser. You should some simple text only, but if you open your .html file you'll see it's probably written in HTML. Code for this part [here](https://github.com/caickdias/blog-posts/tree/e42f076e06a173fc8821ceabdc3af6b572805aa8).

```shell
HTML="<!DOCTYPE html>
<html>
	<head>
		<title>${SITE_NAME}</title>
	</head>

	<body>		
		<h1>$SITE_NAME</h1>		
	</body>
</html>"

echo "$HTML" > index.html
```

> Use double quotes to our HTML variable so it will put all the line breaks and tabs correctly.
{: .prompt-warning}

### Creating CSS variable

We can do the same for our CSS. Let's read our background color, apply it to our CSS variable and use our CSS variable inside our HTML variable header/style tag. It's just repeating the same.

```shell
cho What is the background color?
read BG_COLOR

CSS="body { 
	background-color: $BG_COLOR;
}
"

HTML="<!DOCTYPE html>
<html>
	<head>
		<title>${SITE_NAME}</title>
		<style>
			${CSS}
		</style>
	</head>
```
> It's necessary to assign our CSS variable BEFORE our HTML.
{: .prompt-warning}

Full code until this part [here](https://github.com/caickdias/blog-posts/tree/e229b246d59f99337b97b4d2575ff1ea99b049bc).

## Using terminal arguments

### Dynamic content from arguments

Everything we type after in our terminal are arguments. Our command itself is an argument, but we can make use of this by using everything after our command to catch information and use in our HTML aswell. For example:

`sh main.sh firstArg secondArg`

We can concatenate commands too, but in this case we'll send names as arguments so we can use in our code.

In our code, we can get the arguments using the dollar sign `$`. Our first argument after our command will be $1, the second $2 and so on.

```shell
firstName=$1
secondName=$2

HTML="<!DOCTYPE html>
<html>
	<body>		
		<h1>$firstName $secondName</h1>		
	</body>
</html>"

```

So if you type the command `sh main.sh John Ana` you should see 'John Ana' in your browser.

### Iterating over arguments

Notice in the case above that we used only two arguments, but we can use as much as we want. If we don't know how many arguments we're getting, we should be able to loop through all arguments. We can get all arguments with the sign `$*` and loop through them with a `for`.

We will then acumulate in a variable `LIST` with the following approach: `LIST="${LIST} $name"` as using `+=` doesn't work in Bourne Shell. 

```shell
for name in $*
do
	LIST="${LIST} $name"
done

HTML="<!DOCTYPE html>
<html>
	<body>		
		<h1>$LIST</h1>		
	</body>
</html>"
```

Now we can send as many arguments as we want and they will be displayed and put in our HTML file. Code for this part [here](https://github.com/caickdias/blog-posts/tree/da95e0c76b379e52302cc18b59c71fa836d116f0).

### Using text file content as arguments

Sometimes it's not very optimal to type every argument when you do the command, so we'll learn to use the content in a simple .txt file. Let's create a new file with `touch list.txt` and add names to it.

Now we can send our names to our arguments list using the output of `cat` command in our text file. We'll do this with the following command:

`sh main.sh $(cat list.txt)`

We don't have have to do any changes into our code as the command above will just send everything as arguments and will split them by spaces and line breaks.

## Finishing our project

In this simple project I wanted to have two infos for each contact: name and phone. Since if we use spaces to separate them will result in a different argument, we'll use commas and use the command bellow to get each info. I also added some html to better structure them.

```shell
for contact in $* #example: caick,(82)3298-0412
do	
	name=$(echo ${contact} | cut -d',' -f1) #will get caick
	phone=$(echo ${contact} | cut -d',' -f2) #will get (82)3298-0412

	LIST="${LIST}
	<div class='card'>
		<h2>$name</h2>
		<h3>$phone</h3>		
	</div>"
done
```

> Don't use double quotes inside our HTML/CSS string. Remember to use single quotes for class and etc!
{: .prompt-danger}

Now we should just add styles and it's done! We can execute our script passing arguments manually or within the text file. Examples:

`sh main.sh john,9828390 ana,2871289 someone,8278308` 

`sh main.sh $(cat list.txt)`

> Some symbols as `(` will result in error if sending arguments manually, but they work fine inside the text file.
{: .prompt-warning}


![List View](/assets/posts/20220831/list.png){: width="972" height="589" style="max-width: 70%" .normal}

 Full code [here](https://github.com/caickdias/shell-script-static-site).

See you!