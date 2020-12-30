---
title: Test Commands in Linux CLI
---

ref: http://mywiki.wooledge.org/BashFAQ/031

## old test command (`[` + `]`)
- `[` is a program from `/bin/[` or `/bin/test`
- `test EXPRESION` = `[ EXPRESSION ]`
- `man test` or `man [`

## new test command (`[[` + `]]`)
- `[[` is not a program but a **keyword**

## Comparison

| **Feature**                                                  | **new test** `[[`                                       | **old test** `[`                                             | **Example**                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| string comparison                                            | `>`                                                     | `\>` [(*)](http://mywiki.wooledge.org/BashFAQ/031#np)        | `[[ a > b ]] || echo "a does not come after b"`              |
| `<`                                                          | `\<` [(*)](http://mywiki.wooledge.org/BashFAQ/031#np)   | `[[ az < za ]] && echo "az comes before za"`                 |                                                              |
| `=` (or `==`)                                                | `=`                                                     | `[[ a = a ]] && echo "a equals a"`                           |                                                              |
| `!=`                                                         | `!=`                                                    | `[[ a != b ]] && echo "a is not equal to b"`                 |                                                              |
| integer comparison                                           | `-gt`                                                   | `-gt`                                                        | `[[ 5 -gt 10 ]] || echo "5 is not bigger than 10"`           |
| `-lt`                                                        | `-lt`                                                   | `[[ 8 -lt 9 ]] && echo "8 is less than 9"`                   |                                                              |
| `-ge`                                                        | `-ge`                                                   | `[[ 3 -ge 3 ]] && echo "3 is greater than or equal to 3"`    |                                                              |
| `-le`                                                        | `-le`                                                   | `[[ 3 -le 8 ]] && echo "3 is less than or equal to 8"`       |                                                              |
| `-eq`                                                        | `-eq`                                                   | `[[ 5 -eq 05 ]] && echo "5 equals 05"`                       |                                                              |
| `-ne`                                                        | `-ne`                                                   | `[[ 6 -ne 20 ]] && echo "6 is not equal to 20"`              |                                                              |
| conditional evaluation                                       | `&&`                                                    | `-a` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2)      | `[[ -n $var && -f $var ]] && echo "$var is a file"`          |
| `||`                                                         | `-o` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2) | `[[ -b $var || -c $var ]] && echo "$var is a device"`        |                                                              |
| expression grouping                                          | `(...)`                                                 | `\( ... \)` [(**)](http://mywiki.wooledge.org/BashFAQ/031#np2) | `[[ $var = img* && ($var = *.png || $var = *.jpg) ]] &&` `echo "$var starts with img and ends with .jpg or .png"` |
| Pattern matching (shell pattern, wild card)                  | `=` (or `==`)                                           | (not available)                                              | `[[ $name = a* ]] || echo "name does not start with an 'a': $name"` |
| [RegularExpression](http://mywiki.wooledge.org/RegularExpression) matching | `=~`                                                    | (not available)                                              | `[[ $(date) =~ ^Fri\ ...\ 13 ]] && echo "It's Friday the 13th!"` |