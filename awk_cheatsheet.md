# AWK program

It used to manipulate TEXT files.

## Simple interaction with AWK

`file: names.txt`
```text
Gretchen Galloway
Isaac Steele
Wayne Myers
Lillith Lee
Molly Blackwell
Maia Arnold
```
## Terminology

- Record is the line of the text
- Field is the word in the line by default separated with space (field separator)
- Action is the function which applied to the text (ex. print)
- Pattern is the condition which applies on lines, to detect whether action should be applied on the line or not.

### Swap fields

`awk '{print $2, $1}' names.txt`
```
Galloway Gretchen
Steele Isaac
Myers Wayne
Lee Lillith
Blackwell Molly
Arnold Maia
```

Highlights:

- `$0` keeps the value of entire line
- Variables written next to each other results of their concatenation
- comma is a field separator which is a space by default

### Swap fields and concat by comma separator

`awk '{print $2 ", "  $1}' names.txt`

```text
Galloway, Gretchen
Steele, Isaac
Myers, Wayne
Lee, Lillith
Blackwell, Molly
Arnold, Maia
```

### AWK program structure

- Each program can consist of pattern and/or an action.
- Actions are enclosed in curly braces
- Default action is print
- Default pattern is everything

`file: dukeofyork.txt`
```text
The grand old Duke of York
He had ten thousand men
He marched them up to the top of the hill
And he marched them down again
And when they were up they were up
And when they were down they were down
And when they were only half-way up
They were neither up nor down
```

### Number of Fields

`awk '{print NF, $0}' dukeofyork.txt`

```text
6 The grand old Duke of York
5 He had ten thousand men
10 He marched them up to the top of the hill
6 And he marched them down again
...
```

### Number of fields with applied pattern

`awk 'NF==6 {print NF, $0}' dukeofyork.txt`

```text
6 The grand old Duke of York
6 And he marched them down again
6 They were neither up nor down
```

### We can omit an action

`awk 'NF==6' dukeofyork.txt`

```text
The grand old Duke of York
And he marched them down again
They were neither up nor down
```

### We can combine multiple pattern action statements

`awk '/up/{print "UP: " $0} /down/{print "DONW: " $0}' dukeofyork.txt`

```text
UP: He marched them up to the top of the hill
DONW: And he marched them down again
UP: And when they were up they were up
DONW: And when they were down they were down
UP: And when they were only half-way up
UP: They were neither up nor down
DONW: They were neither up nor down
```

- up and down between slashes (/) are just regex patterns.

### Invoke awk program from the file (-f flag)

`file: swap`

```shell
{print $2, $1}
```

`awk -f swap names.txt`

```text
Galloway Gretchen
Steele Isaac
Myers Wayne
Lee Lillith
Blackwell Molly
Arnold Maia
```

### Use a character(s) as a Field separator (-F flag)

Here we change FS from space into comma

`awk -F, '{print $2}'`

```text
one,two
two
...
```

When we skip to provide text file, then it takes it from standard input

Change field separator into a regex

`awk -F '[,!]' '{print $2}'`

```text
one,two!three
two
...
```

### Specify an awk variable in command line. (-v flag)

`awk -v hi=hello '{print hi, $1}'`

```text
Vardan
hello Vardan
...
```

### Pass input using pipe (|)

`date | awk '{print NF, $0}'`
```
6 Sun May 21 17:29:41 CEST 2023
```

