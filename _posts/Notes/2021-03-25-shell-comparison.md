---
title: Shell Comparison
---

| shell                | bash                                                         | fish                                                         | zsh                    |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------- |
| support bash         | Yes                                                          | False                                                        | Yes                    |
| Here Doc/String      | Yes                                                          | No, just use `echo ... | ...`                                | Yes                    |
| Process Substitution | Yes `... <(...)`                                             | Yes, use `... (... | psub)`                                  | Yes                    |
| alias/abbr           | alias                                                        | abbr -s<br />alias -l<br />functions                         | alias                  |
| variable assignment  | `VAR=value`<br />`export VAR=value`<br />(`set` is used for changing the values of shell options) | `set [-g/-U] VAR value`<br />erase by `set -e MyVariable`    | `set`                  |
| export               | `export`                                                     | `set -x MyVariable SomeValue`                                |                        |
| prompt               | `$PS1`                                                       | `function fish_prompt; echo "New Prompt % "; end`            | `$PS1`                 |
| `$PATH`              | colon-delimited string                                       | list<br />`set PATH /usr/local/bin /usr/sbin $PATH`<br />`fish_add_path /usr/local/bin` (prepend)<br />`set PATH $PATH /usr/local/bin` (append) | colon-delimited string |
| Command Substitution |                                                              | `(...)`                                                      |                        |
| exit status          | `$?`                                                         | `$status`                                                    |                        |
|                      |                                                              | `if ... ; else if ...; else ...; end`<br />`switch (); case ...; case ...; end`<br />`for ...; ...; end` |                        |

