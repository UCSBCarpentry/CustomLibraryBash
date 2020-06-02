---
title: "Regular Expressions"
teaching: 20
exercises: 10
questions:
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

## Mining or searching

Searching for something in one or more files is something we'll often need to do,
so let's introduce a command for doing that: `grep` (short for **global regular
expression print**). As the name suggests, it supports regular expressions and
is therefore only limited by your imagination, the shape of your data, and - when
working with thousands or millions of files - the processing power at your disposal.

To begin using `grep`, first navigate to the `shell-lesson` directory if not already
there. Then create a new directory "results." We'll use this later:

~~~
$ mkdir results
~~~
{: .bash}


Now let's try our first search.  `cd desktrackers`.  DeskTracker is the system we use to record desk 
statistics in various places in the Library. It's reports are all Flash-driven, and today I just want
to explore the raw data to see how much GIS, or spatial data, service we give in the Library.

~~~
$ grep Spatial *.csv
~~~
{: .bash}

Remember that the shell will expand `*.csv` to a list of all the `.csv` files in the directory. 
`grep` will then search these for instances of the string "Spatial" in the raw datafiles and print 
all of the the matching lines, which will look like gobbledy-gook. Re-run the command with pipe `| 
more` to see the data one line at a time.  Remember that it's `q` for quit at a colon prompt.

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
Desk_Tracker.csv:374
Desk_Tracker_2016.csv:9
Desk_Tracker_2017.csv:63
Desk_Tracker_2018.csv:190
Desk_Tracker_2019.csv:112
~~~
{: .output}

The shell now prints the number of times the string "Spatial" appeared in each file. If you look at 
the output from the previous command, and remember that we confirmed the names of the files 
accurately reflect the years, you can surmise that we answered the most questions about 'spatial' 
matters in 2018.

> ## Is grep case sensitive?
> Now try searching for 'spatial' vs. 'Spatial'.  
> Why are the answers different? Should we add the results of 'spatial' to 'Spatial' when
> counting questions?
> > ## Solution
> > ~~~
> > $ grep -c spatial *.csv
> > ~~~
> > returns just a few results (1, 0, 5, 2)
> > ~~~
> > $ grep -c Spatial *.csv
> > ~~~
> > returns many more each year.
> >
> > The DeskTracker system has a checkbox for "Spatial" questions.
> > "spatial" is within text comments:
> > ~~~
> > $ grep spatial *.csv
> > The above will output the lines to confirm this, but it's hard to read.
> > Use a pipe to output your output to a file in the results directory
> > ~~~
> > {: .bash}
> > `grep spatial *.csv | ../results/isGrepCaseSensitive.csv` 
> > Insert image here?
> {: .solution}
> > If you open that file in nano, you can `^w` to search for text in
> > the file.
{: .challenge}

Let's try another search:

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

We got back the counts of the instances of the string `Refer` within the files. It looks like 
reference volume has been steadily falling.  Now, amend the above command to the below and observe 
how the output of each is different:

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

Just like spatial, reference appears just a few times more in the file.

> ### Count words (case insensitive)
> We answer questions about Esri software, ArcPro. But we often type arcpro, 
> ArcPro, and Arcpro.  Count all case insensitive instances of this in the 
> DeskTracker files in this directory. Print your results to the shell.  What year did
> we answer the most questions about ArcPro?
>
> > ### Solution
> > ~~~
> > $ grep -ci arcpro *.csv
> > ~~~
> > {: .bash}
> > This repeats the query, but prints a case
> > insensitive count.
> > As before, adding `> results/`, followed by a filename, will save the 
> > results to a data file.
> {: .solution}
{: .challenge}


So far we have counted strings in files and printed to the shell or to
file those counts. But the real power of `grep` comes in that you can
also use it to create subsets of tabulated data (or indeed any data)
from one or multiple files.  

We can let the shell add today's date to the
filename (say this was a daily occurrence and we wanted to keep track of dates):

~~~
$ grep -i refer *.csv > results/$(date -I)_i-refer.csv
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
string 'refer' including as a single word and as part of other words
 such as 'reference'. This perhaps isn't as useful as we thought...
Thankfully, the `-w` flag instructs `grep` to look for whole words only,
giving us greater precision in our search--in this case to find where questions
were referred to other librarians.

~~~
$ grep -iw Refer *.csv > results/$(date -I)_iw-refer.csv
~~~
{: .bash}

This script looks in both of the defined files and
exports any lines containing the whole word `refer` (without regard to case)
to the specified `.csv` file.

We can show the difference between the files we created.

~~~
$ wc -l results/*.csv
~~~
{: .bash}
~~~
  205089 i-refer.csv
      22 iw-refer.csv
  205111 total
~~~
{: .output} 


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

 

