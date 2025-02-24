# 1 Bash Overview

## 1.1 Bash Startup Files

Startup procedure of Bash depends on the shell types. Shell can be either *login* or *non-login, interactive* or *non-interactive*, to tell what type of shell is running:

1. Use ``echo $-`` to check the set of options for current shell, the pattern would be `*i*` if *interactive*.
2. (Bash only) Use ``shopt login_shell`` to check if current shell is *login*
3. Use ``echo $0``, if the output starts with hyphen "``-``", then it's *login*; otherwise, we're not sure.
4. ``ssh`` are usually *login* shell. Subshell, if not invoked with ``-l`` option, are usually *non-login*.

So the startup procedure can be distro dependent. Based on *Ubuntu 22.04 wsl2*:  

1. Login shell ( Bourne and Bourne-compatible shells ) reads ``/etc/profile`` and ``~/.profile`` . Bash executes ``~/.bash_logout`` upon logout.
2. Non-login shell, usually opened with an icon or a menu item, reads nothing.
3. Interactive Bash would read ``/etc/bash.bashrc`` ( which may be triggered by ``/etc/profile`` ) and ``~/.bashrc``(  which may be triggered by `~/.profile`)
4. Non-interactive Bash reads nothing.
5. Other files could be read in other distros: ``/etc/bashrc``, ``~/.bash_profile``, ``~/.bash_login``
6. To prevent double sourcing

```bash
if [ -z "$BASHRCSOURCED" ]; then
  BASHRCSOURCED="Y"
  # COMMANDS
fi
```

## 1.2 Shell built-in commands

Bourne Shell built-in commands:
**:, ., break, cd, continue, eval, exec, exit, export, getopts, hash, pwd, readonly, return, set, shift, test, \[, times, trap, umask, unset**

Bash built-in commands:
**alias, bind, builtin, command, declare, echo, enable, help, let, local, logout, printf, read, shopt, type, typeset, ulimit, ualias**

POSIX special built-ins:
**:, ., break, continue, eval, exec, exit, export, readonly, return, set, shift, trap, unset**

## 1.3 Shell Parsing Syntax

1. The shell reads input from a file, a string or the terminal.
2. Input is broken up into words and operators, obeying **quoting rules**. **Alias expansion** is performed.
3. The shell parses the tokens into simple and compound commands, performing **expansions** in a specific order.
4. **Redirection** is performed, redirection operators and operands are removed from argument list.
5. Commands are executed.
6. Optionally wait for completion and collects exit status.

## 1.4 Executing Commands

"A simple command is a **sequence of optional variable assignments** followed by blank-separated **words and redirections**, and terminated by a control operator. The **first word** specifies the command to be executed, and is passed as argument zero. The remaining words are passed as arguments to the invoked command." -- Bash man page

1. Variable assignments (preceding the command name) and redirections are **saved but not performed** for later reference. 
2. The first remaining word after expansion is taken to be **the name of the command** 
3. **Redirections** are performed 
4. Strings assigned to variables are **expanded, and assignments** are performed.
5. If no command name results, variables will affect the current shell environment.

To look up the command given the command name:

1. Check whether the command contains **slashes**. If not, first check with the **function list**. 
2. If command is not a function, check for it in the **built-in list**.
3. If command is neither a function nor a built-in, look for it analyzing the directories listed in ``PATH``.
4. If the search is unsuccessful, Bash prints an error message and returns an exit status of 127.
5. If the search was successful or if the command contains slashes, the shell executes the command in a separate execution environment.

That explains why `` /some/dir$ script.sh `` would not work if neither ``/some/dir`` nor ``.`` is in the ``PATH``. Instead, ``$ ./script.sh`` should be used.

Moreover, it's a good habit to start relative path with ``./``

1. Consider 

```bash
source helper.sh   # $PATH would be searched first
source ./heler.sh  # always source local script
```

2. Avoid ambiguous semantics like ``touch -a file``, i.e. file name starting with hyphen. Another way to avoid the ambiguous hyphen is to use end of flags: double hyphen``--``.

## 1.5 List of Commands

A `list` is a sequence of one or more pipelines separated by one of the operators ‘\;’, ‘\&’, ‘\&\&’, or ‘\|\|’, and optionally terminated by one of ‘\;’, ‘\&’, or a `newline`.

Of these list operators, ‘\&\&’ and ‘\|\|’ have equal precedence, followed by ‘\;’ and ‘\&’, which have equal precedence.

For "\&\&" command2 is executed if, and only if, command1 returns an exit status of zero (success).

For "\|\|" command2 is executed if, and only if, command1 returns a non-zero exit status.

The return status of AND and OR lists is the exit status of the last command executed in the list.

```bash
echo -n A || echo -n B && echo -n C # AC $?=0
echo -n A || echo -n B && false # A $?=1
echo -n A || false && echo -n B # AB $?=0
false || echo -n A && echo -n B # AB $?=0
```

## 1.6 Executing the Script

1. A script is preferred to be executed in a sub-shell or child shell, for post-execution cleanup.  
2. In order to change the **current shell context**, or if you don't want to start a new shell, use ``.`` (dot command) for Bourne shell and ``source`` for Bash.
3. ``export NAME=VAL`` the variable would be **inherited to child process** (and subshell). Once exported, changes of the variable in the same shell would be reflected in later child process, i.e. no need to export twice.
4. Subshell vs child shell
	1. Subshell is usually invoked by `` () ``, `` $() ``, `` `...` ``.  Child shell is usually invoked by explicit call ``bash ./scripts.sh`` or ``./script.sh``.
	2. Obeying POSIX requirement, ``$$``, ``$PPID`` are preserved at subshell. To access the actual PID, use ``$BASHPID``
	3. Examples

```bash
echo $$ $PPID $BASHPID        # 6651 6646 6651 current shell
(echo $$ $PPID $BASHPID)      # 6651 6646 7349 subshell
bash -c 'echo $PPDI $BASHPID' # 7351 6651 7351 shell in child process

export a=0
b=1
echo $a ${b-unset}           # 0 1
(echo $a ${b-unset})         # 0 1 subshell shares all variables
bash -c 'echo $a $b'         # 0   child process shares exported variables
b=2 echo $a $b               # 0 1 expanded first, then b=2 applys to echo
b=2 bash -c 'echo $a $b'     # 0 2 the var assignment applys to child bash
(b=2; echo $a $b)            # 0 2
echo $a $b                   # 0 1 changes in subshell won't be reflected
```

## 1.7 Debugging and Configuring Bash 

The most common is to start up the subshell with the ``-x`` option, which will run the entire script in debug mode, or enclose some commands with ``set -x`` and ``set +x. Traces of each command plus its arguments are printed to standard output after the commands have been expanded but before they are executed.  

1. `` set -f `` or `` set -o noglob `` To disable file name generation using metacharacters (globbing). For example, ``*`` would be interpreted as a filename.
2. `` set -v `` or `` set -o verbose `` To prints shell input lines as they are read.
3. `` set -x `` or `` set -o xtrance `` To print command traces before executing commands.\
4. `` set -o `` show all options
5. `` set -o optname `` turn on options; `` set +o optname `` turn off options
6. Use PS1 variable and control block for coloring of prompts

```bash
TITLE='\[\e]0;\u@\h: \w\a\]'
PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$'
```


# 2 Parameters and Expansions

## 2.1 Parameters and Variables

In a bash context a parameter is an entity that stores values. Furthermore, a parameter can be a name ( variable ), number ( positional parameter ) or special character ( special parameter ).

1. **Global / environment** variables: available in all shells, use ``env`` or ``printenv`` to display
2. **Local** variables: available in the current shell, use ``set`` to display a list of all variables (including environment variables) and functions. Use ``unset`` to unset variables.
3. By content, variables can be **string, integer, constant or array**.
4. Variables are **case sensitive** and **capitalized** by default. Variables can also contain digits, but a name starting with a digit is not allowed. Variables may contain or start with underscore "``_``".
5. Assignment: ``VAR="VALUE"`` **no spaces around equals**, **quote** content string as much as possible. Use ``readonly OPTION VARIABLES`` to create constants. Use a ``declare OPTIONS VAR=VALUE`` statement to limit the value assignment to variables.

| Options | Meaning |
| ------- | ------- |
| -a | Variable is an array. |
| -A | Variable is an associate array. |
| -i | he variable is to be treated as an integer; arithmetic evaluation is performed when the variable is assigned a value. |
| -p | Display the attributes and values of each variable. |
| -r | Make variables read-only. |
| -x | Mark each variable for export to subsequent commands via the environment. |

6. Exporting: In order to pass variables to a subshell, ``export VAR=VALUE``. Variables that are exported are referred to as environment variables.
7. Reserved Variables: like ``OPTARG, OPTIND, PATH, PS1, PS2`` etc.
8. Special parameters

| Character | Definition |
| --------- | ---------- |
| ``$*``    | Expands to the positional parameters, starting from one. |
| ``$@``    | Expands to the positional parameters, starting from one. Preferred over ``$*``. |
| ``$#``    | Expands to the number of positional parameters in decimal. |
| ``$?``    | Expands to the exit status of the most recently executed foreground pipeline. |
| ``$-``    | A hyphen expands to the current option flags as specified upon invocation, by the **set** built-in command, or those set by the shell itself (such as the -i).  |
| ``$$``    | Expands to the process ID of the shell. |
| ``$!``    | Expands to the process ID of the most recently executed background (asynchronous) command. |
| ``$0``    | Expands to the name of the shell or shell script. |
| ``$_``    | The underscore variable is set at shell startup and contains the absolute file name of the shell or script being executed as passed in the argument list. Subsequently, it expands to the last argument to the previous command, after expansion. It is also set to the full pathname of each command executed and placed in the environment exported to that command. When checking mail, this parameter holds the name of the mail file. |

9. Array and associate array: declare an array ``ARRAY=( str1 str2 )`` or ``ARRAY=( [1]=str1 [2]=str2)``. Declare an associate array ``declare -A ARRAY=([key1]=str1 [key2]=str2)``. Use ``ARRAY+=( str )`` or ``ARRAY+=( [key]=str )`` to append. Use ``unset ARRAY[key]`` ``unset ARRAY`` to delete. Use 

## 2.2 Quoting

1. Escape character: A non-quoted backslash, ``\``, is used as an escape character in Bash. It preserves the literal value of the next character that follows, with the exception of *newline*. If a newline character appears immediately after the backslash, it marks the continuation of a line when it is longer that the width of the terminal; the backslash is removed from the input stream and effectively ignored.
2. Single quotes: Single quotes are used to preserve the **literal value** of each character enclosed within the quotes. **A single quote may not occur between single quotes**, even when preceded by a backslash. Example ``echo 'There'\''s no escape'`` generates ``There's no escape``
3. Double quotes: Using double quotes the **literal value** of all characters enclosed is preserved, **except for the dollar sign, the backticks (backward single quotes, `` ` ``) and the backslash.**
4. ANSI-C quoting: ``$'STRING'`` expands to a string, with backslash-escaped characters replaced as specified by the ANSI-C standard.

## 2.3 Shell Expansion

The "$" character introduces **parameter expansion, command substitution, or arithmetic expansion**. The expansion is performed in the following order.

1. **Brace expansion**: performed before any other expansion, strictly textual, dollar sign is not allowed right before the left brace. A sequence expression takes the form `{x..y[..incr]}`, where x and y are either integers or letters, and incr, an optional increment, is an integer.

```bash
echo student{a,b,c}
studenta studentb studentc
```

2. **Tilde expansion**: start with unquoted ``~``, all up to the first unquoted slash (if no slash, then all chars) is called tilde prefix. ``~uname`` first try to parse as a login name, and be expanded to home dir of the user. If login name is null string, replace it with ``$HOME``.  If ``HOME`` is unset, replace it with home of sh executor. ``~+`` is replaced by ``PWD``, ``~-`` is replaced by ``OLDPWD``, ``~+N  ~-N  ~N`` interact with dirstack.

3. **Parameter and variable expansion**: Like ``"$VAR ${VAR}"``. **Braces** are recommended to avoid semantic ambiguity. When braces are used, ending brace is the first ``}`` not escaped by a backslash, not within a quoted string, and not within embedded expansion

4. **Command substitution**: Allows the output of a command to replace the command itself.``"$(command)"`` is preferred over `` `command` ``, because the latter has some backslash escape mechanism. May be nested. If appeared in double quotes, word splitting and filename expansion are not performed on the results.

5. **Arithmetic expansion**: ``"$((EXPR))"`` is preferred over ``"$[EXPR]"``. May be nested. The expression is treated as if it were within double quotes. Evaluation is done in fixed-width integers with no overflow check, div by zero is trapped and recognized as an error. Operator precedence in decreasing order:

| Operator | Meaning |
| -------- | ------- |
| VAR++ and VAR-- | variable post-increment and post-decrement |
| ++VAR and --VAR | variable pre-increment and pre-decrement   |
| - and + | unary minus and plus                       |
| ! and ~ | logical and bitwise negation               |
| \*\* | exponentiation                             |
| *, / and % | multiplication, division, remainder        |
| + and - | addition, subtraction                      |
| << and >> | left and right bitwise shifts              |
| <=, >=, < and >   | comparison operators                       |
| == and !=   | equality and inequality                    |
| & | bitwise AND                                |
| ^ | bitwise exclusive OR                       |
| \| | bitwise OR                                 |
| && | logical AND                                |
| \|\|  | logical OR                                 |
| expr ? expr : expr | conditional evaluation                     |
| =, *=, /=, %=, +=, -=, <<=, >>=, &=, ^= and \|= | assignments  |
|, | separator between expressions              |

Constants with leading 0 are interpreted as octal numbers. A leading "0x" or "0X" denotes hexadecimal. ``B#N`` means B-based number, ``N`` means base 10.

6. **Process substitution**: ``<(LIST) >(LIST)`` use named pipes (FIFOs) or the ``/dev/fd`` method of naming open files. As a result of substitution, the name of this file (i.e. ``/dev/fd/63``) is passed as an argument to the current command. 
	- ``<(LIST)``: the file passed as an argument should be read to obtain the output of LIST.
	- ``>(LIST)``: writing to the file will provide input for LIST.  
	- When available, process substitution is performed simultaneously with parameter and variable expansion, command substitution, and arithmetic expansion.

7. Word splitting and file name expansion

```bash
word="a   b"
echo $word
# a b
echo "$word"
# a   b
```

## 2.4 More on Variable Expansion 

The double quotes are omitted in the following points

1. ``${!VAR}`` **Indirect expansion**, allowing double expansion
2. ``${!PREFIX*}`` **Names matching prefix**.  Expands to the names of variables whose names begin with ``PREFIX``,  separated  by the  first character of the IFS special variable.  When @ is used and the expansion appears within double quotes, each variable name expands to a separate word.
3. ``${!name[@]}`` **List of array keys.** If name is an array variable, expands to the list of array indices (keys) assigned in ``name``. 
4. ``${name[@]}``expands each element of name to a separate word.
5. ``${#VAR}`` Length of VAR, or number of elements in an array.
6. ``${VAR-DEFAULT}  ${VAR:-DEFAULT}  ${VAR:=DEFAULT}`` if a is not empty, then expand normally (except ``:+``). To tell the differences

| symbol | usage                                       |
| ------ | ------------------------------------------- |
| ``-``  | expand DEFAULT if VAR is unset |
| ``:-`` | expand DEFAULT if VAR is unset or empty |
| ``=``  | expand and assign DFAULT to VAR if VAR is unset |
| ``:=`` | expand and assign DEFAULT to VAR if VAR unset or empty |
| ``:+`` | If VAR is null or unset, nothing is substituted, otherwise the expansion of DEFAULT is substituted. |
| ``:?`` | expand DEFAULT, write it to stderr, exit non-interactive shell if VAR unset or empty |

6. ``${VAR#PRE} ${VAR##PRE}`` Remove shortest/longest (globbing) matched prefix of the expanded VAR 
7. ``${VAR%SUF} ${VAR%%SUF}`` Remove shortest/longest (globbing) matched suffix of the expanded VAR
8. ``${VAR/PAT/REP} ${VAR//PAT/REP}`` replace first/all occurrence(s) of the pattern
9. ``${VAR:START:LEN}`` 0-indexed substring, START can be like `` -3``(leading space, to avoid misleading ``:-``) or ``(-3)``, if ``LEN`` is negative, then it represent the end index (not included).
10. ``${@:START:LEN}`` or ``${ARRAY[@]:START:LEN}`` like above, LEN cannot be negative here. 
11. Operations can be applied to array ``${ARRAY[@]##PRE}`` 
12. Nested expansion

```bash
#! /bin/bash
l0=value
l1=l0
l2=l1
l3=l2
l4=l3

echo $l0
eval echo \$$l1
eval eval echo \\$\$$l2
eval eval eval echo \\\\$\\$\$$l3
eval eval eval eval echo \\\\\\\\$\\\\$\\$\$$l4
```

## 2.5 Aliases

An alias allows a string to be substituted for a word when it is used **as the first word of a simple command**. The shell maintains a list of aliases that may be set and unset with the **alias** and **unalias** built-in commands. 

The first word of each simple command, if unquoted, is checked to see if it has an alias. If so, that word is replaced by the text of the alias. The first word of the replacement text is tested for aliases, but a word that is identical to an alias being expanded is not expanded a second time. This avoid recursively expand.

Aliases are not inherited by child processes. Bourne shell (**sh**) does not recognize aliases.


# 3 Pattern and Editing

## 3.1 Regular Expressions

Metacharacters are as follows. ``^ $ \< \>`` are called anchors. 

| Operator | Effect    |
| :------- | :-------- |
| .        | Matches any single character. (Wildcard) |
| ?        | The preceding item is optional and will be matched, at most, once. |
| *        | The preceding item will be matched zero or more times. |
| +        | The preceding item will be matched one or more times. |
| {N}      | The preceding item is matched exactly N times. |
| {N,}     | The preceding item is matched N or more times.  |
| {N,M}    | The preceding item is matched at least N times, but not more than M times.  |
| -        | represents the range if it's not first or last in a list or the ending point of a range in a list. |
| ^        | Matches the empty string at the beginning of a line; also represents the characters not in the range of a list. |
| $        | Matches the empty string at the end of a line.  |
| \b       | Matches the empty string at the edge of a word.  |
| \B       | Matches the empty string provided it's not at the edge of a word. |
| \<       | Match the empty string at the beginning of word.  |
| \>       | Match the empty string at the end of word. |

Two regular expressions may be **concatenated**; the resulting regular expression matches any string formed by concatenating two substrings that respectively match the concatenated subexpressions.

Two regular expressions may be joined by the infix operator "|"; the resulting regular expression matches any string matching either subexpression.

Repetition takes precedence over concatenation, which in turn takes precedence over alternation. A whole subexpression may be enclosed in parentheses to override these precedence rules.

In basic regular expressions (BRE) the metacharacters "\?", "\+", "\{", "\|", "\(", and "\)" lose their special meaning; instead use the backslashed versions "\\\?", "\\\+", "\\\{", "\\\|", "\\\(", and "\\\)".

A *bracket expression* is a list of characters enclosed by "\[" and "\]". It matches any **single character** in that list; first character "\^" means matching anything NOT in the list. Within a bracket expression, a *range expression* consists of two characters separated by a hyphen. ``[a-c]`` may mean ``[aBbCc]`` or ``[abc]`` depending on ``LC_ALL`` environment variables. Set ``LC_ALL='C'`` to use C locale.

## 3.2 ``grep``

**grep** searches the input files for lines containing a match to a given pattern list.

``-i`` ignore case, ``-n`` print line number, ``-v`` invert match,  ``-c`` count matched lines, ``-w`` match word

## 3.3 Glob patterns

Asterisk "\*" means any string, question mark "\?" means any single character. Quote them to match them literally.

Like regular expression, globbing support square braces. If the first character within the braces is "!" or "^", any character not enclosed will be matched. To match the dash ("-"), include it as the first or last character in the set. The sorting depends on the current locale and of the value of the LC_COLLATE variable, if it is set.

Character classes also supported. ``[[:CLASS:]]`` where CLASS can be one of:
"alnum", "alpha", "ascii", "blank", "cntrl", "digit", "graph", "lower", "print", "punct", "space", "upper", "word" or "xdigit".

When ``extglob`` is enabled using ``shopt`` builtin, the following extended pattern is allowed. A pattern-list is a list of one or more patterns separated by a "\|".
``?(pattern-list)`` 0 or 1 occurrence
``*(pattern-list)`` 0 or more occurrences
``+(pattern-list)`` 1 or more occurrences
``@(pattern-list)`` one of given patterns
``!(pattern-list)`` anything except one of the given patterns.

## 3.4 ``sed`` for Stream Editing

| Option             | Effect                                     |
| :----------------- | :----------------------------------------- |
| ``-e SCRIPT``      | use string-scripts                         |
| ``-f SCIRPT_FILE`` | use file-script                            |
| ``-n``             | Silent mode.                               |
| ``-V``             | Print version information and exit.        |
| ``-i [SUFFIX]``    | edit file in-place \[or make back-up\]     |
| ``-E``             | Use ERE for pattern matching (BRE default) |

It is recommended that ``a\ c\ i\`` should only be placed at the end of the script. ``c\`` would start a new cycle, and the 

| Command | Result |
| :------ | :----- |
| a\\     | Append text below current line. |
| c\\     | Change text in the current line with new text. Start a new cycle.|
| d       | Delete text. |
| i\\     | Insert text above current line. |
| p       | Print text. Use with ``-n`` to simulate ``grep``. |
| r       | Read a file. |
| s       | Search and replace text. ``s/regex/rep/g`` means replace all occurrences. |
| w       | Write to a file. |

A semicolon (‘;’) may be used to separate most simple commands (except `a\ c\ i\ # e w r` require newline).

Use number and pattern to specify line, and use ``n,m`` or ``/pat1/,/pat2/`` or mixture of previous two to represent line ranges.

## 3.5 ``awk`` Editor

``awk PROGRAM inputfile(s)`` or ``awk -f PROGRAM-FILE inputfile(s)``

```awk
BEGIN { PRE-PROCESS }
/pat/ { PROCESS }
END { POST-PROCESS }
```

| variable | usage                                             |
| -------- | ------------------------------------------------- |
| FS       | Field separator                                   |
| OFS      | How commas is interpreted in print, default space |
| ORS      | How output record is separated, default "\n"      |
| NS       | Number of record                                  |
| NF       | Number of fields                                  |
 
# 4 Redirection

## 4.1 Interactive Scripts

``echo`` flags: ``-n`` suppress trailing newline ``-e`` interprets ANSI-C backslash-escaped characters (i.e. ``\\ \n \t`` etc)

``read [options] NAME1 NAME2 ...``  leftover words and their intervening separators assigned to the last name; remaining names are assigned empty value;

| Option       | Meaning |
| :----------- | :------ |
| ``-a ANAME`` | The words are assigned to sequential indexes of the array variable ANAME, starting at 0. All elements are removed from ANAME before the assignment. Other NAME arguments are ignored. |
| ``-d DELIM`` | The first character of DELIM is used to terminate the input line, rather than newline. |
| ``-e``       | **readline** is used to obtain the line. |
| ``-n N``     | **read** returns after reading N characters rather than waiting for a complete line of input. |
| ``-p PRT``   | Display PRT, without a trailing newline, before attempting to read any input. The prompt is displayed only if input is coming from a terminal. |
| ``-r``       | If this option is given, backslash does not act as an escape character. The backslash is considered to be part of the line. In particular, a backslash-newline pair may not be used as a line continuation. |
| ``-s``       | Silent mode. If input is coming from a terminal, characters are not echoed. |
| ``-t TM``    | Cause **read** to time out and return failure if a complete line of input is not read within TM seconds. This option has no effect if **read** is not reading input from the terminal or from a pipe. |
| ``-u FD``    | Read input from file descriptor FD. |

## 4.2 Redirection and file descriptor

File input and output are accomplished by process-specific integer handles that track all open files for a given process. These numeric values are known as **file descriptors**.

``[n]<word`` redirecting input: open the file whose name results from expansion of word for reading on file descriptor ``n``, if ``n`` not specified, read on file descriptor 0.

``[n]>[|]word`` redirecting output: open the file whose name results from expansion of word for reading on file descriptor ``n``, if ``n`` not specified, read on file descriptor 1. If no "\|" is used, file already exists, and ``noclobber`` is on in shell options, the redirection will fail. 

``[n]>>word`` append to file, file will be created if not exists.

``&>word`` or  ``>&word`` or ``>word 2>&1`` standard output and standard error be redirected to file name that results from word expansion. The second form, word may not expand to a number or '-'.

``&>>word`` or ``>>word 2>&1`` allows both the standard output and the standard error output to be appended to the file whose name is the expansion of word.


```bash
[n]<<[-]word
  here-document
delimiter
```

**Here documents**: read input from the current source until a line containing only word (with no trailing blanks) is seen. 
- No expansion is performed on word. 
- If any part of word is **quoted**, the delimiter is the result of quote removal on word, and the lines in the here-document are **not expanded**. 
- If word is **unquoted**, all lines of the here-document are subjected to expansion, the character sequence `\newline` is ignored, and ‘\’ must be used to quote the characters ‘\’, ‘$’, and ‘`’.
- If hyphen is used, all **leading tab characters are stripped** from input lines and the line containing delimiter.

```bash
# example
yum install $1 << CONFIRM
y
CONFIRM
```


``[n]<<< word`` Here strings: a variant of here documents. Expansion except filename and word splitting is performed. The result is supplied as a single string with a new line appended, to fd n.

``[n]<&m [n]<&word`` duplicate input file descriptor, fd *n* is made to be a copy of fd *m*.

``[n]>&m [n]>&word`` duplicate output file descriptor, fd *n* is made to be a copy of fd *m*.

``n>&- n<&-`` close fd *n*.

`[n]<&digit- [n]>&digit-` copy fd digit to fd n and remove fd digit.

``[n]<>word`` open a file for reading and writing on fd *n*, or 0 if *n* not specified.

It is also often combined with redirection to ``2>/dev/null``.

In Bash script, you can use ``exec m<&n`` to redirect file descriptor to allow ``read`` to switch between reading stdin and reading a file.

```bash
read VAR FROM STDIN
exec 7<&0
exec 0 < /path/to/file
read VAR FROM FILE
exec 0<&7 7<&-

# fd 6 targets fd 1 target (console out) in current shell
exec 6>&1

# fd 1 targets pipe, fd 2 targets fd 1 target (pipe),
# fd 1 targets fd 6 target (console out), fd 6 closed, execute ls
ls "$INPUTDIR"/* 2>&1 >&6 6>&- \
				# Closesechoe fd 6 for awk, but not for ls.
| awk 'BEGIN { FS=":" } { print "YOU HAVE NO ACCESS TO" $2 }' 6>&-

exec 6>&-
```



## 4.3 Catching Signals and Trapping


# 5 Compound Commands

``( list )`` invoke subshell while ``{ list; }`` run in current shell.

## 5.1 ``if`` Conditional Statements

``if TEST-COMMANDS; then CONSEQUENT-COMMANDS; fi``
``if TEST-COMMANDS; then CONSEQUENT-COMMANDS; elif MORE-TEST-COMMANDS; then MORE-CONSEQUENT-COMMANDS; else ELSE-CONSEQUENT-COMMANDS; fi``

The ``TEST-COMMAND`` often involves numerical or string comparison tests, a.k.a. **Bash Conditional Expression**, but it can also be any command that returns a status of **zero when it succeeds** and some **other status when it fails**.

The ``[`` or ``test`` built-in, or the ``[[`` compound command are called Bash conditional expression. They are usually used as TEST-COMMAND. 

## 5.2 Bash Conditional Expressions

``[[ … ]]`` is preferred over ``[ … ]``, ``test`` and ``/usr/bin/[``. ``[[ … ]]`` reduces errors as **no pathname expansion or word splitting** takes place between ``[[`` and ``]]``. In addition, ``[[ … ]]`` allows for pattern and regular expression matching, while ``[ … ]`` does not.  -- Google style guide

The following is 

| Primary                         | Meaning |
| :------------------------------ | :------ |
| ``-a FILE``                     | True if FILE exists. |
| ``-d FILE``                     | True if FILE exists and is a directory. |
| ``-e FILE``                     | True if FILE exists. |
| ``-f FILE``                     | True if FILE exists and is a regular file. |
| ``-h FILE``                     | True if FILE exists and is a symbolic link. |
| ``-p FILE``                     | True if FILE exists and is a named pipe (FIFO). |
| ``-r FILE``                     | True if FILE exists and is readable. |
| ``-s FILE``                     | True if FILE exists and has a size greater than zero. |
| ``-w FILE``                     | True if FILE exists and is writable. |
| ``-x FILE``                     | True if FILE exists and is executable. |
| ``-O FILE``                     | True if FILE exists and is owned by the effective user ID. |
| ``-G FILE``                     | True if FILE exists and is owned by the effective group ID. |
| ``-L FILE``                     | True if FILE exists and is a symbolic link. |
| ``-N FILE``                     | True if FILE exists and has been modified since it was last read. |
| ``-S FILE``                     | True if FILE exists and is a socket. |
| ``FILE1 -nt FILE2``             | True if FILE1 has been changed more recently than FILE2, or if FILE1 exists and FILE2 does not. |
| ``FILE1 -ot FILE2``             | True if FILE1 is older than FILE2, or is FILE2 exists and FILE1 does not. |
| ``FILE1 -ef FILE2``             | True if FILE1 and FILE2 refer to the same device and inode numbers. |
| ``-o OPTIONNAME``               | True if shell option "OPTIONNAME" is enabled. |
| ``-z STRING``                   | True of the length if "STRING" is zero. |
| ``-n STRING`` or ``STRING``     | True if the length of "STRING" is non-zero. |
| ``STRING1 == STRING2``          | True if the strings are equal. "=" may be used instead of "==" for strict POSIX compliance. |
| ``STRING1 != STRING2``          | True if the strings are not equal. |
| ``STRING1 < STRING2``           | True if "STRING1" sorts before "STRING2" lexicographically in the current locale. |
| ``[TRING1 > STRING2``           | True if "STRING1" sorts after "STRING2" lexicographically in the current locale. |
| ``ARG1 OP ARG2``                | "OP" is one of -eq, -ne, -lt, -le, -gt or -ge.\* |
 
\*: These arithmetic binary operators return true if arg1 is equal to, not equal to, less than, less than or equal to, greater than, or greater than or equal to arg2, respectively. Arg1 and arg2 may be positive or negative integers. When used with the ``[[`` command, Arg1 and Arg2 are evaluated as arithmetic expressions.

Expressions may be combined using the following operators, listed in decreasing order of precedence:

| Operation              | Effect |
| :--------------------- | :----- |
| ``[ ! EXPR ]``         | True if **EXPR** is false. |
| ``[ ( EXPR ) ]``       | Returns the value of **EXPR**. Override the normal precedence of operators. |
| ``[ EXPR1 -a EXPR2 ]`` | True if both **EXPR1** and **EXPR2** are true. |
| ``[ EXPR1 -o EXPR2 ]`` | True if either **EXPR1** or **EXPR2** is true. |

## 5.3 ``case`` Conditional Statements

```bash
case EXPRESSION in 
  CASE1) 
  COMMAND-LIST
  ;; 
  CASE2) 
  COMMAND-LIST
  ;; 
  ... 
  CASEN) 
  COMMAND-LIST
  ;; 
esac
```

**Glob patterns** are used here. Unlike other programming language, once enter a case, the following clauses would not be processed. (or you can treat ``;;`` as break)

If there's no command to do in COMMAND-LIST, use ``:`` as an empty command.

## 5.4 Repetitive Tasks

``for NAME [in LIST ]; do COMMANDS; done``
If ``[in LIST]`` is not present, it is replaced with ``in $@``.  ``LIST`` usually can be command substitution, variable substitution, and IFS separated.

``for (( expr1 ; expr2 ; expr3 )) ; do commands ; done``
Like for loop in C. Notice that arithmetic return 1 for true and 0 for false.

``while test-commands; do consequent-commands; done``
Execute consequent-commands as long as test-commands has an exit status of zero.

``until test-commands; do consequent-commands; done``
Execute consequent-commands as long as test-commands has an exit status which is not zero.

For all of them, the return status is the exit status of the last command executed in consequent-commands, or zero if none was executed.

Since the loop construct is considered to be one command structure, the **redirection should occur after** the **done** statement, so that it complies with the form.

```bash
unset a a=() # Explicitly declare as array 

while IFS= read -r -d $'\0' file; 
do 
  a+=("$file") # Appends to array automatically 
done < <(find /tmp -type f -print0)

# could be rewritten as
find /bar -name *foo* -print0 | while read line; do
  ...
done

```

Break and continue can be followed by a number to specify how many layers of loop to break/continue.

## 5.5 ``select`` Commands

``select NAME [in LIST ]; do COMMANDS; done``
1. Expanded LIST would be printed to stderr preceded with corresponding number. 
2. Upon printing all the items, the PS3 prompt is printed and one line from standard input is read.
3. If this line consists of a number corresponding to one of the items, the value of NAME is set to the content of that item. COMMANDS are executed and PS3 is printed again.
4. If the line is empty, the items and the PS3 prompt are displayed again. 
5. If an _EOF_ (End Of File) character is read, (in terminal, press control-D) the loop exits.
6. It's user-friendly to add a word in list to allow breaking the select loop using ``break``.



## 5.6 Functions

``fname () compound-command [ redirections ]`` or ``function fname [()] compound-command [ redirections ]``

``return`` to resume to the next command after the function call. Any command associated with the `RETURN` trap is executed before execution resumes. If a numeric argument is given to `return`, that is the function’s return status; otherwise the function’s return status is the exit status of the last command executed before the `return`.

# 6 Catching Signals


# 7 Miscellanous Commands

## ``getopts``
``shift`` builtin
TODO



## ``date``


## ``dirs`` directory stack

TODO

## ``sort``

TODO

## ``tr``

TODO

## ``find``

TODO
