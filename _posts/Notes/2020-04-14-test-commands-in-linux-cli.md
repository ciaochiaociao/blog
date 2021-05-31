---
title: Test Commands in Linux CLI
---

ref: 
- http://mywiki.wooledge.org/BashFAQ/031
- https://www.computerhope.com/unix/test.htm
- `man bash`


## old test command (`[` + `]`)

- `[` is a program from `/bin/[` or `/bin/test`
- `test EXPRESION` = `[ EXPRESSION ]`
- `man test` or `man [`

**test** exits with the status determined by EXPRESSION. Placing the EXPRESSION between square brackets (**[** and **]**) is the same as testing the EXPRESSION with **test**. To see the exit status at the command prompt, [echo](https://www.computerhope.com/unix/uecho.htm) the value "**$?**" A value of 0 means the expression evaluated as true, and a value of 1 means the expression evaluated as false.

### Syntax

```
test EXPRESSION
[ EXPRESSION ]
```

Expressions take the following forms:

| **(** *EXPRESSION* **)**                                     | *EXPRESSION* is true                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **!** *EXPRESSION*                                           | *EXPRESSION* is false                                        |
| *EXPRESSION1* **-a** *EXPRESSION2*                           | both *EXPRESSION1* and *EXPRESSION2* are true                |
| *EXPRESSION1* **-o** *EXPRESSION2*                           | either *EXPRESSION1* or *EXPRESSION2* is true                |
| **-n** *[STRING](https://www.computerhope.com/jargon/s/string.htm)* | the length of *STRING* is nonzero                            |
| *STRING*                                                     | equivalent to **-n** *STRING*                                |
| **-z** *STRING*                                              | the length of *STRING* is zero                               |
| *STRING1* **=** *STRING2*                                    | the strings are equal                                        |
| *STRING1* **!=** *STRING2*                                   | the strings are not equal                                    |
| *INTEGER1* **-eq** *INTEGER2*                                | *INTEGER1* is equal to *INTEGER2*                            |
| *INTEGER1* **-ge** *INTEGER2*                                | *INTEGER1* is greater than or equal to *INTEGER2*            |
| *INTEGER1* **-gt** *INTEGER2*                                | *INTEGER1* is greater than *INTEGER2*                        |
| *INTEGER1* **-le** *INTEGER2*                                | *INTEGER1* is less than or equal to *INTEGER2*               |
| *INTEGER1* **-lt** *INTEGER2*                                | *INTEGER1* is less than *INTEGER2*                           |
| *INTEGER1* **-ne** *INTEGER2*                                | *INTEGER1* is not equal to *INTEGER2*                        |
| *FILE1* **-ef** *FILE2*                                      | *FILE1* and *FILE2* have the same device and [inode](https://www.computerhope.com/jargon/i/inode.htm) numbers |
| *FILE1* **-nt** *FILE2*                                      | *FILE1* is newer (modification date) than *FILE2*            |
| *FILE1* **-ot** *FILE2*                                      | *FILE1* is older than *FILE2*                                |
| **-b** *FILE*                                                | *FILE* exists and is [block](https://www.computerhope.com/jargon/b/block.htm) special |
| **-c** *FILE*                                                | *FILE* exists and is [character](https://www.computerhope.com/jargon/c/charact.htm) special |
| **-d** *FILE*                                                | *FILE* exists and is a [directory](https://www.computerhope.com/jargon/d/director.htm) |
| **-e** *FILE*                                                | *FILE* exists                                                |
| **-f** *FILE*                                                | *FILE* exists and is a regular file                          |
| **-g** *FILE*                                                | *FILE* exists and is set-group-ID                            |
| **-G** *FILE*                                                | *FILE* exists and is owned by the effective group ID         |
| **-h** *FILE*                                                | *FILE* exists and is a [symbolic link](https://www.computerhope.com/jargon/s/symblink.htm) (same as **-L**) |
| **-k** *FILE*                                                | *FILE* exists and has its sticky bit set                     |
| **-L** *FILE*                                                | *FILE* exists and is a symbolic link (same as **-h**)        |
| **-O** *FILE*                                                | *FILE* exists and is owned by the effective user ID          |
| **-p** *FILE*                                                | *FILE* exists and is a named pipe                            |
| **-r** *FILE*                                                | *FILE* exists and read [permission](https://www.computerhope.com/jargon/p/permissi.htm) is granted |
| **-s** *FILE*                                                | *FILE* exists and has a size greater than zero               |
| **-S** *FILE*                                                | *FILE* exists and is a socket                                |
| **-t** *FD*                                                  | file descriptor *FD* is opened on a terminal                 |
| **-u** *FILE*                                                | *FILE* exists and its set-user-ID bit is set                 |
| **-w** *FILE*                                                | *FILE* exists and write permission is granted                |
| **-x** *FILE*                                                | *FILE* exists and execute (or search) permission is granted  |

Except for **-h** and **-L**, all *FILE-related* tests dereference symbolic links. Beware that parentheses need to be escaped (e.g., by backslashes) for shells. **INTEGER** may also be **-l STRING**, which evaluates to the length of **STRING**.

**NOTE:** your shell may have its own version of **test**, which usually supersedes the version described here. Please refer to your shell's documentation for details about the options it supports.

## new test command (`[[` + `]]`)
- `[[` is not a program but a **keyword**

## Comparison

| **Feature**                                                  | **new test** `[[` | **old test** `[`                                             | **Example**                                                  |
| ------------------------------------------------------------ | ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| string comparison                                            | `>`               | `\>` [(*)](http://mywiki.wooledge.org/BashFAQ/031#np)        | `[[ a > b ]] || echo "a does not come after b"`              |
|                                                              | `<`               | `\<` [(*)](http://mywiki.wooledge.org/BashFAQ/031#np)        | `[[ az < za ]] && echo "az comes before za"`                 |
|                                                              | `=` (or `==`)     | `=`                                                          | `[[ a = a ]] && echo "a equals a"`<br />`[[ "var1" = "var2" ]] # double quote is needed sometimes (weird?)` |
|                                                              | `!=`              | `!=`                                                         | `[[ a != b ]] && echo "a is not equal to b"`                 |
| integer comparison                                           | `-gt`             | `-gt`                                                        | `[[ 5 -gt 10 ]] || echo "5 is not bigger than 10"`           |
|                                                              | `-lt`             | `-lt`                                                        | `[[ 8 -lt 9 ]] && echo "8 is less than 9"`                   |
|                                                              | `-ge`             | `-ge`                                                        | `[[ 3 -ge 3 ]] && echo "3 is greater than or equal to 3"`    |
|                                                              | `-le`             | `-le`                                                        | `[[ 3 -le 8 ]] && echo "3 is less than or equal to 8"`       |
|                                                              | `-eq`             | `-eq`                                                        | `[[ 5 -eq 05 ]] && echo "5 equals 05"`                       |
|                                                              | `-ne`             | `-ne`                                                        | `[[ 6 -ne 20 ]] && echo "6 is not equal to 20"`              |
| conditional evaluation                                       | `&&`              | `-a` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2)      | `[[ -n $var && -f $var ]] && echo "$var is a file"`          |
|                                                              | `||`              | `-o` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2)      | `[[ -b $var || -c $var ]] && echo "$var is a device"`        |
| expression grouping                                          | `(...)`           | `\( ... \)` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2) | `[[ $var = img* && ($var = *.png || $var = *.jpg) ]] &&` `echo "$var starts with img and ends with .jpg or .png"` |
| Pattern matching (shell pattern, wild card)                  | `=` (or `==`)     | (not available)                                              | `[[ $name = a* ]] || echo "name does not start with an 'a': $name"` |
| [RegularExpression](http://mywiki.wooledge.org/RegularExpression) matching | `=~`              | (not available)                                              | `[[ $(date) =~ ^Fri\ ...\ 13 ]] && echo "It's Friday the 13th!"` |

## Others

```bash
# work
[[ -n "$var" ]]

# work
[[ -n $var ]]

# work
[ -n "$var" ]

# does not work
[ -n $var ]
```

