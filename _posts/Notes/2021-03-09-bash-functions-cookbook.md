---
title: Bash Functions Cookbook
---

## ProgressBar

```
#!/bin/bash

function ProgressBar {
# Process data
    let _progress=(${1}*100/${2}*100)/100
    let _done=(${_progress}*4)/10
    let _left=40-$_done
# Build progressbar string lengths
    _fill=$(printf "%${_done}s")
    _empty=$(printf "%${_left}s")

# 1.2 Build progressbar strings and print the ProgressBar line
# 1.2.1 Output example:
# 1.2.1.1 Progress : [########################################] 100%
printf "\rProgress : [${_fill// /#}${_empty// /-}] ${_progress}%% (${1}/${2})"

}
```

## Remove the Last character

```
sed 's/.$//'

awk 'NR > 1 {print $<num>+0}'
# With the arithmetic expression ($5+0) we force awk to interpret the 5th field as a number, and anything after the number will be ignored.
```

