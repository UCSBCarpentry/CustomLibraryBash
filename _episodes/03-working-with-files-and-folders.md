---
title: "Working with files and directories"
teaching: 20
exercises: 10
questions:
- "How can I copy, move, and delete files and directories?"
- "How can I read files?"
objectives:
- "Work with files and directories from the command line"
- "Use tab completion to limit typing"
- "Look at all or part of a file using the command line"
- "Moving and copying files"
keypoints:
- "The shell can be used to copy, move, and combine multiple files"
---
## Working with files and folders

As well as navigating directories, we can interact with files on the command line:
we can read them, open them, run them, and even edit them. In fact, there's really
no limit to what we *can* do in the shell, but even experienced shell users still switch to
graphical user interfaces (GUIs) for many tasks, such as editing formatted text
documents (Word or OpenOffice), browsing the web, editing images, etc. But if we
wanted to make the same crop on hundreds of images, say, the pages of a scanned book,
then we could automate that cropping work by using shell commands.

We will try a few basic ways to interact with files. Let's first move into the
`shell-lesson` directory on your desktop.

~~~
$ cd
$ cd Desktop/shell-lesson
$ pwd
~~~
{: .bash}
~~~
/Users/your.username/Desktop/shell-lesson
~~~
{: .output}

Here, we will create a new directory and move into it:

~~~
$ mkdir firstdir
$ cd firstdir
~~~
{: .bash}

Here we used the `mkdir` command (meaning 'make directories') to create a directory
named 'firstdir'. Then we moved into that directory using the `cd` command.

But wait! There's a trick to make things a bit quicker. Let's go up one directory.

~~~
$ cd ..
~~~
{: .bash}

Instead of typing `cd firstdir`, let's try to type `cd f` and then hit the Tab key.
We notice that the shell completes the line to `cd firstdir/`.

> ## Tab for Auto-complete
> Hitting tab at any time within the shell will prompt it to attempt to auto-complete
> the line based on the files or sub-directories in the current directory.
> Where two or more files have the same characters, the auto-complete will only fill up to the
> first point of difference, after which we can add more characters, and
> try using tab again. We would encourage using this method throughout
> today to see how it behaves (as it saves loads of time and effort!).
{: .callout}

### Reading files

If you are in `firstdir`, use `cd ..` to get back to the `shell-lesson` directory.

Here there are three directories containing the data we will work with 
for the workshop.  Move into the `desktrackers` directory.

~~~
$ ls -lh
~~~
{: .bash}
~~~
total 98M
 53M -rw-r--r-- 1 your.username 1049089  53M Apr  6 11:45 Desk_Tracker.csv
 18M -rw-r--r-- 1 your.username 1049089  18M Apr  6 10:02 Desk_Tracker_2016.csv
 12M -rw-r--r-- 1 your.username 1049089  12M Apr  6 10:38 Desk_Tracker_2017.csv
9.4M -rw-r--r-- 1 your.username 1049089 9.4M Apr  6 10:39 Desk_Tracker_2018.csv
6.7M -rw-r--r-- 1 your.username 1049089 6.7M Apr  6 10:41 Desk_Tracker_2019.csv
total 1128
~~~
{: .output}

The files `Desk_Tracker_2016.csv`, and `...2017.csv`, `...2018.csv` 
holds statistics exported from DeskTracker, the system used to track 
traffic at service desks. But we've forgot *which* desk we exported, 
so we try the `cat` command to read the text of the first file:

~~~
$ cat Desk_Tracker_2017.csv
~~~
{: .bash}

The terminal window erupts and the whole book cascades by (it is
printed to your terminal), leaving us with a new prompt and the last 
few lines of the file above this prompt.  

> ## Wrapping and Unwrapping Text 
> You might not be able to tell line from line if your Shell is wrapping 
> the lines of text.  Users can use the command `tput rmam` to disable 
> line wrapping and `tput smam` to enable line wrapping
{: .callout}

Often we just want a quick glimpse of the first or the last part of a file to
get an idea about what the file is about. To let us do that, the Unix shell
provides us with the commands `head` and `tail`.  Let's make sure that our log files start and end at appropriate dates.

~~~
$ head Desk_Tracker_2017.csv
~~~
{: .bash}
~~~
response_set_id,parent_response_set_id,date_time,page,user,branch,desk,library,)
"51530","","2017-01-03 08:02:32","Interdisciplinary Researc","RefBuddy","Interd"
"51531","","2017-01-03 08:08:52","Desk Statistics","RefBuddy","Reference Servic"
"51532","","2017-01-03 08:08:58","Desk Statistics","RefBuddy","Reference Servic"
"51533","","2017-01-03 08:17:26","Desk Statistics","RefBuddy","Reference Servic"
"51534","","2017-01-03 08:57:08","Desk Statistics","RefBuddy","Reference Servic"
"51535","","2017-01-03 08:58:52","Desk Statistics","RefBuddy","Reference Servic"
"51538","","2017-01-03 09:10:46","MILrefdesk","RefBuddy","Map and Imagery Lab","
"51539","","2017-01-03 09:11:07","MILrefdesk","RefBuddy","Map and Imagery Lab","
"51540","","2017-01-03 09:32:00","Desk Statistics","RefBuddy","Reference Servic"
~~~
{: .output}

This provides a view of the first ten lines, or as much of the first ten 
lines as fits the width of your terminal. 

Tail, you might guess `tail Desk_Tracker_2017.csv` 
gets you the last ten lines:

~~~
$ tail Desk_Tracker_2017.csv
~~~
{: .bash}
~~~
"83263","","2017-12-23 11:59:15","Desk Statistics","RefBuddy","Reference Servic"
"83264","","2017-12-23 12:14:54","Desk Statistics","RefBuddy","Reference Servic"
"83265","","2017-12-23 12:28:24","Desk Statistics","RefBuddy","Reference Servic"
"83266","","2017-12-23 12:51:42","Desk Statistics","RefBuddy","Reference Servic"
"83267","","2017-12-23 13:37:52","Desk Statistics","RefBuddy","Reference Servic"
"83268","","2017-12-23 13:44:48","Desk Statistics","RefBuddy","Reference Servic"
"83269","","2017-12-23 14:21:14","Desk Statistics","RefBuddy","Reference Servic"
"83270","","2017-12-23 14:41:58","Desk Statistics","RefBuddy","Reference Servic"
"83271","","2017-12-23 14:42:15","Desk Statistics","RefBuddy","Reference Servic"
"83272","","2017-12-23 14:42:35","Desk Statistics","RefBuddy","Reference Servic"
~~~
{: .output}

If ten lines is not enough (or too much), we would check `man head`
to see if there exists an option to specify the number of lines to get
(there is: `head -n 20` will print 20 lines).

Another way to navigate files is to view the contents one screen at a time.
Type `less Desk_Tracker_2017.csv` to see the first screen, `spacebar` to see the
next screen and so on, then `q` to quit (return to the command prompt).

~~~
$ less Desk_Tracker_2017.csv
~~~
{: .bash}

Like many other shell commands, the commands `cat`, `head`, `tail` and `less`
can take any number of arguments (they can work with any number of files).
We will see how we can get the first lines of several files at once.
To save some typing, we introduce a very useful trick first.

> ## Re-using commands
> On a blank command prompt, hit the up arrow key and notice that the previous
> command you typed appears before your cursor. We can continue pressing the
> up arrow to cycle through your previous commands. The down arrow cycles back
> toward your most recent command. This is another important labour-saving
> function and something we'll use a lot.
{: .callout}

Hit the up arrow until you get to the `head Desk_Tracker_2017.csv` 
command. Add a space and then `Desk_Tracker_2016.csv` to produce the 
following command:

~~~
$ head Desk_Tracker_2017.csv Desk_Tracker_2016.csv 
~~~
{: .bash}
~~~
==> Desk_Tracker_2017.csv <==
response_set_id,parent_response_set_id,date_time,page,user,branch,desk,library,)
"51530","","2017-01-03 08:02:32","Interdisciplinary Researc","RefBuddy","Interd"
"51531","","2017-01-03 08:08:52","Desk Statistics","RefBuddy","Reference Servic"
"51532","","2017-01-03 08:08:58","Desk Statistics","RefBuddy","Reference Servic"
"51533","","2017-01-03 08:17:26","Desk Statistics","RefBuddy","Reference Servic"
"51534","","2017-01-03 08:57:08","Desk Statistics","RefBuddy","Reference Servic"
"51535","","2017-01-03 08:58:52","Desk Statistics","RefBuddy","Reference Servic"
"51538","","2017-01-03 09:10:46","MILrefdesk","RefBuddy","Map and Imagery Lab","
"51539","","2017-01-03 09:11:07","MILrefdesk","RefBuddy","Map and Imagery Lab","
"51540","","2017-01-03 09:32:00","Desk Statistics","RefBuddy","Reference Servic"
(base)

==> Desk_Tracker_2016.csv <==
response_set_id,parent_response_set_id,date_time,page,user,branch,desk,library,)
"9898","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Service"
"9899","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Service"
"9900","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Service"
"11582","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
"11583","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
"11584","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
"11585","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
"11586","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
"11587","","2016-01-04 08:00:00","Desk Statistics","RefBuddy","Reference Servic"
~~~
{: .output}

All good so far, but if we had *lots* of years, it would be tedious to enter
all the filenames. Luckily the shell supports wildcards! The `?` (matches exactly
one character) and `*` (matches zero or more characters) are probably familiar
from library search systems. We can use the `*` wildcard to write the above `head`
command in a more compact way:

~~~
$ head *.csv
~~~
{: .bash}

> ## Wildcards
> Wildcards are a feature of the shell and will therefore work with *any* command.
> The shell will expand wildcards to a list of files and/or directories before
> the command is executed, and the command will never see the wildcards.
> As an exception, if a wildcard expression does not match any file, Bash
> will pass the expression as a parameter to the command as it is. For example
> typing `ls *.pdf` results in an error message that there is no file called *.pdf.
{: .callout}


<!-- Timing: Spent 75 minutes to get here -->

### Moving, Copying and Deleting files

We may also want to change the file name to something more descriptive.
We can **move** it to a new name by using the `mv` or move command,
giving it the old name as the first argument and the new name as the second
argument:

~~~
$ mv Desk_Tracker_2016.csv CollabStats_2016.csv
~~~
{: .bash}

This is equivalent to the 'rename file' function.

Afterwards, when we perform a `ls` command, we will see that it is now called `CollabStats_2016.csv`:

~~~
$ ls
~~~
{: .bash}
~~~
CollabStats_2016.csv	Desk_Tracker_2017.csv	Desk_Tracker_2018.csv	Desk_Tracker_2019.csv
~~~
{: .output}

> ## Copying a file
>
> Instead of *moving* a file, you might want to *copy* a file (make a duplicate),
> for instance to make a backup before modifying a file.
> Just like the `mv` command, the `cp` command takes two arguments: the old name
> and the new name. How would you make a copy of the file `Desk_Tracker_2018.csv` called
> `Desk_Tracker_2018_backup.csv`? Try it!
>
> > ## Answer
> > ~~~
> > cp Desk_Tracker_2018.csv DeskTracker_2018_backup.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Renaming a directory
>
> Renaming a directory works in the same way as renaming a file. Try using the
> `mv` command to rename the `firstdir` directory to `backup`.
>
> > ## Answer
> > ~~~
> > mv firstdir backup
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Moving a file into a directory
>
> If the last argument you give to the `mv` command is a directory, not a file,
> the file given in the first argument will be moved to that directory. Try
> using the `mv` command to move the file `DeskTracker_2018_backup.csv` into the
> `backup` folder.
>
> > ## Answer
> > ~~~
> > mv DeskTracker_2018_backup.csv backup
> > ~~~
> > {: .bash}
> >
> > This would also work:
> >
> > ~~~
> > mv Desk_Tracker_2018.csv backup/DeskTracker_2018_backup.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

<!-- > ## The wildcards and regular expressions -->

<!--  The `?` wildcard matches one character. The `*` wildcard matches 
> zero or more characters. If you attended the lesson on regular 
> expressions, do you remember how you would express that as regular 
> expressions?

(Regular expressions are not a feature of the shell, but some commands support
them. We'll get back to that.)
## Answer
> > * The `?` wildcard matches the regular expression `.` (a dot)
> > * The `*` wildcard matches the regular expression `.*`
> {: .solution}
{: .challenge} -->

> ## Using `history`
> Use the `history` command to see a list of all the commands you've entered during the 
> current session. You can also use <kbd>Ctrl</kbd> + <kbd>r</kbd> to do a reverse lookup. Hit <kbd>Ctrl</kbd> + <kbd>r</kbd>, 
> then start typing any part of the command you're looking for. The past command will 
> autocomplete. Hit `enter` to run the command again, or press the arrow keys to start 
> editing the command. If multiple past commands contain the text you input, you can 
> <kbd>Ctrl</kbd> + <kbd>r</kbd> repeatedly to cycle through them. If you can't find what you're looking for 
> in the reverse lookup, use <kbd>Ctrl</kbd> + <kbd>c</kbd> to return to the prompt. If you want to save 
> your history, maybe to extract some commands from which to build a script later on, you 
> can do that with `history > history.txt`. This will output all history to a text file 
> called `history.txt` that you can later edit. To recall a command from history, enter 
> `history`. Note the command number, e.g. 2045. Recall the command by entering 
> `!2045`. This will execute the command.
{: .callout}

> ## Using the `echo` command
> The `echo` command simply prints out a text you specify. Try it out: `echo "Library Carpentry is awesome!"`.
> Interesting, isn't it?
>
> You can also specify a variable, for instance `NAME=` followed by your name.
> Then type `echo "$NAME is a fantastic library carpentry student"`. What happens?
>
> You can combine both text and normal shell commands using `echo`, for example the
> `pwd` command you have learned earlier today. You do this by enclosing a shell
> command in `$(` and `)`, for instance `$(pwd)`. Now, try out the following:
> `echo "Finally, it is nice and sunny on" $(date)`.
> Note that the output of the `date` command is printed together with the text
> you specified. You can try the same with some of the other commands you have learned so far.
>
> **Why do you think the echo command is actually quite important in the shell environment?**
>
> > ## Answer
> > You may think there is not much value in such a basic command like `echo`. However, from the moment you
> > start writing automated shell scripts, it becomes very useful. For instance, you often need
> > to output text to the screen, such as the current status of a script.
> >
> > Moreover, you just used a shell variable for the first time, which can be used to temporarily store information,
> > that you can reuse later on. It will give many opportunities from the moment you start writing automated scripts.
> {: .solution}
{: .challenge}

Finally, onto deleting. We won't use it now, but if you do want to delete a file,
for whatever reason, the command is `rm`, or remove.

Using wildcards, we can even delete lots of files. And adding the `-r` flag we
can delete folders with all their content.

**Unlike deleting from within our graphical user interface, there is *no* warning,
*no* recycling bin from which you can get the files back and no other undo options!**
For that reason, please be very careful with `rm` and extremely careful with `rm -r`.
