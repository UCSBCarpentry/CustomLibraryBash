---
title: "Working with free text"
teaching: 20
exercises: 40
questions:
- "How do we work with complex files?"
objectives:
- "Use shell tools to clean and transform free text"
keypoints:
- "Shell tools can be combined to powerful effect"
---
### Working with free text

So far we have looked at how to use the Unix shell to manipulate, count, and
mine tabulated data. Some library data, especially digitised documents, is much messier than
tabular metadata. Nonetheless, many of the same techniques can be applied
to non-tabulated data such as free text. We need to think carefully about
what it is we are counting and how we can get the best out of the Unix shell.

Thankfully there are plenty of folks out there doing this sort of work and we
can borrow what they do as an introduction to working with these more complex files.
So for this final exercise we're going to leap forward a little in terms
of difficulty to a scenario where we won't learn about everything that
is happening in detail or discuss at length each command. We're going
to prepare and pull apart texts to demonstrate some of the potential applications of the Unix shell. And where commands we've learnt about are used,
I've left some of the figuring out to do to you - so please refer to your notes if you get stuck!

Before going any further, speak to the person next to you and choose which type of text you'd like to work on together. You have three options:

- An example of hand transcribed text: *Gulliver's Travels* (1735)
- An example of text captured by an optical character recognition process: *General Report on the Physiography of Maryland. A dissertation, etc. (Reprinted from Report of Maryland State Weather Service.) [With maps and illustrations.]* 1898 (from [https://doi.org/10.21250/db12](https://doi.org/10.21250/db12))
- An example of a webpage: Piper's World (a GeoCities page from 1999 saved at [archive.org](http://wayback.archive.org/web/20091020080943/http:/geocities.com/Heartland/Hills/7649/diary.html))

## Option 1: Hand transcribed text

### Grabbing a text, cleaning it up

We're going to work with the `gulliver.txt` file we made in the previous lesson.
You should (still) be working in the `shell-lesson` directory.

Let's look at the file.

~~~
$ less -N gulliver.txt
~~~
{: .bash}
~~~
      1 <U+FEFF>The Project Gutenberg eBook, Gulliver's Travels, by Jonatha
      1 n Swift
      2
      3
      4 This eBook is for the use of anyone anywhere at no cost and with
      5 almost no restrictions whatsoever.  You may copy it, give it away o
      5 r
      6 re-use it under the terms of the Project Gutenberg License included
      7 with this eBook or online at www.gutenberg.org
      8
      9
     10
     11
     12
     13 Title: Gulliver's Travels
     14        into several remote nations of the world
     15
     16
     17 Author: Jonathan Swift
     18
     19
     20
     21 Release Date: June 15, 2009  [eBook #829]
     22
     23 Language: English
     24
     25 Character set encoding: UTF-8
     26
     27
     28 ***START OF THE PROJECT GUTENBERG EBOOK GULLIVER'S TRAVELS***
     29
     30
     31 Transcribed from the 1892 George Bell and Sons edition by David Pri
     31 ce,
     32 email ccx074@pglaf.org
     33
     34
     35
~~~
{: .output}

We're going to start by using the `sed` command. The command allows you to edit files directly.

~~~
$ sed '9352,9714d' gulliver.txt > gulliver-nofoot.txt
~~~
{: .bash}

The command `sed` in combination with the `d`
value will look at `gulliver.txt` and delete all
values between the rows specified. The `>` action then
prompts the script to this edited text to the new file specified.

~~~
$ sed '1,37d' gulliver-nofoot.txt > gulliver-noheadfoot.txt
~~~
{: .bash}

This does the same as before, but for the header.

You now have a cleaner text. The next step is to
prepare it even further for rigorous analysis.

We now use the `tr` command, used for translating or
deleting characters. Type and run:

~~~
$ tr -d [:punct:] < gulliver-noheadfoot.txt > gulliver-noheadfootpunct.txt
~~~
{: .bash}

This uses the translate command and a special syntax to remove all punctuation.
It also requires the use of both the output redirect `>` we have seen and the input redirect `<` we haven't seen.

Finally regularise the text by removing all the uppercase lettering.

~~~
$ tr [:upper:] [:lower:] < gulliver-noheadfootpunct.txt > gulliver-clean.txt
~~~
{: .bash}

Open the `gulliver-clean.txt` in a text editor. Note how the text has been transformed ready for analysis.

### Pulling a text apart, counting word frequencies

We are now ready to pull the text apart.

~~~
$ tr ' ' '\n' < gulliver-clean.txt | sort | uniq -c | sort -r > gulliver-final.txt
~~~
{: .bash}

Here we've made extended use of the pipes we saw in [Counting and mining with the shell]({{ page.root }}{% link 
_episodes/05-counting-extracting.md %}). The first part of this script uses the translate command again, this time to translate 
every blank space into `\n` which renders as a new line. Every word in the file will at this stage have its own line.

The second part uses the `sort` command to rearrange the text from its original order into an alphabetical configuration.

The third part uses `uniq`, another new command, in combination with the `-c` flag to remove duplicate lines and to produce a word count of those duplicates.

The fourth and final part sorts the text again by the counts of duplicates generated in step three.

> ## Challenge
> There are still some remaining punctuation in the text. They are called 'smart' or 'curly' quotes. 
> Can you remove them using `sed`?
>
> > ## Solution
> > This allows us to do some bug fixing and search the internet for answers either using 'sed smart quotes' or 'sed curly quotes' as our keywords to start.
> {: .solution}
{: .challenge}

We have now taken the text apart and produced a
count for each word in it. This is data we can prod and poke
and visualise, that can form the basis of our investigations,
and can compare with other texts processed in the same way.
And if we need to run a different set of transformation for
a different analysis, we can return to `gulliver-clean.txt` to start that work.

And all this using a few commands on an otherwise unassuming but very powerful command line.


## Option 2: A webpage

### Grabbing a text, cleaning it up

We're going to work with the UCSB East Asian Studies Faculty website.

First grab the file from the web. 

~~~
$ wget https://www.eastasian.ucsb.edu/people/faculty/
~~~
{: .bash}

Websites are often downloaded through wget as index.html. Let's look at the file.

~~~
$ less -N index.html
~~~
{: .bash}
~~~
  1 <!DOCTYPE html>
      2 <!--[if IE 7 | IE 8]>
      3 <html class="ie" lang="en-US">
      4 <![endif]-->
      5 <!--[if !(IE 7) | !(IE 8)  ]><!-->
      6 <html lang="en-US">
      7 <!--<![endif]-->
      8 <head>
      9         <meta charset="UTF-8" />
     10         <meta http-equiv="X-UA-Compatible" content="IE=edge" /><script type="text/javascript">(window.NREUM||(NREUM={})).loader_c     10 onfig={licenseKey:"fd891ebd1f",applicationID:"304466369"};window.NREUM||(NREUM={}),__nr_require=function(e,n,t){function r(t){if(     10 !n[t]){var i=n[t]={exports:{}};e[t][0].call(i.exports,function(n){var i=e[t][1][n];return r(i||n)},i,i.exports)}return n[t].expor     10 ts}if("function"==typeof __nr_require)return __nr_require;for(var i=0;i<t.length;i++)r(t[i]);return r}({1:[function(e,n,t){functi     10 on r(){}function i(e,n,t){return function(){return o(e,[u.now()].concat(f(arguments)),n?null:this,t),n?void 0:this}}var o=e("hand     10 le"),a=e(4),f=e(5),c=e("ee").get("tracer"),u=e("loader"),s=NREUM;"undefined"==typeof window.newrelic&&(newrelic=s);var p=["setPag     10 eViewName","setCustomAttribute","setErrorHandler","finished","addToTrace","inlineHit","addRelease"],l="api-",d=l+"ixn-";a(p,funct     10 ion(e,n){s[n]=i(l+n,!0,"api")}),s.addPageAction=i(l+"addPageAction",!0),s.setCurrentRouteName=i(l+"routeName",!0),n.exports=newre     10 lic,s.interaction=function(){return(new r).get()};var m=r.prototype={createTracer:function(e,n){var t={},r=this,i="function"==typ     10 eof n;return o(d+"tracer",[u.now(),e,t],r),function(){if(c.emit((i?"":"no-")+"fn-start",[u.now(),r,i],t),i)try{return n.apply(thi     10 s,arguments)}catch(e){throw c.emit("fn-err",[arguments,this,e],t),e}finally{c.emit("fn-end",[u.now()],t)}}}};a("actionText,setNam     10 e,setAttribute,save,ignore,onEnd,getContext,end,get".split(","),function(e,n){m[n]=i(d+n)}),newrelic.noticeError=function(e,n){"s     10 tring"==typeof e&&(e=new Error(e)),o("err",[e,u.now(),!1,n])}},{}],2:[function(e,n,t){function r(e,n){var t=e.getEntries();t.forE     10 ach(function(e){"first-paint"===e.name?c("timing",["fp",Math.floor(e.startTime)]):"first-contentful-paint"===e.name&&c("timing",[     10 "fcp",Math.floor(e.startTime)])})}function i(e,n){var t=e.getEntries();t.length>0&&c("lcp",[t[t.length-1]])}function o(e){if(e in     10 stanceof s&&!l){var n,t=Math.round(e.timeStamp);n=t>1e12?Date.now()-t:u.now()-t,l=!0,c("timing",["fi",t,{type:e.type,fid:n}])}}if     10 (!("init"in NREUM&&"page_view_timing"in NREUM.init&&"enabled"in NREUM.init.page_view_timing&&NREUM.init.page_view_timing.enabled=     10 ==!1)){var a,f,c=e("handle"),u=e("loader"),s=NREUM.o.EV;if("PerformanceObserver"in window&&"function"==typeof window.PerformanceO     10 bserver){a=new PerformanceObserver(r),f=new PerformanceObserver(i);try{a.observe({entryTypes:["paint"]}),f.observe({entryTypes:["     10 largest-contentful-paint"]})}catch(p){}}if("addEventListener"in document){var l=!1,d=["click","keydown","mousedown","pointerdown"     10 ,"touchstart"];d.forEach(function(e){document.addEventListener(e,o,!1)})}}},{}],3:[function(e,n,t){function r(e,n){if(!i)return!1     10 ;if(e!==i)return!1;if(!n)return!0;if(!o)return!1;for(var t=o.split("."),r=n.split("."),a=0;a<r.length;a++)if(r[a]!==t[a])return!1     10 ;return!0}var i=null,o=null,a=/Version\/(\S+)\s+Safari/;if(navigator.userAgent){var f=navigator.userAgent,c=f.match(a);c&&f.index
~~~
{: .output}

We're going to start by using the `sed` command. The command allows you to edit files directly.

~~~
$ sed '285,404d' index.html > index-nofoot.txt
~~~
{: .bash}

The command `sed` in combination with the `d`
value will look at `index.html` and delete all
values between the rows specified. The `>` action then
prompts the script to this edited text to the new file specified.

~~~
$ sed '1,280d' index-nofoot.txt > index-noheadfoot.txt
~~~
{: .bash}

This does the same as before, but for the header.

You now have a cleaner text. The next step is to
prepare it even further for rigorous analysis.

First we wil remove all the html tags. Type and run:

~~~
$ sed -e 's/<[^>]*>//g' index-noheadfoot.txt > index-notags.txt
~~~
{: .bash}

Here we are using a regular expression (see the [Library Carpentry regular expression lesson]({{ page.root }}/lc-data-intro/04-regular-expressions/) to find all valid html tags (anything within angle brackets) and delete them. This is a complex regular expression, do don't worry too much about how it works! 

We see that all the information is on one line in the file. 
Type and run:

~~~
$ sed -e 's/\s\{13\}/\n/g' index-notags.txt > index-lineseparated.txt
~~~
{: .bash}

This replaces 13 instances of whitespace characters with a new line character. 


Open the `index-lineseparated.txt` in a text editor. Note how the text has been transformed ready for analysis.

### Pulling a text apart

We are now ready to grab the email addresses.

~~~
grep -oE '(\w|\.)+@(\w|\.)+' index-lineseparated.txt | sort 
~~~
{: .bash}


We can also check how many of the faculty have offices on the same floor of HSSB.

~~~
grep -o "HSSB\s*[0-9]" index-lineseparated.txt | sort | uniq -c | sort -n
~~~
{: .bash}

Here we've made extensive use of the pipes we saw in [Counting and extracting with the shell]({{ page.root }}{% link 
_episodes/05-counting-extracting.md %}).

The second part uses the `sort` command to rearrange the room numbers from its original order into an alphabetical configuration.

The third part uses the `uniq` command to find the uniq values with a count specified by the `-c` flag.

The fourth part sorts the counts in ascending order.

All this information was extracted from a web page with just a few commands to clean it up and process it.

## Where to go next

Deborah S. Ray and Eric J. Ray, *Unix and Linux: visual quickstart guide*, 4th edition (2009).
Invaluable (and not expensive) as a reference guide - especially if you only use the command line sporadically!

[The Command Line Crash Course](https://learncodethehardway.org/unix/)
'Learn UNIX the Hard Way' -- good for reminders of the basics.

[Automate the Boring Stuff](https://automatetheboringstuff.com/)

Another Coursera course, [Programming for Everybody (Python)](https://www.coursera.org/course/pythonlearn)
is available and lasts 10 weeks, if you have 2-4 hours to spare per week.
Python is popular in research programming as it is readable, relatively simple, and very powerful.

Bill Turkel and the Digital History community more broadly.
The second lesson you did today was based on a lesson Bill has on [his website](https://williamjturkel.net/2013/06/15/basic-text-analysis-with-command-line-tools-in-linux/) and Bill is also a general editor of the [Programming Historian](https://programminghistorian.org/project-team). The Programming Historian is an open, collaborative book aimed at providing programming lessons to historians. Although the lessons are hooked around problems historians have, their lessons - which cover various programming languages - have a wide applicability - indeed today's course is based on two lessons I wrote with Ian Milligan, an historian at Waterloo, Canada - for ProgHist. Bill also has a wonderful tutorial on ['Named Entity Recognition with Command Line Tools in Linux'](http://williamjturkel.net/2013/06/30/named-entity-recognition-with-command-line-tools-in-linux/) which I thoroughly recommend if you want to automatically find, markup, and count names, places, and organisations in text files...

## Conclusion

In this session you have learned to navigate the Unix shell, to undertake some
basic file counting, concatenation and deletion, to query across data for common
strings, to save results and derived data, and to prepare textual data for rigorous computational analysis.

This only scratches the surface of what the Unix environment is capable of.
It is hoped, however, that this session has provided a taster sufficient to
prompt further investigation and productive play.

Keep in mind that the full potential the tools can offer may only
emerge upon embedding these skills into real projects. Nonetheless,
being able to manipulate, count and mine thousands of files is extremely useful.
Even a large collection of files which do not contain any alpha-numeric data
elements, such as image files, can be easily sorted, selected and queried depending
on the amount of description, of metadata contained in each filename.
If not a prerequisite of working with the Unix, then taking the time
to structure your data in a consistent and predictable manner is
certainly a significant step towards getting the most out of Unix
commands. And if you can find a way of using the Unix shell regularly - perhaps
only to copy or amend files - you'll keep the basics fresh, meaning that
next time you have cause to use the Unix shell for more complex commands,
you shouldn't need to learn it all over again.

## References

James Baker and Ian Milligan, 'Counting and mining research data with Unix', *The Programming Historian* ([2014](https://programminghistorian.org/lessons/research-data-with-unix))

Ian Milligan and James Baker, 'Introduction to the Bash Command Line', *The Programming Historian* ([2014](https://programminghistorian.org/lessons/intro-to-bash))

William J. Turkel, 'Named Entity Recognition with Command Line Tools in Linux' ([30 June 2013](http://williamjturkel.net/2013/06/30/named-entity-recognition-with-command-line-tools-in-linux/)). The section 'NER Demo' is adapted from this and shared under a [Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported](https://creativecommons.org/licenses/by-nc-sa/3.0/).

William J. Turkel, 'Basic Text Analysis with Command Line Tools in Linux' ([15 June 2013](https://williamjturkel.net/2013/06/15/basic-text-analysis-with-command-line-tools-in-linux/)). The sections 'Grabbing a text, cleaning it up' and 'Pulling a text apart, counting word frequencies' are adapted from this and shared under a [Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported](https://creativecommons.org/licenses/by-nc-sa/3.0/).
