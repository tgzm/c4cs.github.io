---
---

ln
--
The `ln` command is used to create a hard link or a symbolic link (symlink) to an existing file. Links allow more than one filename to refer to the same file.

<!-- minimal example -->
~~~ bash
$ echo "HelloWorld" >> file.txt
$ cat file.txt 
HelloWorld
$ ln -s file.txt file_sln
# create a symlink to file.txt
$ cat file_sln 
HelloWorld
~~~

<!--more-->

### Specifications

There are mainly two ways of invoking the ln utility.

#### **Single file invocation**
In single file invocation, the `ln` utility creates a new hard link (or Directory entry) for `source_file` by `target_file`. If the -s option is specified, a symbolic link is created.

~~~ bash
ln [-fs] [-L|-P] source_file target_file
~~~


#### **Multiple file invocation**
In multiple file invocation, the `ln` utility creates a new hard link (or Directory entry) for each `source_file_*`, at a destination path in an existing directory `target_dir`.

~~~ bash
ln [-fs] [-L|-P] source_file_1 source_file_2 ... target_dir
~~~

#### **Supported options**

`--backup[=CONTROL]`

make a backup of each existing destination file

`-b`

like --backup but does not accept an argument

`-d`, `-F`, `--directory`

allow the superuser to attempt to hard link directories (note: will probably fail due to system restrictions, even for the superuser)

`-f`, `--force`

remove existing destination files

`-i`, `--interactive`

prompt whether to remove destinations

`-L`, `--logical`

make hard links to symbolic link references

`-n`, `--no-dereference`

treat destination that is a symlink to a directory as if it were a normal file

`-P`, `--physical`

make hard links directly to symbolic links

`-s`, `--symbolic`

make symbolic links instead of hard links

`-S`, `--suffix=SUFFIX`

override the usual backup suffix

`-t`, `--target-directory=DIRECTORY`

specify the DIRECTORY in which to create the links

`-T`, `--no-target-directory`

treat LINK_NAME as a normal file

`-v`, `--verbose`

print name of each linked file

`--help`

display this help and exit

`--version`

output version information and exit

If more than one of the mutually-exclusive options `-L` and `-P` is specified the last option specified determines the behavior of the utility.

If the `-s` option is not specified and neither a `-L` nor a `-P` option is specified, it is implementation-defined which of the `-L` and `-P` options will be used as the default.

If neither target file nor target directory are specified, links will be created in the current working directory.

### Useful Options / Examples

#### `ln -s source_file target_file`

##### **Example**

~~~ bash
$ ln -s file.txt file_sln
~~~

By the above command, a symlink named `file_sln` is created for the existing file `file.txt`. The first character `l` in the output line of `ls -l` command for `file_sln` indicates it's a link file.

~~~ bash
$ ls -l
total 4
lrwxrwxrwx 1 tgzm tgzm  8 Feb 12 10:52 file_sln -> file.txt
-rw-rw-r-- 1 tgzm tgzm 11 Feb 12 10:51 file.txt
~~~

Now `file.txt` can be accessed through `file_sln`.

~~~ bash
$ cat file.txt 
HelloWorld
$ cat file_sln 
HelloWorld
$ echo "HelloAgain" >> file_sln 
$ cat file.txt 
HelloWorld
HelloAgain
$ cat file_sln 
HelloWorld
HelloAgain
~~~

#### `ln source_file target_file`

##### **Example**

~~~ bash
$ ln file.txt file_hln
~~~

By the above command, a hard link named `file_hln` is created for the existing file `file.txt`.
Since a hard link associates `file_hln` with the inode of the source file `file.txt`, the contents can be accessed by `file_hln` even if `file.txt` no longer exists as long as its inode is still valid. However any link file associated with `file.txt` via symlink will be invalidated in such case since a symbolic link is refering to another file by name.

~~~ bash
$ cat file_hln 
HelloWorld
HelloAgain
$ mv file.txt file2.txt
$ cat file_sln
cat: file_sln: No such file or directory
$ cat file_hln 
HelloWorld
HelloAgain
~~~

#### `ln --backup -s source_file target_file`

##### **Example**

~~~ bash
$ echo "This file exists." >> file3.txt
$ ln --backup -s file.txt file3.txt
$ ls
file3.txt  file3.txt~  file_hln  file_sln  file.txt
$ cat file.txt 
HelloWorld
HelloAgain
$ cat file3.txt 
HelloWorld
HelloAgain
$ cat file3.txt~
This file already exists.
~~~

If another file exists with the same name as the new link name, one can use `--backup` option to instruct the `ln` command to make a backup of the original file before creating new link. The backup suffix is '~' by default.

In the above example, the symlink `file3.txt` was associated with `file.txt` after a backup file `file3.txt~` for the original `file3.txt` was created.

Replace `--backup` option with `-f` to overwrite the existing file instead of making a backup for it.