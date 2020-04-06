
---
title: "Counting and mining with the shell"
teaching: 60
exercises: 30
questions:
- "How can I count data?"
- "How can I find data within files?"
- "How can I combine existing commands to do new things?"
objectives:
- "Demonstrate counting lines, words, and characters with the shell command wc and appropriate flags"
- "Use strings to mine files and extract matched lines with the shell"
- "Create complex single line commands by combining shell commands and regular expressions to mine files"
- "Redirect a command's output to a file."
- "Process a file instead of keyboard input using redirection."
- "Construct command pipelines with two or more stages."
- "Explain Unix's 'small pieces, loosely joined' philosophy."
keypoints:
- "The shell can be used to count elements of documents"
- "The shell can be used to search for patterns within files"
- "Commands can be used to count and mine any number of files"
- "Commands and flags can be combined to build complex queries specific to your work"

---
##  Counting and mining data

Now that you know how to navigate the shell, we will move onto
learning how to count and mine data using a few of the standard shell commands.
While these commands are unlikely to revolutionise your work by themselves,
they're very versatile and will add to your foundation for working in the shell
and for learning to code. The commands also replicate the sorts of uses library users might make of library data.

## Counting and sorting

We will begin by counting the contents of files using the Unix shell.
We can use the Unix shell to quickly generate counts from across files,
something that is tricky to achieve using the graphical user interfaces of standard office suites.

Let's start by navigating to the directory that contains our data using the
`cd` command:

~~~
$ cd shell-lesson
~~~
{: .bash}

Remember, if at any time you are not sure where you are in your directory structure,
use the `pwd` command to find out:

~~~
$ pwd
~~~
{: .bash}
~~~
/Users/your.username/Desktop/shell-lesson/data
~~~
{: .output}

And let's just check what files are in the directory and how large they
are with `ls -lhS`:

~~~
$ ls -lhs
~~~
{: .bash}
~~~
 total 6.6M
384K -rw-r--r-- 1 your.username 1049089 384K Mar 12 16:25 000003160_01_text.json
584K -rw-r--r-- 1 your.username 583K Mar 12 16:25 33504-0.txt
600K -rw-r--r-- 1 your.username 598K Mar 12 16:25 829-0.txt
   0 drwxr-xr-x 1 your.username 1049089    0 Mar 18 13:23 airphotodata/
4.0K drwxr-xr-x 1 your.username 1049089    0 Mar 18 13:23 desktrackers/
 20K -rw-r--r-- 1 your.username 1049089  19K Mar 12 16:25 diary.html
1.1M -rw-r--r-- 1 your.username 1049089 1.1M Mar 12 16:25 pg514.txt
4.0M -rw-r--r-- 1 your.username 1049089 4.0M Mar 16 15:40 shell-lesson.zip
   0 drwxr-xr-x 1 your.username 1049089    0 Mar 18 13:35 waitz/
~~~
{: .output}

In this episode we'll focus on the datasets in the desktracker  
directory, that contains four .csv Desktracker files.

> ## CSV and TSV Files
> CSV (Comma-separated values) is a common plain text format for storing tabular
> data, where each record occupies one line and the values are separated by commas.
> TSV (Tab-separated values) is just the same except that values are separated by
> tabs rather than commas. Confusingly, CSV is sometimes used to refer to both CSV,
> TSV and variations of them. The simplicity of the formats make them great for
> exchange and archival. They are not bound to a specific program (unlike Excel
> files, say, there is no `CSV` program, just lots and lots of programs that
> support the format, including Excel by the way.), and you wouldn't have any
> problems opening a 40 year old file today if you came across one.
{: .callout}
<!-- hm, reminds me of MARC -->

First, let's have a look at the the 2016 file, using the tools we 
learned in
[Reading files]({{ page.root }}/03-working-with-files-and-folders/#reading-files):

~~~
$ cat Desk_Tracker.csv
~~~
{: .bash}

Like `829-0.txt` before, the whole dataset cascades by and can't really make any
sense of that amount of text. To cancel this on-going con`cat`enation, or indeed any
process in the Unix shell, press <kbd>Ctrl</kbd>+<kbd>C</kbd>.

In most data files a quick glimpse of the first few lines already tells us a lot
about the structure of the dataset, for example the table/column headers:

~~~
$ head -n 3 Desk_Tracker.csv
~~~
{: .bash}
~~~
response_set_id,parent_response_set_id,date_time,page,user,branch,desk,library,Contact 
Type,Contact Type (text),Question Type,Question Type (text),
Reference   Research Type,Reference   Research Type (text),Reference   
Research Type 2,Reference   Research Type 2 (text),Time Spent in 
minutes,Comment,Reference   Research Type 3,Reference   Research Type 3 (text),Reference   
Research Type 4,Reference   Research Type 4 (text)
"43050","","2016-10-10 14:27:52","Interdisciplinary 
Researc","RefBuddy","Interdisciplinary Research Collaboratory",
"Interdisciplinary Research Collaboratory Desk","University of 
California, Santa Barbara","In Person","","Reference / Research 
Assistance","","Content","","Referred to Subject Librarian","","10","Crimson Hexagon 
query re: 100 Twitter pickup for Korean keywords, 0 for Facebook. 
Referred to Shari Laster.","","","","","43106","","2016-10-10 
15:55:16","Interdisciplinary Researc","RefBuddy","Interdisciplinary Research Collaboratory",
"Interdisciplinary Research Collaboratory Desk","University of 
California, Santa Barbara","In Person","","Reference / Research 
Assistance","","Content","","","","10","Crimson Hexagon odd results.","","","",""
~~~
{: .output}

In the header, we can see the common metadata fields of Desktracker: 
`Question Type`, `Research Type`, `Library.

Next, let's learn about a basic data analysis tool:
`wc` is the "word count" command: it counts the number of lines, words, and bytes.
Since we love the wildcard operator, let's run the command
`wc *.csv` to get counts for all the `.csv` files in the Desktrackers 
directory.
(it takes a little time to complete):

~~~~
$ wc *.csv  
~~~~
{: .bash}
~~~
   122995   2171893  54797588 Desk_Tracker.csv
    43842    757941  18654589 Desk_Tracker_2016.csv
    31456    565013  12271344 Desk_Tracker_2017.csv
    27274    491149   9833562 Desk_Tracker_2018.csv
    20426    358164   6947675 Desk_Tracker_2019.csv
   245993   4344160 102504758 total
~~~
{: .output}

The first three columns contains the number of lines, words and bytes.

If we only have a handful of files to compare, it might be faster or more convenient
to just check with Microsoft Excel, OpenRefine or your favourite text editor, but
when we have tens, hundreds or thousands of documents, the Unix shell has a clear
speed advantage. The real power of the shell comes from being able to combine commands
and automate tasks, though. We will touch upon this slightly.

For now, we'll see how we can build a simple pipeline to find the shortest file
in terms of number of lines. We start by adding the `-l` flag (this looks 
confusing in the shell, but this is a lower-case letter L, not the number one) to get 
only the number of lines, not the number of words and bytes:

~~~~
$ wc -l *.csv
~~~~
{: .bash}
~~~
   122995 Desk_Tracker.csv
    43842 Desk_Tracker_2016.csv
    31456 Desk_Tracker_2017.csv
    27274 Desk_Tracker_2018.csv
    20426 Desk_Tracker_2019.csv
   245993 total
~~~
{: .output}

The `wc` command itself doesn't have a flag to sort the output, but as we'll
see, we can combine three different shell commands to get what we want.

First, we have the `wc -l *.csv` command. We will save the output from 
this command in a new file. To do that, we *redirect* the output from the 
command to a file using the ‘greater than’ sign (>), like so:

~~~
$ wc -l *.csv > lengths.txt
~~~
{: .bash}

There's no output now since the output went into the file `lengths.txt`, but
we can check that the output indeed ended up in the file using `cat` or `less`
(or Notepad or any text editor).

~~~~
$ cat lengths.txt
~~~~
{: .bash}
~~~
   122995 Desk_Tracker.csv
    43842 Desk_Tracker_2016.csv
    31456 Desk_Tracker_2017.csv
    27274 Desk_Tracker_2018.csv
    20426 Desk_Tracker_2019.csv
   245993 total
~~~
{: .output}

Next, there is the `sort` command. We'll use the `-n` flag to specify that we
want numerical sorting, not lexical sorting, we output the results into
yet another file, and we use `cat` to check the results:

~~~~
$ sort -n lengths.txt > sorted-lengths.txt
$ cat sorted-lengths.txt
~~~~
{: .bash}
~~~
    20426 Desk_Tracker_2019.csv
    27274 Desk_Tracker_2018.csv
    31456 Desk_Tracker_2017.csv
    43842 Desk_Tracker_2016.csv
   122995 Desk_Tracker.csv
~~~
{: .output}

Finally we have our old friend `head`, that we can use to get the first 
line of the `sorted-lengths.txt`:
(Earlier, we had the issue of is this a lower case L or a number one. 
Here, its the number 1 because the '-n' is for sorting lines, and we want the number)
~~~~
$ head -n 1 sorted-lengths.txt
~~~~
{: .bash}
~~~
   20426 Desk_Tracker_2019.csv
~~~
{: .output}

But we're really just interested in the end result, not the intermediate
results now stored in `lengths.txt` and `sorted-lengths.txt`. What if we could
send the results from the first command (`wc -l *.csv`) directly to the 
next
command (`sort -n`) and then the output from that command to `head -n 1`?
Luckily we can, using a concept called pipes. On the command line, you make a
pipe with the vertical bar character `|`. Let's try with one pipe first:

~~~~
$ wc -l *.csv | sort -n
~~~~
{: .bash}
~~~
    20426 Desk_Tracker_2019.csv
    27274 Desk_Tracker_2018.csv
    31456 Desk_Tracker_2017.csv
    43842 Desk_Tracker_2016.csv
   122995 Desk_Tracker.csv
   245993 total
~~~
{: .output}

Notice that this is exactly the same output that ended up in our `sorted-lengths.txt`
earlier. Let's add another pipe:

~~~~
$ wc -l *.csv | sort -n | head -n 1
~~~~
{: .bash}
~~~
   20426 Desk_Tracker_2019.csv
~~~
{: .output}

It can take some time to fully grasp pipes and use them efficiently, but it's a
very powerful concept that you will find not only in the shell, but also in most
programming languages.

![Redirects and Pipes](../fig/redirects-and-pipes.png)

> ## Pipes and Filters
> This simple idea is why Unix has been so successful. Instead of creating enormous
> programs that try to do many different things, Unix programmers focus on creating
> lots of simple tools that each do one job well, and that work well with each other.
> This programming model is called “pipes and filters”. We’ve already seen pipes; a
> filter is a program like `wc` or `sort` that transforms a stream of input into a
> stream of output. Almost all of the standard Unix tools can work this way: unless
> told to do otherwise, they read from standard input, do something with what they’ve
> read, and write to standard output.
>
> The key is that any program that reads lines of text from standard input and writes
> lines of text to standard output can be combined with every other program that
> behaves this way as well. You can and should write your programs this way so that
> you and other people can put those programs into pipes to multiply their power.
{: .callout}
<!-- Copied from https://swcarpentry.github.io/shell-novice/04-pipefilter/ -->

> ## Adding another pipe
> We have our `wc -l *.csv | sort -n | head -n 1` pipeline. What would happen
> if you piped this into `cat`? Try it!
>
> > ## Solution
> > The `cat` command just outputs whatever it gets as input, so you get exactly
> > the same output from
> >
> > ~~~
> > $ wc -l *.csv | sort -n | head -n 1
> > ~~~
> > {: .bash}
> >
> > and
> >
> > ~~~
> > $ wc -l *.csv | sort -n | head -n 1 | cat
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Count, sort and print (faded example)
>To count the total lines in every `csv` file, sort the results and then print the first line of the file we use the following:
>
>~~~
>wc -l *.csv | sort -n | head -n 1
>~~~
>{: .bash}
>
>
>Now let's change the scenario. We want to know the 10 files that contain _the most_ words. Fill in the blanks below to count the 
>words for each file, put them into order, and then make an output of the 10 files with the most words (Hint: The sort command sorts 
>in ascending order by default).
>
>~~~
>__ -w *.csv | sort __ | ____
>~~~
>{: .bash}
>
> > ## Solution
> >
> > Here we use the `wc` command with the `-w` (word) flag on all `csv` files, `sort` them and then output the last 11 lines (10 
> > files and the total) using the `tail` command.
> >~~~
> > wc -w *.csv | sort -n | tail -n 1
> >~~~
> {: .solution}
>{: .bash}
{: .challenge}


> ## Counting number of files, part I
> Let's make a different pipeline. You want to find out how many files and
> directories there are in the current directory. Try to see if you can pipe
> the output from `ls` into `wc` to find the answer, or something close to the
> answer.
>
> > ## Solution
> > You get close with
> >
> > ~~~
> > $ ls -l | wc -l
> > ~~~~
> > {: .bash}
> >
> > but the count will be one too high, since the "total" line from `ls`
> > is included in the count. We'll get back to a way to fix that later
> > when we've learned about the `grep` command.
> {: .solution}
{: .challenge}

> ## Writing to files
> The `date` command outputs the current date and time. Can you write the
> current date and time to a new file called `logfile.txt`? Then check
> the contents of the file.
>
> > ## Solution
> > ~~~
> > $ date > logfile.txt
> > $ cat logfile.txt
> > ~~~~
> > {: .bash}
> > To check the contents, you could also use `less` or many other commands.
> >
> > Beware that `>` will happily overwrite an existing file without warning you,
> > so please be careful.
> {: .solution}
{: .challenge}

> ## Appending to a file
> While `>` writes to a file, `>>` appends something to a file. Try to append the
> current date and time to the file `logfile.txt`?
>
> > ## Solution
> > ~~~
> > $ date >> logfile.txt
> > $ cat logfile.txt
> > ~~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Counting the number of words
>
> Check the manual for the `wc` command (either using `man wc` or `wc --help`)
> to see if you can find out what flag to use to print out the number of words
> (but not the number of lines and bytes). Try it with the `.csv` files.
>
> If you have time, you can also try to sort the results by piping it to `sort`.
> And/or explore the other flags of `wc`.
>
> > ## Solution
> >
> > From `man wc`, you will see that there is a `-w` flag to print the number of
> > words:
> >
> > ~~~
> >      -w      The number of words in each input file is written to the standard
> >              output.
> > ~~~
> > {: .output}
> >
> > So to print the word counts of the `.csv` files:
> >
> > ~~~
> > $ wc -w *.csv
> > ~~~
> > {: .bash}
> > ~~~
> >  2171893 Desk_Tracker.csv
> >   757941 Desk_Tracker_2016.csv
> >   565013 Desk_Tracker_2017.csv
> >   491149 Desk_Tracker_2018.csv
> >   358164 Desk_Tracker_2019.csv
> >  4344160 total
> > ~~~
> > {: .output}
> >
> > And to sort the lines numerically:
> >
> > ~~~
> > $ wc -w *.csv | sort -n
> > ~~~
> > {: .bash}
> > ~~~
> >   358164 Desk_Tracker_2019.csv
> >   491149 Desk_Tracker_2018.csv
> >   565013 Desk_Tracker_2017.csv
> >   757941 Desk_Tracker_2016.csv
> >  2171893 Desk_Tracker.csv
> >  4344160 total
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

## Mining or searching

Searching for something in one or more files is something we'll often need to do,
so let's introduce a command for doing that: `grep` (short for **global regular
expression print**). As the name suggests, it supports regular expressions and
is therefore only limited by your imagination, the shape of your data, and - when
working with thousands or millions of files - the processing power at your disposal.

To begin using `grep`, first navigate to the `shell-lesson` directory if not already
there. Then create a new directory "results":

~~~
$ mkdir results
~~~
{: .bash}


Now let's try our first search:

~~~
$ grep Spatial *.csv
~~~
{: .bash}

Remember that the shell will expand `*.csv` to a list of all the `.csv` 
files in the directory. `grep` will then search these for instances of 
the string "irc" and
print the matching lines.

> ## Strings
> A string is a sequence of characters, or "a piece of text".
{: .callout}

Press the up arrow once in order to cycle back to your most recent action.
Amend `grep Spatial *.csv` to `grep -c Spatial *.csv` and hit enter.

~~~
$ grep -c Spatial *.csv
~~~
{: .bash}
~~~
Desk_Tracker.csv:102511
Desk_Tracker_2016.csv:37951
Desk_Tracker_2017.csv:26290
Desk_Tracker_2018.csv:23261
Desk_Tracker_2019.csv:15012
~~~
{: .output}

The shell now prints the number of times the string "Spatial" appeared 
in each file.If you look at the output from the previous command, this tends to 
refer to the date field when counts were made on each floor.

We will try another search:

~~~
$ grep -c Refer *.csv
~~~
{: .bash}
~~~
Desk_Tracker.csv:102511
Desk_Tracker_2016.csv:37951
Desk_Tracker_2017.csv:26290
Desk_Tracker_2018.csv:23261
Desk_Tracker_2019.csv:15012
~~~
{: .output}

We got back the counts of the instances of the string `In Person` within 
the files. Now, amend the above command to the below and observe how the 
output of each is different:

~~~
$ grep -ci in refer *.csv
~~~
{: .bash}
~~~
Desk_Tracker.csv:102543
Desk_Tracker_2016.csv:37956
Desk_Tracker_2017.csv:26299
Desk_Tracker_2018.csv:23273
Desk_Tracker_2019.csv:15018
~~~
{: .output}

This repeats the query, but prints a case
insensitive count (including instances of both `in person` and `In Person` and other variants).
Note how the count has increased for the entries
As before, cycling back and adding `> results/`, followed by a filename (ideally in .txt format), will save the 
results to a data file.

So far we have counted strings in files and printed to the shell or to
file those counts. But the real power of `grep` comes in that you can
also use it to create subsets of tabulated data (or indeed any data)
from one or multiple files.  

~~~
$ grep -i Refer *.csv
~~~
{: .bash}

This script looks in the defined files and prints any lines containing `spatial`
(without regard to case) to the shell. We let the shell add today's date to the
filename (say this was a daily occurrence and we wanted to keep track of dates):

~~~
$ grep -i spatial *.csv > results/$(date -I)_i-refer.csv
~~~
{: .bash}

>## Invalid Date --I command?
> If you are using a Windows or Linux device, (date -I) automatically
>added a date prefix to your subsetted data. However, in Mac operating systems, there is 
>no automatic date function.In order to add the date as a prefix, we must type in the 
>format we want ourselves. Instead of the --I flag, users must type in '(date 
>"+%Y-%m-%d")' when trying to put the YYYY-MM-DD the same way as linux or windows users. Using 'date --help' will show the flags. Some 
>grep commands are different between operating between operating systems and --I is one 
>of them on Mac OSX. 
{: .callout}


This saves the subsetted data to a new file.

However, if we look at this file, it contains every instance of the
string 'spatial' including as a single word and as part of other words
 such as 'spatially'. This perhaps isn't as useful as we thought...
Thankfully, the `-w` flag instructs `grep` to look for whole words only,
giving us greater precision in our search.

~~~
$ grep -iw Refer *.csv > results/$(date -I)_iw-refer.csv
~~~
{: .bash}

This script looks in both of the defined files and
exports any lines containing the whole word `spatial` (without regard to case)
to the specified `.csv` file.

We can show the difference between the files we created.

~~~
$ wc -l results/*.csv
~~~
{: .bash}
~~~
  205089 i-spatial.csv
      22 iw-spatial.csv
  205111 total
~~~
{: .output} 


Finally, we'll use the **regular expression syntax** covered earlier to search for similar words.

> ## Basic and extended regular expressions
> There is unfortunately both ["basic" and "extended" regular expressions](https://www.gnu.org/software/grep/manual/html_node/Basic-vs-Extended.html).
> This is a common cause of confusion, since most tutorials, including ours, teach
> extended regular expression, but `grep` uses basic by default.
> Unless you want to remember the details, make your life easy by always using
> extended regular expressions (`-E` flag) when doing something more complex
> than searching for a plain string.
{: .callout}

The regular expression 'Cont[ae][cn]t' will match "Content", "Contact", 
but also "Contect" and "Contant".
It's generally a good idea to enclose the expression in single quotation marks, since
that ensures the shell sends it directly to grep without any processing (such as trying to
expand the wildcard operator *).

~~~
$ grep -iwE 'Cont[ae][cn]t' *.csv
~~~
{: .bash}

The shell will print out each matching line.

We include the `-o` flag to print only the matching part of the lines e.g.
(handy for isolating/checking results):

~~~
$ grep -iwEo 'Cont[ae][cn]t' *.csv
~~~
{: .bash}

> ## Invalid option -- o?
> If you get an error message "invalid option -- o" when running the above command, it means you use a version of
> `grep` that doesn't support the `-o` flag. This is for instance the case with the version of `grep` that comes with
> Git Bash on Windows. Since the flag is not crucial to this lesson, please just relax and ignore the problem. If you
> really needed the flag, however, you could have installed another version of `grep`. The situation for Windows users
> also improves on Windows 10 with the new Bash on Windows.
{: .callout}

Pair up with your neighbor and work on these exercises:

> ## Case sensitive search
> Search for all case sensitive instances of
> a whole word you choose in all four derived `.csv` files in this 
directory.
> Print your results to the shell.
>
> > ## Solution
> > ~~~
 > > $ grep -w arcpro *.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Case sensitive search in select files
> Search for all case sensitive instances of a word you choose in
> the '2018' `.csv` files in this directory.
> Print your results to the shell.
>
> > ## Solution
> > ~~~
> > $ grep arcpro *2018.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Count words (case sensitive)
> Count all case sensitive instances of a word you choose in
> the '2019' `.csv` files in this directory.
> Print your results to the shell.
>
> > ## Solution
> > ~~~
> > $ grep -c arcpro *2019.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Count words (case insensitive)
> Count all case insensitive instances of that word in the '2019' `.csv` files
> in this directory. Print your results to the shell.
>
> > ## Solution
> > ~~~
> > $ grep -ci arcpro *2019.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Case insensitive search in select files
> Search for all case insensitive instances of that
> word in the '2019' `.csv` files in this directory. 
Print your results to  a file `results/2019.csv`.
>
> > ## Solution
> > ~~~
> > $ grep -i arcpro *2019.csv > results/arcpro_2019.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Case insensitive search in select files (whole word)
> Search for all case insensitive instances of that whole word
> in the '2019' `.csv` files in this directory. Print 
your results to a file `results/2019-i.csv`.
>
> > ## Solution
> > ~~~
> > $ grep -iw arcpro *2019.csv > results/arcpro_2019-i.csv
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Searching with regular expressions
> Use regular expressions to find all ISSN numbers
> (four digits followed by hyphen followed by four digits)
> in `Desk_Tracker_2019.csv` and print the results to a file 
`results/issns.csv`.
> Note that you might have to use the `-E` flag (or `-P` with some versions
> of `grep`, e.g. with Git Bash on Windows.). #change this to finding patterns in arc products 
>
> > ## Solution
> > ~~~
> > $ grep -E '\d{4}-\d{4}' 2014-01_JA.csv > issns.csv
> > ~~~
> > {: .bash}
> >
> > or
> >
> > ~~~
> > $ grep -P '\d{4}-\d{4}' 2014-01_JA.csv > issns.csv
> > ~~~
> > {: .bash}
> >
> > If you came up with something more advanced, perhaps including word boundaries,
> > please share your result in the collaborative document and give yourself a pat on the shoulder.
> >
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Finding unique values
> If you pipe something to the `uniq` command, it will filter out adjacent duplicate lines. In order for the 'uniq' command to only return unique values though, it needs to be used with the 'sort' command. Try piping the output from the command in the last exercise
> to `sort` and then piping these results to 'uniq' and then `wc -l` to count the number of unique ISSN values.
> Note: This exercise requires the `-o` flag. See the callout box "Invalid option -- o?"
> above.
>
> > ## Solution
> > ~~~
> > $ grep -Eo '\d{4}-\d{4}' 2014-01_JA.csv | sort | uniq | wc -l
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Counting number of files, part II
> In the earlier counting exercise in this episode, you tried counting the number
> of files and directories in the current directory.
>
> * Recall that the command `ls -l | wc -l` took us quite far, but the result was one
>   too high because it included the "total" line in the line count.
> * With the knowledge of `grep`, can you figure out how to exclude the "total"
>   line from the `ls -l` output?
> * Hint: You want to exclude any line *starting*
>   with the text "total". The hat character (^) is used
>   in regular expressions to indicate the start of a line.
>
> > ## Solution
> > To find any lines starting with "total", we would use:
> >
> > ~~~
> > $ ls -l | grep -E '^total'
> > ~~~
> > {: .bash}
> >
> > To *exclude those lines, we add the `-v` flag:
> >
> > ~~~
> > $ ls -l | grep -v -E '^total'
> > ~~~
> > {: .bash}
> >
> > The grand finale is to pipe this into `wc -l`:
> >
> > ~~~
> > $ ls -l | grep -v -E '^total' | wc -l
> > ~~~
> > {: .bash}
> {: .solution}
{: .challenge}

## Using a Loop to Count Words

We will now use a loop to automate the counting of certain words within a document. For this, we will keep 
using the Desktracker data but focus on the 2017 data.
 

In the loop, we will ask the computer to go through the text, looking for each 
research type that patrons needed assistance with, and count the number of times it appears. The results will 
print to the screen.

~~~
$ for name in "Spatial" "Statistics" "Content" "Software"
> do
>    echo "$name"
>    grep -wo "$name" Desk_Tracker_2017.csv | wc -l
> done
~~~
{: .bash}

~~~
Spatial
63
Statistics
48
Content
137
Software
124
~~~
{: .output}
>
> ## What is happening in the loop?  
>+ `echo "$name"` is printing the current value of `$name`
>+ `grep "$name" Desk_Tracker_2017.csv` finds each line that contains the value stored in `$name`. The `-w` 
>flag 
>+ finds only the whole word that is the value stored in `$name` and the `-o` flag pulls this value out from 
the line it is in to give you the actual words to count as lines in themselves.
>+ The output from the `grep` command is redirected with the pipe, `|` (without the pipe and the rest of the 
line, the output from `grep` would print directly to the screen)
>+ `wc -l` counts the number of _lines_ (because we used the `-l` flag) sent from `grep`. Because `grep` only 
returned lines that contained the value stored in `$name`, `wc -l` corresponds to the number of occurrences of each girl's name.
{: .callout}

> ## Why are the variables double-quoted here?
>
> a) In [episode 4]({{ page.root }}{% link _episodes/04-loops.md %}) we learned to
> use `"$..."` as a safeguard against white-space being misinterpreted.
> Why _could_ we omit the `"`-quotes in the above example?
> 
> b) What happens if you add `"Interlibrary Loan Requests"` to the first line of
> the loop and remove the `"` from `$name` in the loop's code? (Say you mistake the IRC's Desktracker for the 
> Mountain Information Desk's which falls under Access Services.) 
>
>> ## Solutions
>> 
>> a) Because we are explicitly listing the names after `in`,
>> and those contain no white-space. However, for consistency
>> it's better to use rather once too often than once too rarely.
>> 
>> b) Without `"`-quoting `$name`, the last loop will try to execute
>> `grep "Interlibrary Loan Requests" Desk_Tracker_2017.csv`. `grep` interprets only the
>> first word as the search pattern, but `Loan` and `Requests` as filenames.
>> This produces two errors and a possibly untrustworthy count:
>> ~~~
>> ...
>> Interlibrary Loan Requests
>> grep: Loan: No such file or directory
>> grep: Requests: No such file or directory
>> 0
>> ~~~
>> {: .bash}
> {: .solution}
{: .challenge}

## Selecting Columns from Datasets
When you receive data it will often contain more columns or variables than you need for your work. If you want 
to select only the columns you need for your analysis, you can use the 'cut' command to do so. 'cut' is a tool 
for extracting sections from a file.

> ## Using Delimiters with "cut"
>Sometimes we need to workaround datasets that haven't been cleaned for us. Normally, using the 'cut --f' flag 
>would extract the data, but that's assuming the data has already been cleaned. In our csv 
>files, the data has been separated by commas, so we must use a second flag, '--d' and use " " to indicate a 
>the type of delimiter that is being used to extract the data.
{: .callout}

> ## Extracting data from DeskTracker
> For instance, say we want to retain only the `response_set_id`, `date_time`, `Question Type`, and `Contact 
> Type` columns from our article data. With `cut` we'd:
> 
>~~~ 
>cut -f1,3,10,12 -d "," Desk_Tracker_2017.csv | head 
>~~~
>{: .bash}
>
>~~~
> response_set_id,date_time,Contact Type (text),Question Type (text)
> "51530", "2017-01-03 08:02:32", "Personal Email", "Reference / Research Assistance"
> "51800", "2017-01-09 14:57:27", "In Person", "Reference / Research Assistance"
> "51801", "2017-01-09 14:57:47", "In Person", "Directional / Informational"
> "52089", "2017-01-11 14:17:19", "In Person", "Reference / Research Assistance"
> "52209", "2017-01-12 15:23:41", "In Person", "Directional / Informational" 
> "52240", "2017-01-12 18:51:06". "In Person", "Reference / Research Assistance"
> "52335", "2017-01-13 15:06:25", "Personal Email", "Reference / Research Assistance"
> "52336", "2017-01-13 15:07:19", "In Person", "Reference / Research Assistance"
> "52337", "2017-01-13 15:07:33", "Personal Email", "Reference / Research Assistance"
>~~~
>{: .output}
>
> Above we used `cut` and the `-f` flag to indicate which columns we want to retain. `cut` works on tab delimited files by default. We can use the flag `-d` to change this to a comma, or semicolon or another delimiter.
> If you are unsure of your column position and the file has headers on the first line, we can use `head -n 1 <filename>` to print those out.
> ### Now your turn
>>Select the columns `response_time_id`, `date_time`, `Contact Type`, `Question Type` and direct the output 
>>into a new file. You can name it something like `Desk_Tracker_2016_simp.csv`.
>> ## Solution
>> First, let's see where our desired columns are:
>>
>>~~~
>> head -n 1 Desk_Tracker_2016.csv
>>~~~
>>{: .bash}
>>
>>~~~
>>response_set_id, parent_response_set_id, date_time, page, user, branch, desk, library, Contact Type, Contact 
Type (text), Question Type, Question Type (text)
>>~~~
>>{: .output}
>>
>>Ok, now we know `response_set_id` is column 1, `date_time` 3, `Contact Type` 10, and `Question Type` 12.
>> We use these positional column numbers to construct our `cut` command:
>>```
>> cut -f1,3,10,12 -d "," Desk_Tracker_2016.csv > Desk_Tracker_2016_simp.csv
>>```
>> We can confirm this worked by running "head" on the file:
>>```
>>head Desk_Tracker_2016_simp.csv
>>```
>>~~~
>>response_set_id,date_time,Contact Type (text),Question Type (text)
>>"43050", "2016-10-10 14:27:52", "In Person", "Reference / Research Assistance"
>>"43106", "2016-10-10 15:55:16", "In Person", "Reference / Research Assistance"
>>"43161", "2016-10-10 18:57:35", "Collab Email", "Reference / Research Assistance"
>>"43447", "2016-10-11 14:50:00", "In Person", "Reference / Research Assistance"
>>"43421", "2016-10-11 19:43:52", "In Person", "Directional / Informational"
>>"43580", "2016-10-12 15:20:49", "In Person", "Directional / Informational"
>>"43581", "2016-10-12 15:20:59", "In Person", "Directional / Informational"
>>"43590", "2016-10-12 16:06:24", "In Person", "Directional / Informational"
>>"43613", "2016-10-12 17:12:29", "In Person", "Reference / Research Assistance"
>>~~~
>>{: .output}
> {: .solution}
{: .challenge}
