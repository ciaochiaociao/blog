---
title: Typora
---

ref: https://support.typora.io/Add-Custom-CSS/

## Add Custom CSS TLDR

Typora will load CSS files in following order:

1. Typora’s basic styles
2. CSS for current theme
3. `base.user.css` under theme folder
4. `{current-theme}.user.css` under theme folder.

## Custom Source Code Window Width

```css
/* create this base.user.css in theme folder, e.g., <user_folder>\AppData\Roaming\Typora\themes */

#typora-source .CodeMirror-lines {
  max-width: 100%;
}
```

