This file is example realisation of setting for FM ranger. see ranger on githab
Also it have ideas for FM.

# open from + dynamical navigation

Will be cool if we have possibility start file from folder with out cd in folder and open tab

Open any from (same folder) on hot key
dynamical window of commands hot keys

=> appear window on hotkeys and alphabetical result ls in folder

on hotkey 'key1':
fa       ...folder1
fb       ...folder2
aa       file1
ab       file2

press fb:
fa       ...child folder1 of fa
aa       file.txt

return last opened window for folder available on hotkey 'key2':
fa       ...child folder1 of fa
aa       file.txt

Something similar is implemented by a qtbrowser for links

# Handling crazy file/directory names in console

I have not yet met a any one FM that would well implement console processing (especially console). Probably there are none yet. Yes, this is a difficult task.
```
"file ' 1 " => normal
'file " 1 ' => normal
"file " 1 ' => crazy
"file " 1 " => crazy
```

## Ranger-console handling file names with canonical style and other styles

Why classic(canonical) method is not good idea? Because:
1. it limited using the file. Of cause for console we can use terminal (system shell) method. But it is not comfortably.
2. it is inappropriate use of space:

```
command      'file1'     "file 2" target
```

But there are few people who write commands like this (more spaces), using their spaces inappropriately.

Yes, if we keep a list of file names with us, we can definitely get the names in any form and time for us. For the console, we can use a clean name. For shell we can wrap in quotation marks.

What happens when we hit the visible console? We display files if the specified qualifier is %s also we display commands and command-arguments.

We highlighted two files.
As an example, you need to copy two files to a certain directory. We want to give the opportunity to edit the directory name. We will show the copy command and file names in the console.

How can we do this?
```
perfectly: ranger-command 'file 1' 'file 2' 'target_directory'
perfectly: ranger-command "file 1" "file 2" "target_directory"
good:      ranger-command file\ 1 file\ 2 target_directory

perfectly: shell command 'file 1' 'file 2' 'target_directory'
perfectly: shell command "file 1" "file 2" "target_directory"
good:      shell command file\ 1 file\ 2 target_directory
```

But what if second file has a name?: `" '" '1`
```
no:   ranger-command 'file 1' '" '" '1' 'target_directory'
no:   ranger-command "file 1" "" '" '1" "target_directory"
good: ranger-command file\ 1 \"\ \'\"\ \'1 target_directory

no:   shell command 'file 1' '" '" '1' 'target_directory'
no:   shell command "file 1" "" '" '1" "target_directory"
good: shell command file\ 1 \"\ \'\"\ \'1 target_directory
```

How to show files to the user? Only the 3rd method remains. But this method is uncomfortable.
Of course the space is escaped. But besides the space, a lot of ascii symbols are escaped:
```
,[];'`<>?{}:"$^&|\
```
You can easily check it in your terminal:
```
~$ touch 'abcdefghijklmnopqrstuvwxyz,.[];'`ABCDEFGHIJKLMNOPQRSTUVWXYZ<>?{}:"~@#$^&|\'
~$ cat a
```
and press Tab
and see:
```
abcdefghijklmnopqrstuvwxyz\,.\[\]\;\'\`ABCDEFGHIJKLMNOPQRSTUVWXYZ\<\>\?\{\}\:\"~@#\$\^\&\|\\
```

There are quite a few characters.

For RU and UA alphabetic escapes only symbols same acii:
```
~$ touch 'фисвуапршолдьтщзйкыіегмцчнябю.хъїжэєёФИСВУАПРШОЛДЬТЩЗЙКЫІЕГМЦЧНЯБЮ,ХЪЇЖЭЄЁ"№;:?\'
~$ cat ф
```
and press Tab
and see:
```
фисвуапршолдьтщзйкыіегмцчнябю.хъїжэєёФИСВУАПРШОЛДЬТЩЗЙКЫІЕГМЦЧНЯБЮ\,ХЪЇЖЭЄЁ\"№\;\:\?\\
```

Those for RU and UA, same ascii are shielded.

What about other languages?
It doesn't matter what language characters are used. Escape need up-ascii characters if you use utf-8 locale.

What about other terminals / shells? Need to check.

But what if use delimiter method for all files in console?
```
perfect: ranger-command file 1‿" '" '1‿target_directory
perfect: shell command file 1‿" '" '1‿target_directory
```
Compare with the terminal method, which is better for perception?:
```
good:    shell command file\ 1 \"\ \'\"\ \'1 target_directory
```
You can create one method for translate get file names into console, and create one method for translate file names from console.

In post-console processing, there is no need to map file names to list file names, since we can get the names from the console. You can handle absolutely any name.

This allows you to use file names for the ranger command:
```
:mkdir dir 1‿dir 2
```

console shell (quantifier conversion on hot key (shortcut key) and pasting/deleting in console)
console (quantifier conversion on hot key (shortcut key) and pasting/deleting in console)

Ok. What about this?
```
:ranger-command arg1 file 1‿" '" '1‿/target_directory
:shell command -arg1 file 1‿" '" '1‿/target_directory
```
-arg1 is file name or is not? What about the command?

And if thus?
```
:shell command -arg1      file 1‿" '" '1‿/target_directory
```
spaces is a part of file name?
or thus:
```
:shell command -arg1      file 1‿" '" '1‿../file 3 | command2 > file 4
```

If we mix it from various sources, we know for sure what is what. But as soon as it all falls into the console visible and editable by the user, we must be guided by certain rules, otherwise in post-processing we will not know what is what.

Yes, -arg1 is not a part of file name, because we marked it as part of command, using hot key with Ctrl or when we construct a console line with %s, %t etc.
Yes, spases are part of file, because command of parts of command are surrounded by one space on both sides (not first command).
```
:shell command -arg1      file 1‿" '" '1‿../file 3 | command2 > file 4
 ^^^^^^^^^^^^^^^^^^^^___________S_______S_________^^^^^^^^^^^^^^______
```
Since we need to enter an additional line to define parts of the command and file delimiter, we do not need to enter additional characters in the visible line.
```
:shell command -arg1      file 1 " '" '1 ../file 3 | command2 > file 4
 ^^^^^^^^^^^^^^^^^^^^___________S_______S_________^^^^^^^^^^^^^^______
```
Since it is important for us to separate file names, we can indicate them.
```
:shell command -arg1      file 1 " '" '1 ../file 3 | command2 > file 4
 ^^^^^^^^^^^^^^^^^^^^           S       S         ^^^^^^^^^^^^^^
```
The other side of the coin:
1. we need to synchronize two console lines all the time(both are shown).
2. we need to sign hotkeys to indicate parts of the command and file delimiters.

Instead 2-nd console line we can keep list of name-space positions, but a string stored method is better. But we need to highlight the separating spaces of the files in order to distinguish from the space inside the file name.

Usually the first component of the console line is a command. And entering simple commands with one file name is not difficult, it will not differ from the classical method. This is also the same for shell.
Do I need to enter quantifiers in the console? The answer is not clear, but it would be better not to. This will complicate the processing of the console, and you need to enter another notation and a hot key for quantifiers. Methods for copying and deleting files and/or paths (%f, %s, %t, %p) are quite enough.


## Separation principle for processing commands and file names (composite console)

This principle is based on the separation of commands and data (file names).

It can be implemented in different ways. Initially, commands are entered via ";" is entered and a new field is entered data (or empty is for no data). Also can be inputting data initially and next inputting comands. It is used quite rarely, as it is not convenient for fixing errors, and intuitively not convenient, especially for console history.

The second implementation method is to use two or more separate input forms. In one, commands are entered (via ';') in other data. In comparison with the previous one, this way is used more often, and should include horizontal forms that dynamically change their sizes (no more free forms) or are vertical version with fixing size. Forms are also will created dynamically. Not convenience is that you need to switch between forms, but it will be the advantage (see next). If last form is empty, the next form is no longer created, or forms will be created at the command information or manually.

Examples:

```
:shell command -arg1; command2 > :     file 1‿" '" '1‿../file 3 : file 4    :
______________1st form____________ ___________2nd form___________ _3rd form__
```

```
:@shell; command -arg1; command2 > :     file 1‿" '" '1‿../file 3 : file 4    :
______________1st form____________ ___________2nd form___________ _3rd form__
```

and vertical version:
```
c:@shell; command -arg1; command2 > 
______________1st form___________

1:     file 1‿" '" '1‿../file 3
___________2nd form___________

2: file 4
_3rd form__
```

Simple command with double Enter:
```
:mark_tag :
____1st___ _2nd_
```

Simple command with double Enter and data:
```
:mkdir : dir1
__1st__ _2nd_
```

@ (or {@} etc) — use with all (master command)

More complicated implementation compared to other methods.

Typically, data is specified after the command, but there are complex commands where this is not so. In this case, a data position quantifier is used for the desired command.

If the same data needs to be applied in two commands, a repetition quantifier is also used (similar to string formatting method). It is also possible to use a quantifier form data. This complicates the use of this method.
The advantages of the method can be reproduced in history as a commands and data and as only a commands. Also suggests implement switching between it. This allows you to delete data less and immediately enter new ones.
It implies hot keys for moving between forms, even the FM window (usually selection and tagging mode only, moving cursor for navigation with return window back after an executing of command).

Methods may involve manually creating a form using the hotkey.

Despite the disadvantages of the console handling method, it is one of the best among the others nowadays.

Something like this.
```
def show_console(comands, *data_forms)
    "show console"
    ...

def dtc(spec, data_form=temp, position=None, delim='‿')
    "data to console:  _s1 => dtc(s, 1) #or %s1 insted _s1
    	  _s    => dtc(s, temp, None) #insert for temp form with replacing
    	  _s1_0 => dtc(s, 1, 0) #insert in position 0
    	  _s1_0_ => dtc(s, 1, 0, None) #insert with concatenate via ''
	  _s1_ => dtc(s, 1, None) #replace all content of form
    "
    ...
```

### copy files to folder

Suppose we have a file copy method similar to the copy command of the system console.
```
#(inputting file names or hotkeys)
c:cp
1:file1‿file2‿/home/user/dir1/1

#(concatenate data inputting)
c:cp
1:file1‿file2
2:/home/user/dir1/1

#(form indicates)
c:cp _1 _2
1:file1‿file2
2:/home/user/dir1/1

#(with %s quantifier for 1-st form with showing)
c:_s1; cp _1 _2
1:
2:/home/user/dir1/1
#rc.conf
-> console("_s1; cp _1 _2", "", "/home/user/dir1/1")

#(with %s quantifier for 1-st form without pressed C-Enter and showing but manually created form N2)
c:_s1; cp _1 _2
2:/home/user/dir1/1
#rc.conf
-> console("_s1; cp _1 _2", None, "/home/user/dir1/1")

#(with %s quantifier using temp console buffer)
c:_s; cp _ _1
1:/home/user/dir1/1
#rc.conf
-> console("_s; cp _ _1", "/home/user/dir1/1")

#(with %s quantifier for 1-st form into 0-position and concatenation with ‿ by default)
c:_s1_0; cp
1:/home/user/dir1/1
#rc.conf
-> console("_s1_0; cp", "/home/user/dir1/1")
```

### copy files into two folder

```
#(with %s and 1-st with showing as one composite comand)
c:_s1; @cp _1 _2; @cp _1 _3
1:
2:/home/user/dir1/1
3:/home/user/dir1/2

#(with %s and 1-st without showing)
c:_s1; @cp _1 _2; @cp _1 _3
2:/home/user/dir1/1
3:/home/user/dir1/2

#(with %s and temp buffer form)
c:_s; @cp _ _1; @cp _ _2
1:/home/user/dir1/1
2:/home/user/dir1/2


#(with replace command line from external file.command using command @)
#./file.command
#~$ cat ./file.command
#>_s; @cp _ _1; @cp _ _2
#>_1=/home/user/dir1/1
#>_2=/home/user/dir1/2
#or
#~$ cat ./file.command
#>_s; cp _ _1
#>1=/home/user/dir1/1
#>_s; cp _ _2
#>_2=/home/user/dir1/2
# ranger: cursor on file.command
c:@; {f}
```

## The use of `//`
Typically, `//` is not used to separate file names directly in the console. But can used in the history of storing names as a string with subsequent replacement in the console.