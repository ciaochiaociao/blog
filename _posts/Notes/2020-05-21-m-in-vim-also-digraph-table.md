---
title: "^M in vim (also digraph table)"
---

ref: https://superuser.com/questions/473614/vim-shows-m-symbols-and-they-arent-disappearing
ref: https://stackoverflow.com/questions/5843495/what-does-m-character-mean-in-vim

The character that vim displays as ^M is CR (carriage return, ASCII character 13). Windows uses both CR and LF (new line, ASCII character 10) to encode line breaks in text files. Linux/Unix use only LF to encode line breaks, and Mac OS uses only CR.

If you open a text file created on a Windows computer on a Linux box, you may see trailing CR characters in each line. There are several ways to remove them. One is to replace them in vim as m000 suggested, another would be to recode the file:


solutions:
 - `:%s/^M//g` in VIM (type `Ctrl`+`V` + `M` or `Ctrl`+`V` `Ctrl`+`Enter` or `Ctrl`+`Q` + `M` in Windows)
 - `:%s/\r//g`
 - `:%s/\r/\r/g` for replacement instead of deleting
 - `recode ibmpc..latin1 SOME.TXT`
- `set ffs=unix,dos` in vim
- 

## Others
http://vimdoc.sourceforge.net/htmldoc/digraph.html#digraph-table


### VIM digraph table
```
    | How to type         | In search, means:       | In replacement, means:
----------------------------------------------------------------------------
\n  | \n                  | End-of-line             | <Nul> 0x0
^@  | CTRL-V CTRL-J       | <Nul> 0x0               | <Nul> 0x0
\r  | \r                  | Carriage return 0xD     | "Break the line here"
^M  | CTRL-Enter          | Carriage return 0xD     | "Break the line here"
\^M | \ CTRL-V CTRL-ENTER | \ + carriage return 0xD | Carriage return 0xD
```