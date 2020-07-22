# Date Seq

Easily create a sequance of dates.

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
wo 22 jul 2020 17:20:16 CEST
do 23 jul 2020 17:20:16 CEST
vr 24 jul 2020 17:20:16 CEST
za 25 jul 2020 17:20:16 CEST
```

Iterate with an increment from `now`.

```bash
$ seq 0 4 12 | date-seq
wo 22 jul 2020 17:20:16 CEST
zo 26 jul 2020 17:20:16 CEST
do 30 jul 2020 17:20:16 CEST
ma  3 aug 2020 17:20:16 CEST
```

How about creating a sequence from a few increments from the past till now.

```bash
$ seq -3 0 | date-seq
zo 19 jul 2020 17:20:16 CEST
ma 20 jul 2020 17:20:16 CEST
di 21 jul 2020 17:20:16 CEST
wo 22 jul 2020 17:20:16 CEST
```

Sadly seq doesn't give output when the FROM option is greater than the TILL.

In the case we want to reverse the sequence of dates we can do three things.

```bash
$ seq  0 -1 # Does not write anything to STDOUT.

$ seq -1  0 | tac | date-seq # firstly
wo 22 jul 2020 17:20:16 CEST
di 21 jul 2020 17:20:16 CEST
$ seq -1  0 | date-seq | tac # secondly
wo 22 jul 2020 17:20:16 CEST
di 21 jul 2020 17:20:16 CEST
$ seq  0  1 | date-seq '-%sdays' # thirdly
wo 22 jul 2020 17:20:16 CEST
di 21 jul 2020 17:20:16 CEST
```

Create a sequence where each increment is a month. For this we change the
template.

```bash
$ seq 0 3 | date-seq '+%smonth'
wo 22 jul 2020 17:20:16 CEST
za 22 aug 2020 17:20:16 CEST
di 22 sep 2020 17:20:16 CEST
do 22 okt 2020 17:20:16 CEST
```

The template is based on a combination of printf's `%s` string substitution and
date's --date argument value. See printf and date manual pages for more
information.

`date-seq` wraps the date command.

It takes date compatible options after the first template argument.

```bash
$ seq 0 3 | date-seq '-%smonth' +%b
jul
jun
mei
apr
```

In order to change the locale one simply configures it by using the date LANG
environment variables.

```bash
$ seq 0 3 | LANG='en-US' date-seq '-%smonth' +%b
Jul
Jun
May
Apr
```

[1]:https://stackoverflow.com/questions/28226229/how-to-loop-through-dates-using-bash/60512491#60512491
