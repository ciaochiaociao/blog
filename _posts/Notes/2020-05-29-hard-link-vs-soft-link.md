---
title: Hard Link VS Soft Link VS reflink
typora-root-url: ..\..
---

https://blog.usejournal.com/what-is-the-difference-between-a-hard-link-and-a-symbolic-link-8c0493041b62

https://dev.to/robogeek/reflinks-vs-symlinks-vs-hard-links-and-how-they-can-help-machine-learning-projects-1cj4

## Hardlink

A *hard link* is merely an additional name for an existing [file](http://www.linfo.org/file.html) on [Linux](http://www.linfo.org/linuxdef.html) or other [Unix-like](http://www.linfo.org/unix-like.html) [operating systems](http://www.linfo.org/operating_system.html).

- Any number of hard links, and thus any number of names, can be created for any file. Hard links can also be created to other hard links. 

- cannot be created for [directories](http://www.linfo.org/directory.html), and they cannot cross [filesystem](http://www.linfo.org/filesystem.html) boundaries or span across [partitions](http://www.linfo.org/partition.html).

- ```
  ln file1 hlink1
  ls -i  # lists all files with inode.
  
  # INODE Definition: The inode is a data structure in a Unix-style file system which describes a filesystem object such as a file or a directory. Each inode stores the attributes and disk block location(s) of the object’s data.[1] Filesystem object attributes may include metadata (times of last change,[2] access, modification), as well as owner and permission data.[3] Directories are lists of names assigned to inodes. A directory contains an entry for itself, its parent, and each of its children. (Systems derived from BSD use the term vnode (the "v" refers to the kernel's virtual file system layer).)
  ```

## Soft Link

Soft links is a special kind of file that points to another file, much like a shortcut. Unlike a hard link, **a symbolic link does not contain the data in the target file.** It simply points to another entry somewhere in the file system.

	- This difference gives symbolic links certain qualities that hard links do not have, such as the ability to link to directories, or to files on remote computers networked through [NFS](https://kb.iu.edu/d/adux). 
	- when you delete a target file, symbolic links to that file become unusable, whereas **hard links preserve the contents of the file.**
![Hard Links and Symbolic Links](/assets/images/nt9y47pghodhzyixcgb4.png)

## Reflink

This technique means to duplicate a file on the disk such that the “copy” is a “clone” similar to a hard link. Unlike a hard link where two directory entries refer to the same inode entry, with reflinks there are two inode entries, and it is the data blocks that are shared. It happens as quickly as a hard link, but there is an important difference. Any write to the cloned file causes new data blocks to be allocated to hold that data. The cloned file appears changed, and the original file is unmodified.

Reflinks are easily available on Mac OS X, and with a little work is available on Linux. This feature is supported only on certain file systems:

- Linux
  - BTRFS
  - XFS
  - OCFS2
- Mac OS X
  - APFS

![Reflinks](/assets/images/hda2xvpq1068vyjwfx4c.png)

![Results](/assets/images/9duojm0vyjqm5vqnasm3.png)