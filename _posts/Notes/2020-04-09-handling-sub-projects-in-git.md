---
title: Handling SubProjects in Git
---

Sometimes, we will have multiple projects that are related to each other. And one of the cases is that a project has subprojects inside itself. It often happens when the main project have some functions that are refactored/modularized and created as a repository in Git. 

There are few ways to handle sub-projects in a parent project in Git:
1. `git submodule`
2. `git subtree`
3. ...


## `git submodule`

ref: https://www.youtube.com/watch?v=UQvXst5I41I
  - `git submodule add <url of that repo> <folder name>`
    - add `.gitsubmodules` file
    in `git status`, it will also show `new "file": <repo>` 
      - note that the staged is not a folder. It only stages the "commit" the submodule <repo> is referenced to instead of its content. That's why it does not show "folder" but instead "file".
  - `git push` in parent module does not `push` the child's module. You have to do it separately.

## Workflow:

### clone projects containing submodules
```
git clone <repo> <path>
git submodule init
git submodule update

# or just
git clone <repo> <path>
git submodule update --init  ## pull for the first time

# or even simpler
git clone --recurse <repo> <path>
```

### Workflow of creating a project with submodule : 
```
  vvv `git submodule add --name <name> <repo> <path>` vvv
1. `new file: <repo>`
  vvv `git commit; git submodule status` vvv
    <commit> <repo>
    ...
  vvv `cd <repo>; # and make changes` vvv
2. `modified: <repo> (modified content)`
  vvv `cd <repo>; git add .; git commit` vvv
3. `modified: <repo> (new commits)`
  vvv `git add <repo>` vvv
4. `modified: <repo>`
  vvv `git commit` vvv
    clean
  vvv `cd <repo>; git push; cd ..; git push ` vvv
```

## Commonly used commands:

```
$ git submodule add <url> <path>  # Add the given repository as a submodule at the given path to the changeset to be committed next to the current project
$ git submodule init  # Initialize the submodules recorded in the index by setting submodule.$name.url in .git/config. It uses the same setting from .gitmodules as a template.

$ git submodule update  # same as git submodule update --checkout  # Update the registered submodules to match what the superproject expects by cloning missing submodules, fetching missing commits in submodules and updating the working tree of the submodules.
$ git submodule update --rebase/--merge

$ git submodule update --remote --checkout/--rebase/--merge  # Instead of using the superproject’s recorded SHA-1 to update the submodule, use the status of the submodule’s remote-tracking branch.
 Use this option to integrate changes from the upstream subproject with your submodule’s current HEAD. ( Alternatively, you can run git pull from the submodule, which is equivalent except for the remote branch name: update --remote uses the default upstream repository and submodule.<name>.branch, while git pull uses the submodule’s branch.<name>.merge. Prefer submodule.<name>.branch if you want to distribute the default upstream branch with the superproject and branch.<name>.merge if you want a more native feel while working in the submodule itself.)
 
$ git submodule update --recursive --remote = git pull --recurse-submodules


$ git submodule sync  # Synchronizes submodules' remote URL configuration setting to the value specified in .gitmodules. This is useful when submodule URLs change upstream and you need to update your local repositories accordingly.

$ git submodule status  # print the SHA-1 of the currently checked out commit for each submodule, along with the submodule path and the output of git describe for the SHA-1.
# Each SHA-1 will possibly be prefixed with 
#	- if the submodule is not initialized
#	+ if the currently checked out submodule commit does not match the SHA-1 found in the index of the containing repository and 
#	U if the submodule has merge conflicts.

$ git submodule summary  # Show commit summary between the given commit (defaults to HEAD) and working tree/index.
```

`.gitmodules` will be created with contents like
```
[submodule "libs/flair"]
	path = libs/flair
	url = https://github.com/flairNLP/flair.git
	
[submodule "libs/conlleval"]
	path = libs/conlleval
	url = https://github.com/sighsmile/conlleval.git

```

When using `git status` in the parent module, the submodule status will be shown as a **file** instead of a directory.
```
Changes not staged for commit:
modified:   libs/ner_error_analysis (modified content, untracked content)
or
modified:   libs/ner_error_analysis (new commit)

```
The **file** only stores the reference to the commit of the subrepository. `git add/commit` that **file** and  in the parent to update the reference.
```
# in parent module
git add <path/to/submodule>
git commit -m '...'
git push origin/master:master
```

## git remove submodules
ref: https://gist.github.com/myusuf3/7f645819ded92bda6677

```
1. 
git submodule deinit <path_to_submodule>
git rm <path_to_submodule>
git commit -m "Removed submodule "
rm -rf .git/modules/<path_to_submodule>

2. To remove a submodule you need to:

Delete the relevant section from the .gitmodules file.
Stage the .gitmodules changes git add .gitmodules
Delete the relevant section from .git/config.
Run git rm --cached path_to_submodule (no trailing slash).
Run rm -rf .git/modules/path_to_submodule (no trailing slash).
Commit git commit -m "Removed submodule "
Delete the now untracked submodule files rm -rf path_to_submodule
```