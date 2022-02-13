# Date Seq

Easily create a sequence of dates.

> Answer to a [Stackoverflow question][1].

## Reasoning

Bash is best written when leveraging pipes (`|`). It allows for easily
composing programs.

Most of you are familiar with the `seq` command. We'll use this to compose
a program that will create date sequences.

```bash bash
seq --help | head -n 3
```
```
Usage: seq [OPTION]... LAST
  or:  seq [OPTION]... FIRST LAST
  or:  seq [OPTION]... FIRST INCREMENT LAST
```

```bash bash
seq 3 # Does not contain the zero
```
```
1
2
3
```

```bash bash
seq 0 3 # Contains the zero
```
```
0
1
2
3
```

```bash bash
seq 0 4 12 # With a different increment than 1
```
```
0
4
8
12
```

```bash bash
seq -3 0 # From negative numbers till zero
```
```
-3
-2
-1
0
```

`date-seq` is a command that intends to leverage this command to create
sequences of dates.

## Usage

Dates from now till n days from now.

```bash bash
seq 0 3 | date-seq
```
```
zo 13 feb 2022 11:28:17 CET
ma 14 feb 2022 11:28:17 CET
di 15 feb 2022 11:28:17 CET
wo 16 feb 2022 11:28:17 CET
```

Iterate with an increment from `now`.

```bash bash
seq 0 4 12 | date-seq
```
```
zo 13 feb 2022 11:28:17 CET
do 17 feb 2022 11:28:17 CET
ma 21 feb 2022 11:28:17 CET
vr 25 feb 2022 11:28:17 CET
```

How about creating a sequence from a few increments from the past till now.

```bash bash
seq -3 0 | date-seq
```
```
do 10 feb 2022 11:28:17 CET
vr 11 feb 2022 11:28:17 CET
za 12 feb 2022 11:28:17 CET
zo 13 feb 2022 11:28:17 CET
```

`seq` doesn't give output when the FROM option is greater than the TILL.

```bash bash
seq 0 -1 # Does not write anything to STDOUT.
```

In the case we want to reverse the sequence of dates we can do three things.

```bash
seq -1  0 | tac | date-seq # firstly

seq -1  0 | date-seq | tac # secondly

seq  0  1 | date-seq '-%sdays' # thirdly
```

Create a sequence where each increment is a month. For this we change the
template.

```bash bash
seq 0 3 | date-seq '+%smonth'
```
```
zo 13 feb 2022 11:28:17 CET
zo 13 mrt 2022 11:28:17 CET
wo 13 apr 2022 12:28:17 CEST
vr 13 mei 2022 12:28:17 CEST
```

The template is based on a combination of printf's `%s` string substitution and
date's --date argument value. See printf and date manual pages for more
information.

`date-seq` wraps the date command.

It takes date compatible options after the first template argument.

```bash bash
seq 0 3 | date-seq '-%smonth' +%b
```
```
feb
jan
dec
nov
```

In order to change the locale one simply configures it by using the date LANG
environment variables.

```bash bash
seq 0 3 | LANG='en-US' date-seq '-%smonth' +%b
```
```
Feb
Jan
Dec
Nov
```

[1]:https://stackoverflow.com/questions/28226229/how-to-loop-through-dates-using-bash/60512491#60512491
