# Date Seq

Easily create a sequence of dates.

> Answer to a [Stackoverflow question][1].

## Reasoning

Bash is best written when leveraging pipes (`|`). It allows for easily
composing programs.

Most of you are familiar with the `seq` command. We'll use this to compose
a program that will create date sequences.

```
$ seq --help | head -n 3
Usage: seq [OPTION]... LAST
  or:  seq [OPTION]... FIRST LAST
  or:  seq [OPTION]... FIRST INCREMENT LAST

$ seq 3 # Does not contain the zero
1
2
3

$ seq 0 3 # Contains the zero
0
1
2
3

$ seq 0 4 12 # With an different increment than 1
0
4
8
12

$ seq -3 0 # From negative numbers till zero
-3
-2
-1
0
```

`date-seq` is a command that intends to leverage this command to create
sequences of dates.

## Usage

Dates from now till n days from now.

```bash
$ seq 0 3 | date-seq
do 11 feb 2021 11:43:35 CET
vr 12 feb 2021 11:43:35 CET
za 13 feb 2021 11:43:35 CET
zo 14 feb 2021 11:43:35 CET
```

Iterate with an increment from `now`.

```bash
$ seq 0 4 12 | date-seq
do 11 feb 2021 11:43:35 CET
ma 15 feb 2021 11:43:35 CET
vr 19 feb 2021 11:43:35 CET
di 23 feb 2021 11:43:35 CET
```

How about creating a sequence from a few increments from the past till now.

```bash
$ seq -3 0 | date-seq
ma  8 feb 2021 11:43:35 CET
di  9 feb 2021 11:43:35 CET
wo 10 feb 2021 11:43:35 CET
do 11 feb 2021 11:43:35 CET
```

Sadly seq doesn't give output when the FROM option is greater than the TILL.

In the case we want to reverse the sequence of dates we can do three things.

```bash
$ seq  0 -1 # Does not write anything to STDOUT.

$ seq -1  0 | tac | date-seq # firstly
do 11 feb 2021 11:43:35 CET
wo 10 feb 2021 11:43:35 CET
$ seq -1  0 | date-seq | tac # secondly
do 11 feb 2021 11:43:35 CET
wo 10 feb 2021 11:43:35 CET
$ seq  0  1 | date-seq '-%sdays' # thirdly
do 11 feb 2021 11:43:35 CET
wo 10 feb 2021 11:43:35 CET
```

Create a sequence where each increment is a month. For this we change the
template.

```bash
$ seq 0 3 | date-seq '+%smonth'
do 11 feb 2021 11:43:35 CET
do 11 mrt 2021 11:43:35 CET
zo 11 apr 2021 12:43:35 CEST
di 11 mei 2021 12:43:35 CEST
```

The template is based on a combination of printf's `%s` string substitution and
date's --date argument value. See printf and date manual pages for more
information.

`date-seq` wraps the date command.

It takes date compatible options after the first template argument.

```bash
$ seq 0 3 | date-seq '-%smonth' +%b
feb
jan
dec
nov
```

In order to change the locale one simply configures it by using the date LANG
environment variables.

```bash
$ seq 0 3 | LANG='en-US' date-seq '-%smonth' +%b
Feb
Jan
Dec
Nov
```

[1]:https://stackoverflow.com/questions/28226229/how-to-loop-through-dates-using-bash/60512491#60512491
