## Essentials commands

### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Get info about your OS (some examples)</ins>

```sh
# Find kernel version
$   uname -r

# Output value of kernel parameter
$   cat /proc/sys/net/ipv4/ip_forward

# Get current timezone
$   date +%Z
$   cat /etc/timezone
```

&nbsp;

### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create and manage hard & soft links</ins>

```sh
# Hard Link
$   ln <TARGET_FILE_D> <LINK_FILE>

# Soft Link ('-s' argument)
$   ln -s <TARGET_FILE_D> <LINK_FILE>


# Absolute path from shortcut
$   readlink <LINK_FILE>
```

&nbsp;

### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins> List, set, and change standard file permissions</ins>

```sh
# Changing group
$   chgrp <GROUP_NAME> <TARGET_FILE_D_U>

# List groups
$   groups


# Changing owner
$   chown <USER_NAME> <TARGET_FILE_D>

# Changing owner and group
$   chown <USER_NAME>:<GROUP_NAME> <TARGET_FILE_D>
```

<br/>

#### 🔖 <ins>File & Directory permissions</ins>

Based on the following output :
* The 1st character shows what **type of entry** this is : *file*, *special file*, *directory*, etc.  
* The others represents the **Read**, **Write**, **Execute** permissions for owner, group, others.

```sh
$   ls -l
-rwxrwxrw-. 1 aaron family 49 Oct 27 14:41 family_dog.jpg
```

<div align="center">
  <table>
    <tr>
      <th>File Type</th>
      <th>Identifier</th>
    </tr>
    <tr>
      <td>DIRECTORY</td>
      <td align="center">d</td>
    </tr>
    <tr>
      <td>REGULAR FILE</td>
      <td align="center">-</td>
    </tr>
    <tr>
      <td>CHARACTER DEVICE </td>
      <td align="center">c</td>
    </tr>
    <tr>
      <td>LINK</td>
      <td align="center">l</td>
    </tr>
    <tr>
      <td>SOCKET FILE</td>
      <td align="center">s</td>
    </tr>
    <tr>
      <td>PIPE</td>
      <td align="center">p</td>
    </tr>
    <tr>
      <td>BLOCK DEVICE</td>
      <td align="center">b</td>
    </tr> 
  </table>
</div>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Changing permissions**</ins>

```sh
# Giving WRITE permission to owner with '+'
$   chmod u+w <TARGET_FILE_D>

# Removing READ permission to owner with '-'
$   chmod u-r <TARGET_FILE_D>


# Setting exact permissions to group with '='
$   chmod g=rwx <TARGET_FILE_D>

# Make empty permissions
$   chmod g= <TARGET_FILE_D> 
```

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Chaining permissions**</ins>
```sh
# user: at least READ and WRITE
# group: only READ
# others: NO PERMISSIONS
$   chmod u+rw,g=r,o= <TARGET_FILE_D> 
```

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <ins>**Octal permissions**</ins>
```sh
$   stat <TARGET_FILE_D>

File: family_dog.jpg
  Size: 49 Blocks: 8 IO Block: 4096 regular file
Device: fd00h/64768d Inode: 52946177 Links: 1
Access: (0640/-rw-r-----) Uid: ( 1000/ aaron) Gid: ( 10/ wheel)
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>SUID, SGID, and sticky bit</ins>

#### 🔖 <ins>SUID (Set User ID bit)</ins>

When setting this bit on a file : it means whenever this file is executed, it is going to be executed as **the owner of the file** (instead of the ID of the person running that file)

```sh
$   man chmod

DESCRIPTION

       ...

       The first digit selects the set user ID (4) and set group ID (2) and restricted deletion  or  sticky  (1)  attributes.
```
```sh
$   ls -l suidfile
-rw-rw-r--. 1 aaron aaron 0 Apr 26 14:41 suidfile

# Before using the 3 digit permissions in octal format, we're going to add a 4th digit, our SUID
$   chmod 4664 suidfile

# We can see a capital S replacing the x bit :  SUID is enabled for this file
# If anybody else were to be able to execute this file, IT WILL BE as the user Aaron
$   ls -l suidfile
-rwSrw-r--. 1 aaron aaron 0 Apr 26 15:00 suidfile

# Now we add the EXECUTE permission to user
$   chmod 4764 suidfile

# When you see capital S, SUID is enabled but there is NO EXECUTE permissions
# When you see lowercase s, SUID and EXECUTE are both enabled.
$   ls -l suidfile
-rwsrw-r--. 1 aaron aaron 0 Apr 26 15:19 suidfile
```

<br/>

#### 🔖 <ins>SGID</ins>
Pretty much the same as the SUID, only it is going to apply for the group on a file.

```sh
$   ls -l sgidfile
-rwsrw-r--. 1 aaron aaron 0 Apr 26 14:41 sgidfile

# We're using the 2 instead of the 4 as the leading digit
$   chmod 2664 sgidfile

$   ls -l sgidfile
-rw-rwSr--. 1 aaron aaron 0 Apr 26 15:00 sgidfile

$   chmod 2674 sgidfile

$   ls -l sgidfile
-rw-rwsr--. 1 aaron aaron 0 Apr 26 15:19 sgidfile
```
```sh
# Creating entry with BOTH leading bits (on user & group), using 6
$   chmod 6664 both

$   ls -l both
-rwSrwSr--. 1 aaron aaron 0 Apr 26 17:21 both
```

<br/>

#### 🔖 <ins>Sticky bit</ins>
It is usually set on directories that **are shared between people** and it's going to **only allow the owner of a file** inside to be able to remove the file.
```sh
$   mkdir stickydir

$   ls -ld stickydir
drwxrwxr-x. 2 aaron aaron 6 Apr 26 20:54 stickydir

# Using leading bit 1
# $ chmod +t stickydir
$   chmod 1777 stickydir

# Sticky bit and EXECUTE are both set, so we have a lowercase t
$   ls -ld stickydir
drwxrwxrwt. 2 aaron aaron 6 Apr 26 20:59 stickydir

# With Uppercase T, execute is not enabled
$   chmod 1666 stickydir

$   ls -ld stickydir
drw-rw-rwT. 2 aaron aaron 6 Apr 26 21:24 stickydir
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Search for files</ins>

```sh
# Find JPG files
$   find /usr/share/ -name '*.jpg'
1.jpg 2.jpg 3.jpg

# Find files larger then 10 Mb / 10 b / 10 kb / 10 Gb
$   find /lib64/ -size +10M           # +10c / +10k / +10G
large-file.txt

# Find files that have been modified in the last minute
$   find /dev/ -mmin -1

# Find files that have been modified more than 5 minutes ago
$   find /dev/ -mmin +5

# Find files that have been modified past 24h periods (betwwen 24 and 48h)
$   find /dev/ -mtime 2

# Find without sensitive case
$   find -iname felix

# Find files starting by the letter f
$   find -name "f*"

# Finding content having the 4 leading digit. We don't care about permissions -> 000
$   find . -perm /4000

# Finding file created before 01-01-2020
$   find . -newermt "01/01/2020" -type f
```
```sh
# Permissions: 664 = u+rw,g+rw,o+r
$   find –perm 664       # find files with exactly 664 permissions
$   find –perm u=rw,g=rw,o=r 
$   find –perm -664      # find files with at least 664 permissions
$   find –perm –u=rw,g=rw,o=r 
$   find -perm /664      # find files with any of these permissions
$   find –perm /u=rw,g=rw,o=r 
```

<br/>

#### 🔖 <ins>Example with permissions</ins>

```sh
$ sudo find /var/log/ -perm -g=w ! -perm o=rw -exec ls {} \;

/var/log/lastlog
/var/log/wtmp
/var/log/btmp


$ sudo find /var/log/ -perm -g=w ! -perm o=rw -exec ls -l {} \;

-rw-rw-r-- 1 root utmp 292292 Nov 24 09:34 /var/log/lastlog
-rw-rw-r-- 1 root utmp 768 Nov 24 08:43 /var/log/wtmp
-rw-rw---- 1 root utmp 0 Feb  8  2023 /var/log/btmp
```

<br/>

Linux has a thing called `Change Time` for files. But it is different from `Modified Time` (File creation or edition).

* `Modified Time` refers the time when contents have been modified
* `Change Time` refers to the time when metadata has been changed (data about the file, like permissions).
```sh
# Find files having changed time in the last 5 minutes (when someone has changed permissions)
$   find -cmin -5
```

We can also use search expressions
```sh
$   find -name "f*" -size 512k        # AND operator
$   find -name "f*" -o -size 512k     # OR operator

$   find –not -name "f*"              # NOT operator
$   find \! -name "f*"                # alternate NOT operator
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Compare and manipulate file content</ins>

#### 🔖 <ins>Manipulating file</ins>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Replace content**</ins>

```sh
# userinfo.txt, a file with mispelled Canada
$   cat userinfo.txt
ravi montreal canda  39484859 canda
mary ottawa   canda  39384940 canda


# Into single quotes, we have :
#   - s for substitute command (search and replace)
#   - g for global search (to replace all occurences)
$   sed 's/canda/canada/g' userinfo.txt

ravi montreal canada  39484859 canada
mary ottawa   canada  39384940 canada


# Without 'g' argument, only 1st occurence is changed
$   sed 's/canda/canada/' userinfo.txt

ravi montreal canada  39484859 canda
mary ottawa   canada  39384940 canda
```
- To edit directly in the file, use the `-i` argument (--in-place)
```sh
$   sed –i 's/canda/canada/g' userinfo.txt
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Extract content**</ins>

- `cut` command will extract ony the parts we need from a file. **' '** is the delimiter and **-f** the field we want to extract.

```sh
$   cat userinfo.txt
ravi montreal canda  39484859 canda
mary ottawa   canda  39384940 canda


# To get the 1st column. -f 1 means the 1swt word we want to extract on each line.
$   cut –d ' ' –f 1 userinfo.txt
ravi
mary


# To get the 3rd column
$   cut –d ',' –f 3 userinfo.txt
canda
canda
```
- `uniq` command will get us unique entries from a file.
```sh
# We have the following column
usa
canada
usa
canada
canada


# uniq alone will find the 2 last canada are adjacent so it removes the last line
$   uniq countries.txt
usa
canada
usa
canada


# The solution to get unique entires is to sort the list to get duplicates adjacent before using uniq
$   sort countries.txt
canada
canada
canada
usa
usa

$   sort countries.txt | uniq
canada
usa
```

<br/>

#### 🔖 <ins>Comparing file</ins>
- Using `diff` tool will allow us to see the differences between files.

```sh
# Imagine the following contents

# file 1                            # file 2
Only exists in file 1               Only exists in file 2
Identical line 2                    Identical line 2
Identical line 3                    Identical line 3
Only exists in file 1               Only exists in file 2


# 1c1 tells us line 1 from file 1 is changed in line 1 on file 2
# 4c4 tells us line 4 from file 1 is changed in line 4 on file 2
$   diff file1 file2

1c1
< Only exists in file 1
---
> Only exists in file 2
4c4
< Only exists in file 1
---
> Only exists in file 2
```

- In **LARGE** files, we might have no idea where these lines are situated : **`-c`** will give us a bit of context and see what text is around these areas where the lines are different

```sh
$   diff -c file1 file2

*** file1       2023-07-30 14:46:15.250051872 +0000
--- file2       2023-07-30 14:46:28.329941301 +0000
***************
*** 1,4 ****
! Only exists in file 1
  Identical line 2
  Identical line 3
! Only exists in file 1
--- 1,4 ----
! Only exists in file 2
  Identical line 2
  Identical line 3
! Only exists in file 2
```

- Doing a side by side comparison : use **`-y`** option

```sh
$   diff -y file1 file2

Only exists in file 1                                         | Only exists in file 2
Identical line 2                                                Identical line 2
Identical line 3                                                Identical line 3
Only exists in file 1                                         | Only exists in file 2
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Pagers</ins>
`Pagers` are programs that allow us to open multiple pages of text and navigate through them while on terminal. There are 2 Pagers :

- `less`
- `more`

```sh
$   less /var/log/kern.log
```

```sh
# We move by page, using Space Bar
$   more /var/log/kern.log
```


&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Search text in file with Grep</ins>
```sh
# Finding words having 'ubuntu' or 'Ubuntu'
$   grep -i 'Ubuntu' /var/log/kern.log               # -i for case insensitive

ld (GNU Binutils for Ubuntu) 2.38) #85-Ubuntu SMP Fri Jul 7 15:25:09 UTC 2023 (Ubuntu 5.15.0-78.85-generic 5.15.99)
Jul 30 13:32:59 ubuntu kernel: [  137.547769] CPU: 0 PID: 195 Comm: systemd-udevd 


# Search into all files under a directory and subdirectories
$   grep -r 'Ubuntu' /var/log/
$   grep -ir 'Ubuntu' /var/log/


# Reverse search
$   grep -vi 'Ubuntu' /var/log/kern.log
```

<br/>

#### 🔖 <ins>Using Regex (Regular Expression)</ins>

Regular expressions are built with the help of operators :

- **Basic Regular Expression**
  - **`.`** replaces any character
  -  **`^`** matches the start of the string
  - **`$`** matches the end of the string
  - **`*`** matches zero or more times the preceding character
  - **`+`** matches 1 or more times the preceding character
  - **`?`** matches 0 or 1 times the preceding character
  - **`\`** Escape character, represents special characters
  - **`|`** is used as an OR operator

<br/>

- **Bracket Expression**
  - **`()`** Groups regular expressions
  - **`{}`** - Curly braces are used for quantifiers to specify the number of occurrences.
  - **`[]`** Matches any character within the brackets
  - **`[^]`** - **Negation**. For example, [^0-9] matches any character without a digit.


<br/>

- **Backslash Expression**
  - **`\b`** : Match a word boundary  
  - **`\>`** : Match an empty string at the end of a word.
  - **`\<`** : Match an empty string at the beginning of a word.
  - **`\s`** : Match a space.
  - **`\w`** : Match a word.

```sh
# Find words ending with  (KEY_WORD$ or KEY_WORD\>)
$   grep '7$' /etc/login.defs
$   grep '7\>' /etc/login.defs

PASS_WARN_AGE   7
UMASK           077


# Find words beginning with  (^KEY_WORD)
$   grep '^U' /etc/login.defs

UID_MIN                  1000
UID_MAX                 60000
UMASK           077
USERGROUPS_ENAB yes


# +: Match The Previous Element (0) one Or more Times  (Don't forget escaped character \)
# ===================================================
$   grep -r '0\+' /etc/login.defs

PASS_MIN_DAYS   0
UID_MIN                  1000
UID_MAX                 60000
SYS_UID_MIN               201
UMASK           077
```
```sh
# Finding text beginning with / ,  has 0 or more occurences for any characters (due to .) , and ends with a /
# *: Match The Previous Element 0 Or More Times
$   grep -r '/.*/' /etc/

/etc/man_db.conf:# before /usr/man.
/etc/man_db.conf:MANDB_MAP /usr/share/man
/var/cache/man
```

<br/>

#### 🔖 <ins>Extended Regular Expressions</ins>

```sh
# Finding all strings that contain at least three zeros  -> 0{3,}
$   egrep -r '0{3,}' /etc/login.defs
UID_MIN                  1000
UID_MAX                 60000


# Finding all strings that contain 1 , followed by, at least three zeros  -> 10{3,}
$   egrep -r '10{3,}' /home/cento/toto.log
UID_MIN                  1000
GID_MIN                  1000


$   egrep -r '1/0{3,}' /home/cento/toto.log
UID_TOTO_TEST            1/000


# Finding all strings matching EXACTLY 6 followed by three zeros  -> 60{3}
$   egrep -r '60{3}' /home/cento/toto.log
UID_MAX_TOTO_TEST        6000
GID_MAX                 60000


# Finding all strings matching EXACTLY 6, terminating by three zeros  -> 60{3}$
$   egrep -r '60{3}$' /home/cento/toto.log
UID_MAX_TOTO_TEST        6000


# Finding all strings that having 3 or 4 zeros  -> 0{3,4}
$   egrep -r '0{3,4}' /home/cento/toto.log
UID_MIN                  1000
UID_TOTO_TEST            1/000
UID_MAX                 60000
```
```sh
# Finding all strings matching 'disable' or 'disabled'  -> last 'd' is optional
$   egrep -r 'disabled?' /etc/
/etc/vmware-tools/tools.conf.example:# Set to true to disable the 
/etc/vmware-tools/tools.conf.example:#disabled=false

# Match one thing or the other  -> |
$   egrep -r 'enabled|disable?' /etc/
/etc/dleyna-server-service.conf:# If netf is enabled but the list is 
empty, it behaves as disabled.
```
```sh
# Words having /dev/, followed by any characters having a or b or ... to z
$ egrep -r '/dev/[a-z]*' /etc/
/etc/smartmontools/smartd.conf:#/dev/twl0 -d 3ware,1 -a -s L/../../2/03
/etc/smartmontools/smartd.conf:#/dev/hdc,0 -a -s L/../../2/01

# Words having cat or cut
$   egrep -r 'c[au]t' /etc/
/etc/man_db.conf:# NOCACHE keeps man from creating cat pages.
/etc/nanorc:## Use cut-from-cursor-to-end-of-line by default.
```

```sh
# Only /dev/tty0 will be highlighted. p0 will be ignored
$   egrep -r '/dev/[a-z]*[0-9]?' /etc/
/etc/sane.d/dc25.conf:#port=/dev/tty0p0

# Searching after /dev, some letters and a digit at the end.
# After that the same thing can repet 0, 1 or many more time : there can be other sequences of letters followed by 1 digit.
#
# /dev/tty0 would 1st match (-> [a-z]*[0-9]?) and then p0 would be added to this match by that repetition
#
# What makes it look for something to exist 0 or more times ? Thatis going to be the *
# But adding the * at the end would apply to the previous element only -> NOT GOOD
#
# Solution : Put the expression between ()   ->   ([a-z]*[0-9]?)*
$   egrep -r '/dev/([a-z]*[0-9]?)*' /etc/

/etc/sane.d/dc240.conf:port=/dev/ttyS0
/etc/sane.d/dc25.conf:#port=/dev/tty0p0
/etc/sane.d/dc25.conf:#port=/dev/ttyd1
/etc/sane.d/coolscan3.conf:#scsi:/dev/scanner
```
```sh
# Looking for lowercase or Uppercase letters with the | operator
$   egrep -r egrep -r '/dev/(([a-z]|[A-Z])*[0-9]?)*' /etc/

/etc/sane.d/dc240.conf:port=/dev/ttyS0
/etc/sane.d/dc240.conf:#port=/dev/ttyd1
/etc/sane.d/dc240.conf:#port=/dev/term                         #/a
/etc/sane.d/u12.conf:# device /dev/usbscanner

# Looking for Http but excludes Https
# http[^s]  -> ^ will exclude s
$   egrep –r 'http[^s]' /etc/

# After a /, there should not be any lowercase letter
$   egrep –r '/[^a-z]' /etc/
```

<br/>

### ⚠️ <mark>NOTE</mark> 

- a comparison between `grep` and `egrep`

> How many numbers (1 to 4000) in /home/bob/textfile begin with a number 2, save the count in /home/bob/count file.

```sh
$   egrep -e '^2' /home/bob/textfile | wc -l
1111

$   grep -c '^2' textfile
1111
```

- An advanced expression

> Replace all lines, starting with **`container.web`** , ending with **`24h`** and that have the word
**`Running`** anywhere in-between with: **`SENSITIVE LINE REMOVED`**

```sh
# Expression : ^container.web.*Running.*24h$
$   sed -i 's/^container.web.*Running.*24h$/SENSITIVE LINE REMOVED/g' server.log
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Archive, backup, compress, unpack, and uncompress files</ins>


#### 🔖 <ins>Displaying archive content</ins>

```sh
# t for listing
# f for using archive file or device ARCHIVE
$   tar -tf archive.tar
```

<br/>

#### 🔖 <ins>Packing files and Directories</ins>

```sh
# c for creating
# r for appending
$   tar -cf archive.tar file1
$   tar -rf archive.tar file2

# Used for unpacking later directly in current working directory
$   tar --create –-file archive.tar Pictures/

# Used for unpacking later in the origin location
$   tar --create –-file archive.tar /home/aaron/Pictures/
```

<br/>

#### 🔖 <ins>Extracting files</ins>

```sh
# When unpacking and decompressing, though, we don't have to tell tar what decompression utility to use
$   tar -xf archive.tar
$   tar -xf archive.tar.gz


# Extract to other directory
$   tar -xf archive.tar.gz –C /tmp/
```

<br/>

#### 🔖 <ins>Compressing files</ins>

> ⚠️ <mark>**`f`** option should be the last</mark>

```sh
# j for compressing archive through bzip2      -->      -cjf , NOT -cfj !!!!!!!!!!!!!!!
$   tar -cjf /opt/archive.tar.bz2 /home/bob/databases/

# z for compressing archive through gzip
$   tar -czf /opt/archive.tar.gz /home/bob/databases/

# J for compressing archive through xz
$   tar -cJf /opt/archive.tar.xz /home/bob/databases/


# Better option : tar has an auto compressed option to automatically figure out what compression utility to use based on the file name extension we choose for our archive.
$   tar -caf archive.xz file1
```

<br/>

<ins>For **zip** archives, use **`zip`** utility :</ins>

```sh
$   zip archive.zip file1
$   zip –r archive.zip Pictures/

$   unzip archive.zip
Archive: archive.zip
replace file1? [y]es, [n]o, [A]ll, [N]one, [r]ename: 
```

<br/>

If we zip / unzip **<span style="color:#FFA500">non-tar</span>** file

```sh
$ gzip games.txt              $ gunzip games.txt.gz         gzip --decompress games.txt.gz
games.txt.gz                  games.txt

$ bzip2 games.txt             $ bunzip games.txt.bz2        bzip2 --decompress games.txt.bz2
games.txt.bz2                 games.txt

$ xz games.txt                $ unxz games.txt.xz           xz --decompress games.txt.xz
games.txt.xz                  games.txt
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Backup files to a Remote System</ins> (with `rsync` utility)

```sh
# rsync SOURCE_REPO REMOTE_REPO 

# '-a' for archive, to synchronize subdirectories, permissions, modification times
# If you run this command a 2nd time, rsync will only copy data that has changed
$   rsync –a Pictures/ aaron@9.9.9.9:/home/aaron/Pictures/
$   rsync –a aaron@9.9.9.9:/home/aaron/Pictures/ Pictures/
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Input and Output redirection</ins>

#### 🔖 <ins>Output redirection (e.g. >, >>, |, 2>, 2>&1)</ins>

```sh
# Save all errors while OVERWRITING with >
$   find library/ -type d 2> errors.out
$   cat  errors.out
find: ‘library/dir3848’: Permission denied


# Save stdout while APPENDING with >>
$   find library/ -type d >> standard.out
$   cat standard.out
library/dir9896


# Save both stderr and stdout
$   find library/ -type d > all.out 2>&1
$   cat all.out
find: ‘library/dir8868’: Permission denied
library/dir8872
```

<br/>

#### 🔖 <ins>Input redirection</ins>

```sh
# With 'sendmail' command, we don't have an option to use an external file for writing content mail 
$   sort file.txt
$   sendemail someone@example.com

Hi Someone,
How are you today?
...
Talk to you soon
Bye

# We can resolve our issue with this trick  -->  the <
$   sendemail someone@example.com < emailcontent.txt 


# Wit <<EOF we signal the input we want to pass ends before the last line (where we type EOF)
$   sort <<EOF
> 6
> 3
> 2
> 5
> 1
> 4
> EOF              < ---- We will process to the sorting before EOF

1
2
3
4
5
6


# 1+2+3+4 is the input  -->  << will append as a string. <<< do the addition
$   bc <<<1+2+3+4
10
```

&nbsp;
&nbsp;
&nbsp;

## Operation of Running Systems

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Boot, reboot, and shutdown a system safely</ins>

#### 🔖 <ins>Scheduling</ins>

➡️ <ins> <span style="color:#FF8A8A">**NOT RECOMMENDED** (unless absolutelly necessary)</span></ins> : 

we can force close all programs and reboot in a more abrupt way.


```sh
# If neither of those is working, we can pass force twice
$   systemctl reboot --force --force           # It is like rebooting instanly
$   systemctl poweroff --force --force         # It is like unplugging the computer
```

```sh
$   shutdown 02:00         # In 24h format, between 00:00 and 23:59 

# To shutdown a certain number of minutes later  --> +x
$   shutdown +15

# To reboot  --> -r
$   shutdown -r 02:00
$   shutdown -r +15

# To set the wall message : for warning logged in users.
$   shutdown -r +1 'Scheduled restart to do an offline-backup of our database'
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Boot or change system into different operating modes</ins>

```sh
# graphical.target -> The OS is configured to boot into a graphical environment
# By reading the instructions inside this target file, the OS knows :
#   - what programs it needs to launch 
#   - what order to boot into the target
$   systemctl get-default      
graphical.target
```
- If we don't need to load up on graphical user interface, we can change the default boot target **and after we restart the OS**.
```sh
# We can see it removes the old default target file and creates a new symlink.
#
# The multi-user target is something that will make Linux boot normally with all of its Daemons, Databases, servers, utilities and whatever we might have configured. Only the graphical interface is going to be skipped.
#
# Everything will be text-based.
# It is called like this because while Linux is booting in this mode, multiple users can log in and use the system at the same time.
$   systemctl set-default multi-user.target    

Created symlink /etc/systemd/system/default.target → /lib/systemd/system/multi-user.target.
```

- If we want to change to a different target : imagine we want to get back to the graphical desktop because we need to use a 3D modeling application for 1 hour, <span style="color:#FF8A8A"><ins>**instead of changing the default boot**</ins></span>.

<br/>

➡️ We use the `isolate` directive.

```sh
$   systemctl isolate graphical.target
```

- Other useful targets

```sh
# Useful for debugging in case the program is loaded by the other targets for making your system unstable.
$   systemctl isolate emergency.target

# We will be dropped into a root shell. We can type commands as a system administrator : DB backups (while db not online), fix system settings and so on. 
$   systemctl isolate rescue.target
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Use scripting to automate system maintenance tasks</ins>
Example :

Each script begins with a **shebang** `#!/bin/bash`
```sh
#!/bin/bash

# If /tmp/archive.tar.gz already exists
if test -f /tmp/archive.tar.gz; then
  mv /tmp/archive.tar.gz /tmp/archive.tar.gz.OLD
  tar acf /tmp/archive.tar.gz /etc/dnf/
else
  tar acf /tmp/archive.tar.gz /etc/dnf/
fi
```

To be able to run the script, we **first need to make it executable**, by setting the permissions.

```sh
# Only the owner can execute the script
$   chmod u+x script.sh

# Everyone can execute the script
$   chmod +x script.sh 
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Manage the startup process and services (In Services Configuration)</ins>

```sh
# If we want to edit this service file, we can run this command
$   systemctl edit --full sshd.service

# If we want to cancel our edits and return the service file to its factory default settings 
$   systemctl revert sshd.service
```
```sh
# To see the status (if program has started, if it has encountered any errors)
$   systemctl status sshd.service

# To stop a service
$   systemctl stop sshd.service

# To start a service
$   systemctl start sshd.service
```

### ⚠️ <mark>WARNING</mark> :

```sh
# To restart a service
$   systemctl restart sshd.service

# If some users are actively using that program, restart may interrupt their work temporarily.
# There is a MORE gentle way to reload a program settings without completely closing and reopenening the application.
$   systemctl reload sshd.service


# It's worth noting that not all applications support being reloaded with the `reload` option but we have a command that can automatically try a graceful reload first, and then a restart if reload is not supported by the app.
$   systemctl reload-or-restart sshd.service
```

<br/>

```sh
# If we want to diable ssh logins completely, we could prevent the SSH Daemon from automatically starting up at boot time.
$   systemctl disable sshd.service

# Check if service enabled
$   systemctl is-enabled sshd.service
disabled


# Enable and start / disable and stop service
$   systemctl enable --now sshd.service
$   systemctl disable --now sshd.service
```

### ⚠️ <mark>WARNING</mark> :
To REALLY avoid a service to run (**a service 1 can still re-enable a service 2, even if disabled**), use the `mask` option.

If we need to use the service again, please do not forget to `unmask`

```sh
$   systemctl mask atd.service

# Masked services can not start
$   systemctl enable atd.service
Failed to enable unit: Unit file /etc/systemd/system/atd.service is masked.

$   systemctl start atd.service
Failed to start atd.service: Unit atd.service is masked.

# Unmask
$   systemctl unmask atd.service
```

<br/>

```sh
# To list all services
$   systemctl list-units --type service --all
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Create systemd Services ✨</ins>

In a  nutshell, the **service** manages the entire lifecycle of an application according to instructions we add to our **service file**.

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Service file**</ins>

```sh
# Let's create our service file
# systemd-cat is an utility that receives the message and logs it
# We're simulating tha app has crashed after 5 seconds
$   vi /usr/local/bin/myapp.sh

#!/bin/sh
echo "MyApp started" | systemd-cat -t MyApp -p info
sleep 5
echo "MyApp crashed" | systemd-cat -t MyApp -p err
```
```sh
# Make the script executable for all
$   chmod a+x /usr/local/bin/myapp.sh
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Service**</ins>

We will create a service by using SSH daemon service as a **template**.

```sh
$   cp /lib/systemd/system/sshd.service /etc/systemd/system/myapp.service
```
```sh
# Creating the service unit file
$   vi /etc/systemd/system/myapp.service


[Unit]
Description=My Application
# This tells systemd when it should run the service unit.
After=network.target auditd.service                           

[Service]
# Example we could run a program that scan DBs for errors, before starting the DB server itself.
ExecStartPre=echo "Systemd is preparing to startMyApp"        
ExecStart=/usr/local/bin/myapp.sh

# This specifies what should be executed when an administrator runs the Systemctl reload command.
# ExecReload=/bin/kill -HUP $MAINPID

# It specifies how this application should be stopped when running Systemctl stop. Here we tell to only kill the main process and not any of the child processes.                    
KillMode=process                                             
Restart=always
RestartSec=5s
# If 'notify', it tells systemd that the application will notify it when it is finished starting up
Type=simple						      

[Install]
WantedBy=multi-user.target
```

<br/>

The first thing that we have to do is whenever we add or remove or edit the service file, we have to reload the systemd daemon :

```sh
$   systemctl daemon-reload
```

Then we can start our new created service :

```sh
$   systemctl start myapp.service
```

To see the logs :

```sh
$   journalctl -f

-- Logs begin at Sun 2023-10-15 11:00:28 CEST. --
Oct 15 13:00:01 localhost.localdomain systemd[1]: Created slice User Slice of root.
Oct 15 13:00:01 localhost.localdomain systemd[1]: Started Session 16 of user root.
Oct 15 13:00:01 localhost.localdomain CROND[3835]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Oct 15 13:00:01 localhost.localdomain systemd[1]: Removed slice User Slice of root.
Oct 15 13:00:02 localhost.localdomain MyApp[3844]: MyApp crashed
Oct 15 13:00:07 localhost.localdomain systemd[1]: myapp.service holdoff time over, scheduling restart.
Oct 15 13:00:07 localhost.localdomain systemd[1]: Stopped My Application.
Oct 15 13:00:07 localhost.localdomain systemd[1]: Started My Application.
Oct 15 13:00:07 localhost.localdomain MyApp[3847]: MyApp started
Oct 15 13:00:12 localhost.localdomain MyApp[3851]: MyApp crashed
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Diagnose and manage processes</ins>

```sh
# Show all in user format
# TIME represents CPU time. 1s represents 100% of CPU core use.
# If we use 50% for 10s, it will display 5s of TIME.
$   ps aux

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        1531  0.0  0.2  17196 10876 ?        Ss   17:08   0:00 sshd: ubuntu [priv]
ubuntu      1577  0.0  0.2  17196  7980 ?        S    17:08   0:00 sshd: ubuntu@pts/0


# Display process of PID 1
$   ps u 1

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.3 167584 13132 ?        Ss   16:58   0:05 /sbin/init


# Display processes started by a specific user (ubuntu in our case)
$   ps u -U ubuntu

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
ubuntu      1515  0.0  0.1 170496  5000 ?        S    17:08   0:00 (sd-pam)
ubuntu      1521  0.0  0.1   8744  5352 tty1     S+   17:08   0:00 -bash


# Display processes containing the word syslog in its name
$   pgrep -a syslog

943 /usr/sbin/rsyslogd -n -iNONE
```

<br/>

#### 🔖 <ins>Process niceness</ins>

It is a concept to define **priority** for processes. Its value is between -20 and 19 (lower = less nice and means **higher priority** for the process).

> Process A = -19 and Process B = 20. Both needs all CPU resource 
>
> ➡️ Process A might use CPU 90% of time, while Process B will get very little CPU time only when there's something left to spare.

```sh
# Set nice value (NEGATIVE only allowed with root user)
$   sudo nice -n -12 bash
$   nice -n 11 bash


# Set nice value to existing process, with affiliated PID value
$   sudo renice 11 1759     


# Display in long format : NI goes from 0 to 11
$   ps lax
$   ps l

F   UID     PID    PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
4  1000    1521     965  20   0   8744  5352 do_sel S+   tty1       0:00 -bash
0  1000    1760    1578  31  11   8660  5268 do_wai SN   pts/0      0:00 bash
0  1000    1766    1760  31  11  10068  1592 -      RN+  pts/0      0:00 ps l
```

<br/>

#### 🔖 <ins>Parent-Child processes</ins>

```sh
# Display relationship between parent-child processes (including CPU usage)
$   ps fax

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         977  0.0  0.2  15420  9280 ?        Ss   16:59   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root        1531  0.0  0.2  17196 10876 ?        Ss   17:08   0:00  \_ sshd: ubuntu [priv]
ubuntu      1577  0.0  0.2  17196  7980 ?        S    17:08   0:00      \_ sshd: ubuntu@pts/0
```

<br/>

#### 🔖 <ins>Processes signals</ins>

They are like **high priority messages** that send specific instructions to processes (for example : stopping what they're doing, to do a special request). 

But it only works if the applications are programmed specifically to respond that signal.

> The only exceptions are the signals called `SIGSTOP` and `SIGKILL`. They can not be ignored by any processes.
>
> - When processes receive `SIGSTOP`, their execution is going to be paused until `SIGCONT` is called.
> - With `SIGKILL` terminates the process.

<br/>

We can display the list of signals here :

```sh
$   kill -L
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM


# Get PID from status
$   systemctl status sshd.service

● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-08-16 16:59:55 UTC; 2h 17min ago
   Main PID: 977 (sshd)


# Restarting process OpenSSH server daemon (Get PID from status)
$   kill -SIGHUP 977
$   systemctl status sshd.service

● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-08-16 16:59:55 UTC; 2h 18min ago

Aug 16 19:18:03 ubuntu sshd[977]: Received SIGHUP; restarting.
Aug 16 19:18:03 ubuntu sshd[977]: Server listening on 0.0.0.0 port 22.
```

<br/>

#### 🔖 <ins>Background and Foreground processes</ins>

We use the `&` at the end to execute a command in background, like **backing up 500 GB of data**

```sh
# Use &
$   sleep 300 &
[1] 1826

$   echo toto
toto

# Display background processes
$   jobs
[1]+  Running                 sleep 300 &
```

To return a background process to foreground.
```sh
$   jobs
[1]+  Running                 sleep 300 &

# Enter the job ID (1)
$   fg 1
sleep 300

# To stop a foreground process without aborting it (CTRL + Z)
$   ^Z
[1]+  Stopped                 sleep 300

# To resume it in background
$   bg 1
[1]+ sleep 300 &

$   jobs
[1]+  Done                    sleep 300
```

<br/>

#### 🔖 <ins>Display files & directories using a Process</ins>

```sh
$   lsof -p 1788

COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
bash    1788 root  cwd    DIR  253,0     4096 524290 /root


# Here we can see which processes are using this file
$   lsof /var/log/syslog

COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
rsyslogd 943 syslog    7w   REG  253,0   532100 2768 /var/log/syslog
```

<br/>

#### 🔖 <ins>Journal Daemon (on moderne Linux)</ins>

`journalctl` command lets us analyze logs more efficiently

```sh
# journalctl lets us filter for logs generated by a specific command

# Locate sudo command files 
$   which sudo
/usr/bin/sudo


# Showing us the logs generated by the command
$   journalctl /usr/bin/sudo

Jul 26 19:24:47 ubuntu sudo[16290]:   ubuntu : TTY=tty1 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/shutdown -h now
Jul 26 19:24:47 ubuntu sudo[16290]: pam_unix(sudo:session): session opened for user root(uid=0) by ubuntu(uid=1000)
Jul 26 19:24:47 ubuntu sudo[16290]: pam_unix(sudo:session): session closed for user root
```

```sh
# To display logs for a unit (see Manage the startup process and services) 
$   systemctl status sshd.service

● ssh.service - OpenBSD Secure Shell server
     # You see here the real name of the service
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled) 

# As expected, no entries 
$   journalctl -u sshd.service
-- No entries --

# If we have checked the real name above (ssh.service)
$   journalctl -u ssh.service
Jul 26 18:11:28 ubuntu systemd[1]: Starting OpenBSD Secure Shell server...
Jul 26 18:11:28 ubuntu sshd[929]: Server listening on 0.0.0.0 port 22.
Jul 26 18:11:28 ubuntu systemd[1]: Started OpenBSD Secure Shell server.
```

<br/>

Another useful commands

```sh
# To display at the END
$   journalctl -e

Aug 19 08:07:58 ubuntu kernel: [UFW BLOCK] IN=ens33 OUT= MAC=01:00:5e:00:00:01:dc:39:6f:b6:d1:1e:08:00 SRC=192.168.178.1 DS>
lines 972-1000/1000 (END)  # We are at the END


# To display IN LIVE the logs 
$   journalctl -f.

# To filter and display specific log type (info, warning, err or crit)
$   journalctl -p err

Jul 26 18:11:20 ubuntu kernel: piix4_smbus 0000:00:07.3: SMBus Host Controller not enabled!
Jul 26 18:11:20 ubuntu kernel: sd 32:0:0:0: [sda] Assuming drive cache: write through


# To display info log type with message beginning by letter b (grep argument '-g')
$   journalctl -p info -g '^b'

Jul 26 18:11:20 ubuntu kernel: BIOS-provided physical RAM map:
Jul 26 18:11:20 ubuntu kernel: blacklist: Loading compiled-in revocation X.509 certificates
```

```sh
# To display logs since 02:00
$   journalctl -S 02:00

# To display logs between 01:00 AM and 02:00 (also date)
$   journalctl -S 01:00 -U 02:00
$   journalctl -S '2021-11-16 01:00:33' -U '2021-11-16 02:00:50'
-- No entries --


# To see logs on the current boot (0 picks the current boot, -1 the previous boot)
$   journalctl -b 0

Aug 19 07:06:10 ubuntu kernel: Linux version 5.15.0-78-generic (buildd@lcy02-amd64-008) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.>


$   journalctl -b -1

Aug 17 18:45:39 ubuntu kernel: Linux version 5.15.0-78-generic (buildd@lcy02-amd64-008) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.>
```

### ⚠️ <mark>NOTE</mark>

> Contrary to Ubuntu server, on CentOS the journal is only kept in memory (when powering off or rebooting, all logs are lost). 
>
> We can create a directory where to save the logs `mkdir :var/log/journal/`

<br/>

#### 🔖 <ins>See who logged in</ins>

```sh
$   last

ubuntu   pts/1        192.168.178.120  Sat Aug 19 08:11   still logged in
ubuntu   pts/0        192.168.178.120  Sat Aug 19 08:11   still logged in
ubuntu   pts/0        192.168.178.120  Sat Aug 19 07:07 - 08:11  (01:04)
reboot   system boot  5.15.0-78-generi Sat Aug 19 07:06   still running
ubuntu   tty1                          Thu Aug 17 18:48 - down   (01:52)


# Shows when each user logged in the last time.
$   lastlog

Username         Port     From             Latest
root                                       **Never logged in**
ubuntu           pts/1    192.168.178.120  Sat Aug 19 08:11:32 +0000 2023
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Schedule tasks to run at a set date and time</ins>

#### 🔖 <ins>Cron</ins>

Note about the syntax and special characters :

- **`*`** = match all possible values (i.e. every hour)
- **`,`** = match multiple values (i.e. 15, 45)
- **`-`** = range of values (i.e. 2-4)
- **`/`** = specifies steps (i.e. */4)

```sh
$   cat /etc/crontab
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
```
```sh
# Here we edit a user's cron table (not the one from the whole system). In that case we don't specify a user
$   crontab -e

0 3 * * mon,fri tar -czvf /opt/etcbackup.tar.gz /etc/
```
```sh
# To list all jobs of user's cron table
$   crontab -l

35 6 * * * /usr/bin/touch test_passed    # To create a file named test_passed every 6:35
```
```sh
# To remove completely a cron table
$   crontab -r

# For specific user
$   crontab -r -u jane
```

<br/>

#### 🔖 <ins>Anacron</ins>

To schedule a job, we edit the file at **`/etc/anacrontab`**.


```sh
$   vi /etc/anacrontab

# Job runs every 3 days
# 10 different jobs could have missed because the machine was powered off during that day : it wouldn't be healthy to run 10 jobs all at the same time. So we pick a delay for each one and make anacron rn each job after waiting a specific number of minutes. In our case 10 minutes.
#
# job-identifier is used for logging
# Just like Cron, whe should specify the full path to the command or script to run.
  3             10                test_job        /usr/bin/touch /root/anacron_created_this
```
```sh
# Check if anacron syntax is correct. In this case there is an error at Line 17.
$   anacron -T
anacron: Invalid syntax in /etc/anacrontab on line 17 - skipping this line
```

<br/>

We also can use the **`at`** utility :

```sh
# To run something at 3 PM. By pressing ENTER we can specify multiple commands (1st one is 'touch'). To save, type CTRL + D
$   at 15:00

warning: commands will be executed using /bin/sh
at Tue Aug 29 15:00:00 2023
at> /usr/bin/touch file_created_by_at
at> /usr/bin/touch file_created_by_at_2
at>  <EOT>                                # CTRL + D
job 1 at Tue Aug 29 15:00:00 2023


# To run something at August 20 2022 at 2:30
$   at '2:30 August 20 2022'

# To run something 30 minutes later, etc.
$   at 'now + 30 minutes'
$   at 'now + 3 hours'
$   at 'now + 3 days'
$   at 'now + 3 weeks'
$   at 'now + 3 months'
```
```sh
# To see what jobs are scheduled to run
# Our Job ID is 1
$   atq
1       Tue Aug 29 15:00:00 2023 a root

# To see what a job is supposed to do (here our Job ID 1)
$   at -c 1
...
/usr/bin/touch file_created_by_at
/usr/bin/touch file_created_by_at_2

# To remove Job ID 1
$   atrm 1
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Verify completion of scheduled jobs</ins>

When running scheduled jobs with `cron` or `anacron` :
 
* you can see logs in <mark>`/var/log/cron`</mark> (in CentOs by default). In other OS you may need to specify a path to a log output.
* or you can use `journalctl -e`
```sh
$   journalctl -e

Aug 30 16:09:01 ubuntu CRON[57818]: (root) CMD (/bin/echo "Just testing" >> /var/log/cron)
Aug 30 16:09:01 ubuntu CRON[57817]: pam_unix(cron:session): session closed for user root
Aug 30 16:10:01 ubuntu CRON[57821]: (root) CMD (/bin/echo "Just testing" >> /var/log/cron)
Aug 30 16:35:20 ubuntu anacron[57858]: Anacron 2.3 started on 2023-08-30
```


&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Manage software with dnf</ins>

#### 🔖 <ins>What is a software repository ?</ins>

```sh
$   dnf repolist

Last metadata expiration check: 0:02:48 ago on Fri 01 Sep 2023 01:18:57 AM PDT.
repo id                              repo name                                                                    status
base                                 CentOS-7 - Base                                                              10,072
*epel                                Extra Packages for Enterprise Linux 7 - x86_64                               13,770
...


# To get the Web addresses of the online repositories
$   dnf repolist -v

Repo-id      : base
Repo-name    : CentOS-7 - Base
Repo-revision: 1604001756
Repo-updated : Thu 29 Oct 2020 01:03:00 PM PDT
Repo-pkgs    : 10,072
Repo-size    : 8.9 G
Repo-mirrors : http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=$infra
Repo-baseurl : http://ftp.plusline.net/centos/7.9.2009/os/x86_64/ (9 more)                  # HERE
...
```

<br/>

- CentOS has **optional repositories** we can enable and we can see by using the `dnf` command.

```sh
$   dnf repolist --all

repo id                                                   repo name                                                                                         status
base                                                      CentOS-7 - Base                                                                                   enabled: 10,072
base-debuginfo                                            CentOS-7 - Debuginfo                                                                              disabled
```
```sh
# To enable a repo (example : base-debuginfo). You may need to run before "dnf install 'dnf-command(config-manager)'"
$   dnf config-manager --enable base-debuginfo

# To disable a repo
$   dnf config-manager --disable base-debuginfo
```

- Sometimes even the optional CentOS repositories might not have the sofwtare we need. In these cases, we might need to add a 3rd party repository from an external provider. Here, let's install **Docker** :

```sh
$   dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

Adding repo from: https://download.docker.com/linux/centos/docker-ce.repo


$   dnf repolist -v

Repo-id      : docker-ce-stable
Repo-name    : Docker CE Stable - x86_64
Repo-revision: 1690814364
Repo-updated : Mon 31 Jul 2023 07:39:24 AM PDT
Repo-pkgs    : 259
Repo-size    : 5.4 G
Repo-baseurl : https://download.docker.com/linux/centos/7/x86_64/stable
Repo-expire  : 172,800 second(s) (last: Fri 01 Sep 2023 01:49:31 AM PDT)
Repo-filename: /etc/yum.repos.d/docker-ce.repo          # The information about the repository is contained in that file


# To remove the repository, we can simply remove the file
$   rm /etc/yum.repos.d/docker
```

<br/>

- Sometimes we may not be sure that what the exact name of a package is, but we can search through their descriptions and maybe find a match.

```sh
# Search for web server with the exact order
$   dnf search 'web server'

nikto.noarch : Web server scanner
nginx.x86_64 : A high performance web server and reverse proxy server


# To get more info
$   dnf info nginx

Name         : nginx
Epoch        : 1
Version      : 1.20.1
...
```

<br/>

#### 🔖 <ins>Commands</ins>

```sh
# To install a package
$   dnf install nginx

# If we accidentally remove a program file or a configuration file, we can reinstall it
$   dnf reinstall nginx

# To remove a package
$   dnf remove nginx
```

When needed to install a specific software who needs a lot of packages, it can be tedious to type every package name : we can use in this case a `group` install.

```sh
$   dnf group list --hidden

Available Environment Groups:
   GNOME Desktop
Available Groups:
   Electronic Lab


# To install the group 'GNOME Desktop'
$   dnf group install 'GNOME Desktop'

# If we want to install with some extra optional packages
$   dnf group install --with-optional 'GNOME Desktop'

# To remove
$   dnf group remove 'GNOME Desktop'
```
```sh
# To automatically remove packages left behind
$   dnf autoremove

# To get a summary of previous actions with dnf package manager
$   dnf history

ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     5 | remove nginx             | 2023-09-01 02:06 | Removed        |    3
     4 | reinstall nginx          | 2023-09-01 02:00 | R              |    2
     3 | install nginx            | 2023-09-01 01:59 | Install        |    3
     2 | install dnf-command(conf | 2023-09-01 01:30 | Install        |    2
     1 | upgrade                  | 2023-08-31 11:59 | I, U           |  337 EE
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Identify the component of a Linux distribution that a file belongs to</ins>
Imagine you accidentally mess up a configuration file, you want your original file back. 

But **which software package installed that file ?** If we have made bad edits with `anacron` : 

```sh
# You can find out which package installed this (here the name is 'cronie-anacron')
$   dnf provides /etc/anacrontab

cronie-anacron-1.4.11-25.el7_9.x86_64 : Utility for running regular jobs
Repo        : @System
Matched from:
Filename    : /etc/anacrontab


# We can remove our config file and reinstall to get back our original file
$   rm /etc/anacrontab
$   dnf reinstall cronie-anacron

# IF WE WANT TO UNINSTALL COMPLETELY
$   dnf remove cronie-anacron -y
```
```sh
# Instead of looking for which package provides a file, we can get a list of all files that a package contains
$   dnf repoquery --list nginx

/etc/logrotate.d/nginx
/etc/nginx/fastcgi.conf
/etc/nginx/mime.types
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Verify the integrity and availability of resources</ins>

#### 🔖 <ins>Resources</ins>

As time goes by, a server will usually use up more and more resources. **How do we know when we're running out of storage space ?** We can use use the `df` (disk free utility).

```sh
# Ignore tmpfs : they're virtual file systems
$   df -h

Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
/dev/sda3        27G  5.7G   22G  22% /
/dev/sda1       297M  213M   85M  72% /boot
tmpfs           378M     0  378M   0% /run/user/1000
```

- To see how much disk space a specific directory is using, use the `du` (disk usage utility) :

```sh
# -s for summarize, -h for human format
$   du -sh /var/log

8.4M    /var/log
```

- To see how **memory** is used :

```sh
$   free -h
              total        used        free      shared  buff/cache   available
Mem:           3.7G        270M        3.2G         11M        201M        3.2G
Swap:          3.0G          0B        3.0G
```

- With the `uptime` command, we can see how heavily the CPU cores were used by the programs :

```sh
# 1st number : load average for the last one minute
# 2nd number : load average for the last 5 minutes
# 3rd number : refers to the last 15 minutes

# If we see for 1st number a load average of 1.0, it means that one CPU core has been used at full cupacity in the last one minute
$   uptime

 07:01:12 up  1:54,  2 users,  load average: 0.00, 0.01, 0.05
```

- If we want to see some details about the specific CPU used on the system, we can use `lscpu` command :

```sh
$   lscpu

Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
...
```

- To see some details about other hardware on the system :

```sh
$   lspci

00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (rev 01)
00:01.0 PCI bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX AGP bridge (rev 01)
...
```

<br/>

#### 🔖 <ins>Partitions</ins>

To check a file system for errors, we must first mount it. So : 

- to verify a **XFS** file system, we'd use a command like this one : **`xfs_repair`**
- to verify a **EXT4** file system, we'd use a command like this one :  **`fsck`**

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**XFS**</ins>

```sh
# First let's check an unmounted disk (here : sdb)
$   lsblk | grep sd

sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk


# Get info about disks
$   parted -l

Model: VMware, VMware Virtual S (scsi)
Disk /dev/sda: 32.2GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system     Flags
 1      1049kB  316MB   315MB   primary  xfs             boot
 2      316MB   3537MB  3221MB  primary  linux-swap(v1)
 3      3537MB  32.2GB  28.7GB  primary  xfs


Error: /dev/sdb: unrecognised disk label
Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 8590MB
Sector size (logical/physical): 512B/512B
Partition Table: unknown
Disk Flags:


# Creating XFS filesystem with internal log on the same device
$   mkfs.xfs /dev/sdb

meta-data=/dev/sdb               isize=512    agcount=4, agsize=524288 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
...


# Check info about sdb (see Disk Flags)
$   parted -l

Model: VMware, VMware Virtual S (scsi)
Disk /dev/sdb: 8590MB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End     Size    File system  Flags
 1      0.00B  8590MB  8590MB  xfs


# Run command
$   xfs_repair -v /dev/sdb

Phase 1 - find and verify superblock...
        - block cache size set to 355592 entries
...

        XFS_REPAIR Summary    Fri Sep  1 09:07:01 2023

Phase           Start           End             Duration
Phase 1:        09/01 09:07:01  09/01 09:07:01
Phase 2:        09/01 09:07:01  09/01 09:07:01
Phase 3:        09/01 09:07:01  09/01 09:07:01
Phase 4:        09/01 09:07:01  09/01 09:07:01
Phase 5:        09/01 09:07:01  09/01 09:07:01
Phase 6:        09/01 09:07:01  09/01 09:07:01
Phase 7:        09/01 09:07:01  09/01 09:07:01

Total run time:
done
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**EXT4**</ins>

```sh
# /dev/sdb is using XFS file system. Let'es format it
$   lsblk -f

NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
sda
├─sda1 xfs          fb381173-b9a2-4660-8d46-33e2740020ac /boot
├─sda2 swap         fe94ec59-524c-48d9-b1e0-efa52a69854f [SWAP]
└─sda3 xfs          6b82faad-2059-4ea8-bf1f-eb7c1fa74dc0 /
sdb    xfs          3b2118b3-4f06-4055-be4f-9a1eeb9a184a
sr0

# Format to EXT4 
$   mkfs -t ext4 /dev/sdb

mke2fs 1.42.9 (28-Dec-2013)
/dev/sdb is entire device, not just one partition!
Proceed anyway? (y,n) y
...


# Using the command
$   fsck.ext4 -v -f -p /dev/sdb

          11 inodes used (0.00%, out of 524288)
           ...
------------
           2 files
```

<br/>

#### 🔖 <ins>Processes</ins>


How to make sure that key processes are working correctly on our system ?

```sh
# Green light -> service is currently running
$   systemctl list-dependencies

default.target
● ├─abrt-ccpp.service
● ├─abrt-oops.service
● ├─abrt-vmcore.service
● ├─abrt-xorg.service
● ├─crond.service
● ├─...
```
```sh
# To kill a program (here crond.service)
$   pkill crond
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Change kernel runtime parameters, persistent and non-persistent</ins>

<ins>**NOTE :**</ins> **non persistent** kernel runtime parameter is a reseted parameter at reboot. We need to make them persistent.

```sh
# To see all kernel runtime currently in use
# net -> network
# vm  -> virtual memory
# fs  -> file system
$   sysctl -a

fs.pipe-user-pages-hard = 0
sysctl: permission denied on key 'fs.protected_hardlinks'
sysctl: permission denied on key 'fs.protected_symlinks'
net.ipv6.conf.default.disable_ipv6 = 0

# To disable IPv6. BUT ONCE WE REBOOT THE SYSTEM, it is set to 0 again, by default.
$   sysctl -w net.ipv6.conf.default.disable_ipv6=1
net.ipv6.conf.default.disable_ipv6 = 1

# To check the current value per parameter
$   sysctl net.ipv6.conf.default.disable_ipv6
net.ipv6.conf.default.disable_ipv6 = 1
```

```sh
# Let's say we want to look only at memory related parameters.
$   sysctl -a | grep vm

vm.panic_on_oom = 0
vm.percpu_pagelist_fraction = 0
vm.stat_interval = 1
# we're using 30% of disk memory when we run out of RAM
vm.swappiness = 30
```

<br/>

#### 🔖 <ins>How do we make a change **persistent** ?</ins>

We create a file named `swap-less.conf` in **`/etc/sysctl.d`** directory. 
  
  Now every time the Linux OS boots, this parameter will be set to 29.

```sh
$   vi /etc/sysctl.d/swap-less.conf
vm.swappiness = 29


# If we want Linux to adjust parameters immediately
$   sysctl -p /etc/sysctl.d/swap-less.conf
vm.swappiness = 29
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>List and Identify SELinux/AppArmor file and process contexts</ins>

> https://github.blog/2023-07-05-introduction-to-selinux/

We have seen the file and directory permissions but it is not enough today with the current sophisticated cyber attacks. 

Linux kernel can be extended with modules and there is security module called SELInux (**Security-Enhanced Linux**) that adds advanced capabilities of restricting access.

<br/>

To display the SELinux context of a Unix file : **`ls -Z`**

```sh
# AS USER
# =======
$   ls -l
-rw-rw-r--. 1 cento cento 0 Sep  2 03:45 user.log

$   ls -Z
-rw-rw-r--. cento cento unconfined_u:object_r:user_home_t:s0 user.log


# AS ROOT
# =======
$   ls -l
-rw-r--r--. 1 root root    0 Sep  2 03:40 text.log

$   ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 text.log
```

**`unconfined_u:object_r:user_home_t:s0`** is called SELinux context label.

- `unconfined_u` : **user**
- `object_r` : **role**
- `user_home_t`  : **type**
- `s0` : **level**

Every Linux user who logs in to a system is mapped to a SE Linux user as part of the Linux policy configuration.

```sh
# Anything labeled with unconfined_t is running unrestricted
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 1667 ? D   0:00 sshd: cento@pts/0
```

This restriction bubble (`user_home_t`) is called :

- <ins><span style="color:#FF8A8A">**type**</span></ins> : when dealing with files and directories
- <ins><span style="color:#FF8A8A">**domain**</span></ins> : when dealing with processes 

<br/>

It acts like a proctective software jail.

&nbsp;

Going throught this 3 step path (user to role to type), the security module achieves 3 important things :

1. Only certain users can enter certain roles and certain types. 
2. It lets authorized users and processes do their job, by granting the 
permissions they need.
3. Authorized users and processes are allowed to take only a limited 
set of actions. 
4. Everything else is denied.

<br/>

> <ins>The fourth protect us against a hijacked program :</ins>
> 
> Imagine a hacker takes control of the Apache program that serves Web pages to our website visitor.
<br/>
> They are still restricted to SELinux domain, so they aren't able to step out of thos boundaries : they're in a virtual jail and the damage they can do thus is greatly reduced.

<br/>

Usually the most important part of Linux context is <ins><span style="color:#FF8A8A">**the type or domain**</span></ins> (the 3rd part of our label).

#### 🔖 <ins>Commands</ins>

```sh
# To see all processes 
$   ps axZ

# SSH daemon is resctricted to sshd_t domain
# This domain contains a strict set of policies that define what this process is allowed to do.
system_u:system_r:sshd_t:s0-s0:c0.c1023 1044 ?  Ss     0:00 /usr/sbin/sshd -D
system_u:system_r:tuned_t:s0      1046 ?        Ssl    0:03 /usr/bin/python2 -Es /usr/sbin/tuned -l -P


# Checking the /usr/sbin/sshd file. 
# Not anything can enter sshd_t domain. Only files that have the same sshd_exec_t domain SELinux type in their label can enter this domain and so start the process.
$   ls -Z /usr/sbin/sshd
-rwxr-xr-x. root root system_u:object_r:sshd_exec_t:s0 /usr/sbin/sshd
```

<br/>

What got applied to our user when we logged in ?

```sh
$   id -Z
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

# To see how the mapping is done to SE Linux user
# When root logs in it is map to unconfined_u
$   semanage login -l

Login Name           SELinux User         MLS/MCS Range        Service

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
system_u             system_u             s0-s0:c0.c1023       *


# To see the roles that each user can have on this system
$   semanage user -l

                Labeling   MLS/       MLS/
SELinux User    Prefix     MCS Level  MCS Range                      SELinux Roles

guest_u         user       s0         s0                             guest_r
root            user       s0         s0-s0:c0.c1023                 staff_r sysadm_r system_r unconfined_r
...
```

```sh
# To see if SELinux is enabled and actively restricting actions
# 3 possible status : 
# - Enforcing
# - Permissive (allows everything and just locking actions that should have been restricted instead of denying them)
# - Disabled
$   getenforce
Enforcing
```

&nbsp;
&nbsp;
&nbsp;

## User and Group Management

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create, delete, and modify local user accounts</ins>

#### 🔖 <ins>Create</ins>

```sh
# Command to create an User
$   useradd john

$   ls -l /home/
drwx------. 15 cento cento 4096 Sep  2 03:45 cento
drwx------.  3 john  john    78 Sep  3 00:01 john

$   ls -a /etc/skel
.  ..  .bash_logout  .bash_profile  .bashrc  .mozilla


# Add an user with a specific User ID
$   useradd --uid 1100 smith
```

<br/>

Default options for a created user


```sh
$   useradd --defaults

GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes


$   cat /etc/login.defs

# *REQUIRED*
#   Directory where mailboxes reside, _or_ name of file, relative to the
#   home directory.  If you _do_ define both, MAIL_DIR takes precedence.
#   QMAIL_DIR is for Qmail
#
#QMAIL_DIR      Maildir
MAIL_DIR        /var/spool/mail
#MAIL_FILE      .mail
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Password**</ins>

```sh
# To set a password
$   passwd john

Changing password for user john.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

The account details such as usernames, user IDs, group IDs, preferred shells, etc. can be found at **`/etc/passwd`**.

```sh
$   cat /etc/passwd

# john:x:<USER_ID>:<GROUP_ID>::<HOME_DIRECTORY>:<PREFERRED_SHELL>
john:x:1001:1001::/home/otherdirectory/:/bin/othershell
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**System Account**</ins>

These accounts are intended for <ins><span style="color:#FF8A8A">**programs**</span></ins>. Contrary to user accounts, they don't have a home directory.

```sh
# The numeric ID is maller than 1000
$   useradd --system sysacc
```

<br>

#### 🔖 <ins>Delete</ins>

To delete an account and its group

```sh
$   userdel john

# To also delete its home directory and mail spool
$   userdel -r john
```

<br>

#### 🔖 <ins>Modify</ins>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Home directory**</ins>

```sh
$   ls -l /home
drwx------. 15 cento cento 4096 Sep  2 03:45 cento
drwx------.  3 john  john    78 Sep  3 01:15 john

# --move-home or -m ensures that old directory will be moved or renamed
$   usermod -d /home/otherdirectory -m john

$   ls -l /home
drwx------. 15 cento cento 4096 Sep  2 03:45 cento
drwx------.  3 john  john    78 Sep  3 01:15 otherdirectory
```

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Username**</ins>

```sh
$   ls -l /home
drwx------. 15 cento cento 4096 Sep  2 03:45 cento
drwx------.  3 john  john    78 Sep  3 01:15 john

# Modify username
$   usermod -l jane john

# john user name has changed
$   ls -l /home
drwx------. 15 cento cento 4096 Sep  2 03:45 cento
drwx------.  3 jane  john    78 Sep  3 01:15 otherdirectory
```

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**Shell**</ins>

```sh
$   cat /etc/passwd
jane:x:1001:1001::/home/otherdirectory:/bin/bash

# Modify shell
$   usermod -s /bin/othershell jane

$   cat /etc/passwd
jane:x:1001:1001::/home/otherdirectory:/bin/othershell
```

<br/>

- To disable or enable again an account :

```sh
# Lock
$   usermod -L jane

# Unlock
$   usermod -U jane
```

- To set an expiration date or no expiration date to an account :

```sh
# Date format: YEAR-MONTH-DAY
$   usermod -e 2021-12-10 jane

# No expiration
$   usermod -e "" jane
```

<br/>

##### &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>**chage (change age) utility**</ins>


- To set an expiration to a <ins><span style="color:#FF8A8A">**password**</span></ins> (chage for **ch**ange **age**):

```sh
# To set the password to expired immediately
$   chage -d 0 jane

# To cancel previous action : unexpire the password
$   chage -d -1 jane


# To make sure the user changes password every 30 days
$   chage -M 30 jane

# To make sure the password never expires
$   chage -M -1 jane
```

- To see when account expires :

```sh
$   chage -l jane

Last password change                                    : Sep 03, 2023
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7
```

<br>

#### 🔖 <ins>Other commands</ins>

```sh
# Find out the groups of user ray
$   id ray
uid=1003(ray) gid=1003(ray) groups=1003(ray),1001(admin)
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create, delete, and modify local groups and group memberships</ins>

Groups can also have special effects. We have :

- <ins>The primary group (called **login group**) :</ins> this is the group applied to you when you log in.

- <ins>The secondary group :</ins> groups you are a member of beyond your primary group.

<br/>

#### 🔖 <ins>Create</ins>

```sh
# Create group
$   groupadd developers

# To add an User to this group
$   gpasswd -a john developers


# To see in which groups the User belongs
# The first group after the : is the primary or login group
# What follows are the secondary or supplementary groups
$   groups john
john : john developers


# To remove from a group
$   gpasswd -d john developers
```

<br>

#### 🔖 <ins>Modify</ins>

```sh
# -g change the primary group
$   usermod -g developers john
# -G change the secondary group
$   usermod -G developers john


# Primary group is developers
$   groups john
john : developers
```

To rename a group, from developers to programmers :

```sh
$   groups john
john : developers

# RENAME
$   groupmod -n programmers developers

$   groups john
john : programmers
```

### ⚠️ <mark>WARNING</mark> (note the difference here) :

- `gpasswd` : expects USERNAME then GROUP
- `usermod` : expects GROUP then USERNAME


<br>

#### 🔖 <ins>Delete</ins>

```sh
$   groupdel programmers
groupdel: cannot remove the primary group of user 'john'

# We have to change John's primary group to john group
$   usermod --gid john john

# REMOVE
$   groupdel programmers
$   groups john
john : john
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Manage system-wide environment profiles</ins>

#### 🔖 <ins>Setting environment variables</ins>

```sh
# To display current user's environment variables
$   printenv

XDG_SESSION_ID=1
HOSTNAME=localhost.localdomain
SELINUX_ROLE_REQUESTED=
TERM=xterm-256color
SHELL=/bin/bash
HISTSIZE=1000             # Maximum size of the commands history for a shell it should have
SSH_CLIENT=192.168.25.2 51001 22
SELINUX_USE_CURRENT_RANGE=
SSH_TTY=/dev/pts/0
USER=cento


$   echo $HOSTNAME
localhost.localdomain


# To edit variables
$   vi /home/$USER/.bashrc
```

<br/>

If we want to make sure that some variable are set to a certain value **for every user that logs on the system**, we can add them to the **`/etc/environment`** file.

```sh
$   su -
$   vim /etc/environment
KODEKLOUD=https://kodekloud.com

$   ls -l /etc/
-rw-r--r--.  1 root root       32 Sep  4 02:34 environment

$   logout
$   echo $KODEKLOUD
$   logout
Connection to 127.0.0.1 closed

$   ssh cento@127.0.0.1
$   echo $KODEKLOUD
https://kodekloud.com
```

<br>

#### 🔖 <ins>Scripts</ins>

What if we want to run a command every time a user logs in : we can use the special directory **`/etc/profile.d/`** to create a bash file.

```sh
$   vim /etc/profile.d/lastlogin.sh

echo "Your last login was at: " > $HOME/lastlogin
date >> $HOME/lastlogin


$   logout
$   ls /home/cento/
Desktop  Documents  Downloads  lastlogin  Music  Pictures  Public  Templates  user.log  Videos

$   cat /home/cento/lastlogin
Your last login was at:
Mon Sep  4 03:25:45 PDT 2023
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Manage template user environment</ins>

When creating a new User account, all files from **`/etc/skel/`** are copied to the new User's home directory.

Here, we will create a README file that will be copied to the home directory of the new user.
```sh
$   vi /etc/skel/README

Please don't run CPU-intensive 
processes between 8AM and 10PM.
```
```sh
# Let's create a new user 
$   useradd trinity

# We can find that README was copied to /home/trinity
$   ls -a /home/trinity
.  ..  .bash_logout  .bash_profile  .bashrc  .mozilla  README

$   cat /home/trinity/README
Please don't run CPU-intensive
processes between 8AM and 10PM.
```

<br/>

What if we want to add environment variables for our new User ?

 We could edit his/her **`.bashrc`** file : his/her bash login shell will execute the instructions in this file every time she logs in.

```sh
$   echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/cento/.local/bin:/home/cento/bin


$   vi /home/cento/.bashrc

# PATH="$HOME/.local/bin:$HOME/bin:$PATH"
# Our new entry to PATh is /opt/bin, preceded and followed by a colon : (the separator for each new entry)
# We have to make sure we add the entry before the $PATH
# By adding some extra applications in /opt/bin we can add this path variable for all users or for a specific user.
PATH="$HOME/.local/bin:$HOME/bin:/opt/bin:$PATH"


$   logout
Connection to 127.0.0.1 closed.

$   echo $PATH
/home/cento/.local/bin:/home/cento/bin:/opt/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/cento/.local/bin:/home/cento/bin
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure user resource limits</ins>
```sh
$   vi /etc/security/limits.conf

#<domain>      <type>  <item>         <value>
#

# Every users can use to 30 processes but trinity can only go to 10 processes
trinity          hard   nproc            10
@developers      soft   nproc            20
*                -      nproc            30

# trinity should be able to run 20 processes at the most. When she logs in, she should be able to use up to her entire allocation without needing to manually raise her limit.
trinity          -      nproc            20

# CPU time 1s represents 100% of CPU core use. If 50%, 0.5s.
# Here a CPU time limit of 1 minute would take 1 minute to be consumed if the process had 100% cpu utilization, but 2 minutes to consume if the process used 50% utilization.
trinity          hard   cpu              1
```

Lets' limit the number of processes to 3.

```sh
#<domain>      <type>  <item>         <value>
trinity            -   nproc            3

# Let's lauch ps and pipe output to the less pager : we have 3 running processes
$   ps | less
   PID TTY          TIME CMD
  4282 pts/0    00:00:00 bash
  4324 pts/0    00:00:00 ps
  4325 pts/0    00:00:00 less

# Let's try to run 4 mores processes : 'ls', 'grep', 'bash' and 'less'. it is failing as expected
$   ls -a | grep bash | less
-bash: fork: retry: Resource temporarily unavailable
-bash: fork: retry: Resource temporarily unavailable
```

<br/>

To display the limits to our current session.

```sh
[trinity@localhost ~]$   ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 14959
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 3
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

# To set our max user processes to 5000
[trinity@localhost ~]$   ulimit -u 5000
-bash: ulimit: max user processes: cannot modify limit: Operation not permitted
```

<br/>

### ⚠️ <mark>WARNING</mark> :

> You must **logout** of all sessions, the log in again.
>
> If you don't do that, changes will never applied and you will <span style="color:#FF8A8A">**lose points when passing the certification**</span>.

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Manage User Privileges</ins>

There is a special file (**`/etc/sudoers`**) that defines who can use `sudo` and **under what conditions, what commands they can run and so on** <span style="color:#FF8A8A">**but we shouldn't edit this file directly**</span>. 

We use an utility called **`visudo`** that can check up our edits or correct to help us avoid mistakes in the file.

<br/>

Let's start editing '/etc/sudoers' using `visudo` command :

- **%wheel** : represents any members of wheel group. In this 1st colum we define who the policy is for.

- **ALL=** : it is the **host**. Here we could specify that these rules only apply if our server's hostname or IP address has a specific value. Here, with ALL, we run as root.

- **(ALL)** : it is the **run as field**. Here we could type a list of usernames.

- **ALL** : it is the **list of commands** that can be excuted with sudo.
```sh
$   visudo

## Allows people in group wheel to run all commands
#
# The syntax :
# user/group  hot=(run_as_user)     command_list
%wheel  ALL=(ALL)       ALL
```

<br>

#### 🔖 <ins>Example</ins>

Let's define a policy for our User to let him :

- run **any `sudo`** command

```sh
cento  ALL=(ALL)       ALL
```

<br/>

- run any `sudo` command **as *trinity* and *john* user**

```sh
cento  ALL=(trinity,john)       ALL
```

<br/>

- run `sudo` command **to some specific commands**

```sh
cento  ALL=(ALL) /bin/ls, /bin/stat
```
```sh
cento  ALL= /bin/ls, /bin/stat
```

<br/>

- run any `sudo` command **without providing password**

```sh
cento  ALL=       NOPASSWD:ALL
user2 ALL=(root) NOPASSWD: /bin/bash /root/dangerous.sh 
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure PAM (Pluggable Authentication Module)</ins>

When an User authenticates, he is allowed to do someting.

Actually not only people can authenticate but **programs can too** : if Program A needs confidential data from Program B, it needs to authenticate by providing some code. 

This is where **PAM** came into action. Some of them are already installed on CentOS. 

> You can for example enable authentication by USB stick with one of these modules.

<br/>

We already have used **`su`** to authenticate as root.


```sh
$   ls /etc/pam.d/

atd               fingerprint-auth-ac     ksu               pluto         runuser-l          sssd-shadowutils  systemd-user
chfn              gdm-autologin           liveinst          polkit-1      setup              su                vlock
...
```

- Currently, when you use the su command it will **ask for root's password** to allow you to log in as user root. **We can disable it**.

```sh
$   sudo vi /etc/pam.d/su

# Uncomment the following line to implicitly trust users in the "wheel" group.
auth            sufficient      pam_wheel.so trust use_uid
```

&nbsp;
&nbsp;
&nbsp;

## Networking

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure networking and hostname resolution statically or dynamically</ins>

#### 🔖 <ins>Basic commands</ins>

- Show IP

```sh
# Find out the name of network interface. Our 1st network adapter is ens33
$   ip link show
    ip l

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:84:a7:96 brd ff:ff:ff:ff:ff:ff


# To see the IP addresses configured for different network interfaces
# Physical networks cards use wires and always have name beginning with 'e'
# Wireless networks cards always have name beginning with 'w'
$   ip address show
    ip a

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:84:a7:96 brd ff:ff:ff:ff:ff:ff
    inet 192.168.25.135/24 brd 192.168.25.255 scope global noprefixroute dynamic ens33
       valid_lft 1462sec preferred_lft 1462sec
    inet6 fe80::6855:affd:a4a1:7345/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

- Routing

```sh
# Show routing table
#
# default is the default IP address of the gateway (192.168.25.2)
$   ip route show
    ip r

default via 192.168.25.2 dev ens33 proto dhcp metric 100
192.168.25.0/24 dev ens33 proto kernel scope link src 192.168.25.135 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1


# To see which DNS resolvers the system is currently using
#
# Our DNS resolver here is the same as our default gateway (192.168.25.2)
$   cat /etc/resolv.conf

# Generated by NetworkManager
search localdomain
nameserver 192.168.25.2
```
```sh
# Adding a temporary static route so that network traffic going to 192.168.5.0/24 is routed via the IP address of a host's NIC (172.28.128.64), which plays the Router's role
$   ip a

3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:38:cd:9a brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.64/24 brd 172.28.128.255 scope global dynamic noprefixroute eth1
       valid_lft 3185sec preferred_lft 3185sec
    inet6 fe80::5054:ff:fe38:cd9a/64 scope link 
       valid_lft forever preferred_lft forever


$   ip route add 192.168.5.0/24 via 172.28.128.64
```
```sh
# Now, permanently route all traffic that must reach the 192.168.0.* network through the device that has the IP 172.28.128.100
# Also tell the NetworkManager to immediately apply the network settings we just changed.
$   nmcli connection modify eth1 +ipv4.routes "192.168.0.0/24 172.28.128.100"


# Now tell the NetworkManager to apply the network settings we just changed:
$   nmcli device reapply eth1
Connection successfully reapplied to device 'eth1'.
```

- Hostname

```sh
$   vi /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

<br/>

When the OS system boots, it configures the network according to the settings that we find in **`/etc/sysconfig/network-scripts/`**

```sh
$   ls /etc/sysconfig/network-scripts/

ifcfg-ens33
...


# We can see our network is dynamically configured through DHCP (BOOTPROTO)
# For a static configuration, replace dhcp by 'none'
$   cat /etc/sysconfig/network-scripts/ifcfg-ens33
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
...
```

<br/>

#### 🔖 <ins>NMTUI</ins>

Network Manager Text User Interface

```sh
$   nmtui

# After having edited, if we want to apply changes without rebooting (here ens33)
$   nmcli device reapply ens33
Connection successfully reapplied to device 'ens33'.
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Start, stop, and check the status of network services</ins>


```sh
# To see programs that are ready to accept incoming network connections
# -l for listing who is listening
# -t for TCP
# -u for UDP
# -n for numeric values
# -p for showing processes involved with every entry or output
#
# *:22 means the program is listening for incoming connections from anywhere on port 22
# To see a list of connections coming from the OUTSIDE world, see 0.0.0.0 (which means we accept connections from any external IP address)
# [::]:* means we accept IPv6 connections from anywhere
$   ss -tunlp

Netid  State      Recv-Q Send-Q            Local Address:Port                             Peer Address:Port         Process

tcp    LISTEN     0      128                             *:22                                          *:*          users:(("sshd",pid=1041,fd=3))

...


# [::]:22, we can see connections on Port 22 are accepted by our Process, the SSH daemon, running under PID 1041
$   ps 1041

   PID TTY      STAT   TIME COMMAND
  1041 ?        Ss     0:00 /usr/sbin/sshd -D


$   lsof -p 1041

COMMAND  PID USER   FD   TYPE             DEVICE SIZE/OFF    NODE NAME
sshd    1041 root  rtd    DIR                8,3      224      64 /
sshd    1041 root  txt    REG                8,3   852952 1647666 /usr/sbin/sshd
...
```

<br/>

To start, stop, or check the status of network services, use **`systemctl`**

```sh
# To check the status of the network services
$   systemctl status sshd.service

● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2023-09-11 01:39:43 PDT; 3h 44min ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 1041 (sshd)
    Tasks: 1
   CGroup: /system.slice/sshd.service
           └─1041 /usr/sbin/sshd -D

Sep 11 01:39:42 localhost.localdomain systemd[1]: Starting OpenSSH server daemon...
Sep 11 01:39:43 localhost.localdomain sshd[1041]: Server listening on 0.0.0.0 port 22.
Sep 11 01:39:43 localhost.localdomain sshd[1041]: Server listening on :: port 22.
Sep 11 01:39:43 localhost.localdomain systemd[1]: Started OpenSSH server daemon.
Sep 11 01:40:01 localhost.localdomain sshd[1650]: Accepted password for cento from 192.168.25.2 port 51630 ssh2
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure bridge and bonding devices</ins> (with `netplan` utility, <span style="color:#FFA500">for Ubuntu</span>)  

- **Network bonding** combines multiple LAN or Ethernet interfaces into a single logical
interface known as a **network bond** : The goal of network bonding is to provide **`fault tolerance`** and **`network redundancy`**.

- **Network Bridging** involves the creation of a device that connects two LAN networks and controls the flow of data packets between them.

<br/>

`netplan` is the new networking system that is taking over from editing the <mark>`/etc/networking/`</mark> interfaces file.

```sh
$   sudo apt-get -y install netplan.io
```

<br/>

Before, let's take a look at our interfaces

```sh
# ens37 and ens38 are 2 NICs we've just added
$   ip -c addr

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:82 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.1.111/24 brd 192.168.1.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fed6:bb82/64 scope link 
       valid_lft forever preferred_lft forever
3: ens37:  <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:0c:29:d6:bb:96 brd ff:ff:ff:ff:ff:ff
    altname enp2s5
4: ens38: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:0c:29:d6:bb:8c brd ff:ff:ff:ff:ff:ff
    altname enp2s6
```

Let's edit our NICs

```sh
# Set IPv4
$   sudo ip address add 192.168.1.171/24 dev ens37

# Turn on the NIC
$   sudo ip link set dev ens37 up
```
```sh
# Set DNS and gateway : make a file in /etc/neplan/
$   sudo vi /etc/netplan/00-installer-config.yaml
```
```yml
network:
  ethernets:
    ens33:
      addresses:
      - 192.168.1.111/24
      nameservers:
        addresses:
        - 192.168.1.100
      routes:
      - to: default
        via: 192.168.1.1
    ens37:
      addresses:
      - 192.168.1.171/24
      nameservers:
        addresses:
        - 192.168.1.100
      routes:
      - to: 192.168.1.0/24
        via: 192.168.1.1
    ens38:
      addresses:
      - 10.10.1.10/24
  version: 2
```
```sh
# Apply changes
$   sudo netplan try
```

<br/>

```sh
$   ip -c addr

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:82 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.1.111/24 brd 192.168.1.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fed6:bb82/64 scope link 
       valid_lft forever preferred_lft forever
3: ens37: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:96 brd ff:ff:ff:ff:ff:ff
    altname enp2s5
    inet 192.168.1.171/24 brd 192.168.1.255 scope global ens37
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fed6:bb96/64 scope link 
       valid_lft forever preferred_lft forever
4: ens38: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:8c brd ff:ff:ff:ff:ff:ff
    altname enp2s6
    inet 10.10.1.10/24 brd 10.10.1.255 scope global ens38
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fed6:bb8c/64 scope link 
       valid_lft forever preferred_lft forever
```

<br/>

#### 🔖 <ins>Bridge</ins> (just an example of configuration but no demo)

```sh
# Let's create a copy from samples 
$   sudo cp /usr/share/doc/netplan/examples/bridge.yaml /etc/netplan/my-bridge.yaml
```

<br/>

The schema of our bridge

```txt
                         ------------
                         |          |
192.168.1.0/24---ens33-==|  bridge  |==-ens38---10.10.1.0/24
		                 |          |
                         ------------
```

<br/>

```sh
# Edit our new file, while selecting ens33 and ens38
$   vi /etc/netplan/my-bridge.yaml
```

```yml
network:
  version: 2
  renderer: networkd
  ethernets:
    # Our 2 NICs
    ens33:
      dhcp4: no
    ens38:
      dhcp4: no
  bridges:
    br0:
      # Asking IP address for bridge br0
      dhcp4: yes
      interfaces:
        - ens33
        - ens38
```
```sh
# Apply changes
$   sudo netplan try
```
```sh
$   ip -c addr

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:82 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
3: ens37: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:96 brd ff:ff:ff:ff:ff:ff
    altname enp2s5
    inet6 fe80::20c:29ff:fed6:bb96/64 scope link 
       valid_lft forever preferred_lft forever
4: ens38: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP group default qlen 1000
    link/ether 00:0c:29:d6:bb:8c brd ff:ff:ff:ff:ff:ff
    altname enp2s6
5: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 82:2d:ef:fc:60:b2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::802d:efff:fefc:60b2/64 scope link 
       valid_lft forever preferred_lft forever
```

<br/>

#### 🔖 <ins>Bond</ins> (just an example of configuration but no demo)

```sh
# Let's create a copy from samples 
$   sudo cp /usr/share/doc/netplan/examples/bonding.yaml /etc/netplan/my-bond.yaml
```

<br/>

The schema of our bond

```txt
                         ----------          ========= ens33
                         |        |          |
192.168.1.0/24 ----------|  bond  | ---------|
		                 |        |          |
                         ----------          ========= ens37
```

<br/>

```sh
# Edit our new file, while selecting ens33 and ens38
$   vi /etc/netplan/my-bond.yaml
```

```yml
network:
  version: 2
  renderer: networkd
  ethernets:
    # Our 2 NICs
    ens33:
      dhcp4: no
    ens37:
      dhcp4: no
  bonds:
    bond0:
      addresses:
      - 192.168.1.221/24
      nameservers:
        addresses:
        - 192.168.1.100
      routes:
      - to: 192.168.1.0/24
        via: 192.168.1.1
      interfaces:
        - ens33
        - ens37
      parameters:
        # In mode 'active-backup' we lose connection
        mode: balance-tlb
```
```sh
# Apply changes
$   sudo netplan try
```
```sh
# Let's ping from outside
[centos-ad@centos-ad ~]$ ping 192.168.1.221

PING 192.168.1.221 (192.168.1.221) 56(84) bytes of data.
64 bytes from 192.168.1.221: icmp_seq=1 ttl=64 time=0.658 ms


# Let's SSH
[centos-ad@centos-ad ~]$ ssh ubuntu@192.168.1.221

The authenticity of host '192.168.1.221 (192.168.1.221)' can't be established.
ECDSA key fingerprint is SHA256:3xlqPFPXHIazy0OJz1n0cocGMq2FN169yc/Qb4XDTlU.
ECDSA key fingerprint is MD5:22:fd:bb:09:eb:89:60:c0:8f:53:a9:cb:a3:5e:3e:fd.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.221' (ECDSA) to the list of known hosts.
ubuntu@192.168.1.221's password: 
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure packet filtering</ins> (with `iptables` utility)

Before processing, we will need a context from the fake exam : we test connection to some portswhen some of them return a response.

```txt
➜ curl data-002:5000
app on port 5000

➜ curl data-002:6000
curl: (7) Failed to connect to data-002 port 6000 after 4 ms: Connection refused

➜ curl data-002:6001
app on port 6001

➜ curl data-002:6002
app on port 6002
```

- Check for existing iptables

```sh
$   iptables -L

# It is empty
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination 


# For NAT
$   iptables -L -t nat
```
```sh
$ ip -c link

2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:0c:29:d6:bb:82 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
```

<br/>

- Closing port 5000

```sh
# USE sudo
# --------
#
# -A      : for appending
# -p      : protocol
# --dport : destination port
# -j      : jump (what to do if packet matches)
$   sudo iptables -A INPUT -i eth0 -p tcp --dport 5000 -j DROP


Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
DROP       tcp  --  anywhere             anywhere             tcp dpt:5000
```
```sh
➜ root@data-002:~$ curl localhost:5000 # still works on localhost
app on port 5000

➜ root@data-002:~$ exit
➜ curl data-001:5000 # blocked from remote
curl: (7) Failed to connect to data-001 port 5000 after 0 ms: Connection refused
```

<br/>

- Port 6002 should only be accessible from IP 192.168.1.100

```sh
# The idea there is that we first allow that IP and then deny all other traffic on that port.
$   iptables -A INPUT -i eth0 -p tcp --dport 6002 -s 192.168.10.80 -j ACCEPT
$   iptables -A INPUT -i eth0 -p tcp --dport 6002 -j DROP
```
```sh
➜ curl data-002:6002
^C # timeout

➜ ssh data-001

➜ root@data-001:~$ curl data-002:6002
app on port 6002 # success
```

<br/>

- Blocking all outgoing traffic to IP 192.168.10.70

```sh
➜ root@data-002:~$ nc app-srv1 22
SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
^C # success
```
```sh
$   iptables -A OUTPUT -d 192.168.10.70 -p tcp -j DROP
```
```sh
➜ root@data-002:~$ nc app-srv1 22
^C # timeout. Now it is impossible to send requests to 192.168.10.70

➜ root@data-002:~$ nc data-001 22
SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1
^C # success
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Port Redirection, and NAT </ins> (with `iptables` utility)

- Perform some NAT for connections on port 6000 and redirect all traffic on port 6000 to local port 6001

```sh
# -t nat     : it SHOULD be Specified. The target is only valid in the nat table, in the PREROUTING and OUTPUT 
$   iptables -A PREROUTING -i eth0 -t nat -p tcp --dport 6000 -j REDIRECT --to-port 6001


Chain PREROUTING (policy ACCEPT)
target prot opt source destination
REDIRECT tcp -- anywhere anywhere tcp dpt:x11 redir ports 6001 # new rule
```
```sh
➜ root@data-002:~$ curl localhost:6000 # fail
curl: (7) Failed to connect to localhost port 6000 after 0 ms: Connection refused

➜ root@data-002:~$ exit
➜ curl data-002:6000 # fail
app on port 6001
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Implement reverse proxies and load balancers</ins> (with `nginx` utility)

#### 🔖 <ins>Reverse Proxy</ins>

To create a reverse proxy

```sh
$   sudo apt install nginx
$   sudo vi /etc/nginx/sites-available/proxy.conf

server {
  # Listening for incoming connections on port 80
  listen 80;
  # Only for requests that match the location (/) description :
  #
  # example.com/
  # example.com/admin
  # example.com/images/dog.jpg
  location/{
    proxy_pass http://1.1.1.1;
    # To load additional parameters from a config file
    include proxy_params;
  }
}
```
```sh
$   sudo vi /etc/nginx/proxy_params
# To see what users actually visit on the website
```

<br/>

We must pay attention to 2 specific folders :

- <mark>**`/etc/nginx/sites-available/`**</mark> :
  - All config files are defined there
- <mark>**`/etc/nginx/sites-enabled/`**</mark> :
  - The best practice is to create a **soft link** from `/sites-available/` to `/sites-enabled/` when we want to enable a website configuration

```sh
# To enable
$   sudo ln -s /etc/nginx/sites-available/proxy.conf /etc/nginx/sites-enabled/proxy.conf

# No forget to "disable" the default file
$   sudo rm /etc/nginx/sites-enabled/default


# To check config files for errors 
$   sudo nginx-t
```  

<br/>

To reapply the changes, simply reload

```sh
$   sudo systemctl reload nginx.service
```

<br/>

#### 🔖 <ins>Load Balancer</ins>

```sh
$   sudo vi /etc/nginx/sites-available/lb.conf
```
```sh
# Round robin config
upstream mywebservers{
  # Pick the server withe the least active connections from this list
  least_conn;
  # Process more requests than weaker servers, use weight. Default value is 1
  server 1.2.3.4 weight=3;
  server 5.6.7.8;
}

server {
  listen 80;
  location/{
    proxy_pass http://mywebservers;
  }
}
```
```sh
upstream mywebservers{
  least_conn;
  # To do maintenance work on this server
  server 1.2.3.4 weight=3 down;
  server 5.6.7.8;
  # To run a backup server if one of the main list goes down. Server with 'S'
  Server 10.20.30.40 backup;
}

server {
  listen 80;
  location/{
    proxy_pass http://mywebservers;
  }
}
```
```sh
upstream mywebservers{
  least_conn;
  # If web servers are not listening for incoming connections on the standard port 80
  server 1.2.3.4:8081;
  server 5.6.7.8;
  Server 10.20.30.40 backup;
}

server {
  listen 80;
  location/{
    proxy_pass http://mywebservers;
  }
}
```
```sh
# To enable
$   sudo ln -s /etc/nginx/sites-available/lb.conf /etc/nginx/sites-enabled/lb.conf


# To check config files for errors 
$   sudo nginx-t
``` 
```sh
$   sudo systemctl reload nginx.service
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Statically route IP traffic</ins>

When you need to configure a host as Router (**temporary**) :

```sh
# We tell computer A to send data to computer C at 10.0.0.100 to communicate with 192.168.0.0 network
$   sudo ip route add 192.168.0.0/24 via 10.0.0.100

# If we have multiple network based interfaces on the system, it is a good idea to specify the interface name where we want to apply this route
# 'dev' is an argument where we spcify the device name ens33 
$   sudo ip route add 192.168.0.0/24 via 10.0.0.100 dev ens33


# To delete the route
$   sudo ip route del 192.168.0.0/24
```
```sh
# To add a DEFAULT route (gateway) to route all traffic leaving our network
# It is an imaginary gate or a door through which data packets exit from our internal network
#
# If we want to send data to an IP address 1.2.3.4, and no specific route is found about how to reach it in the routing table, the data packet will be sent to the default route, the device at 10.0.0.100 in this case
$   sudo ip route add default via 10.0.0.100


# To delete the default route
$   sudo ip route del default via 10.0.0.100
```

<br>

#### 🔖 <ins>Permanent rules</ins>

Routes added with the **`ip`** command are temporary : they are lost at reboot. To make them permanent :

```sh
$   ip route show
default via 192.168.25.2 dev ens33 proto dhcp metric 100
192.168.25.0/24 dev ens33 proto kernel scope link src 192.168.25.136 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1


# First we need to see what Network Manager Connection is currently active
$   nmcli connection show

NAME    UUID                                  TYPE      DEVICE
ens33   0a56980a-355b-42ea-996f-6a239ea29995  ethernet  ens33
virbr0  7ab7c234-da8b-4b1c-a3f3-86ad1c2bc1a3  bridge    virbr0


# Route all traffic through 10.0.0.100 to reach 192.168.0.0/24
$   nmcli connection modify ens33 +ipv4.routes "192.168.0.0/24 10.0.0.100"


# To immediately apply 
$   nmcli device reapply ens33
Connection successfully reapplied to device 'ens33'.


$   ip route show
default via 192.168.25.2 dev ens33 proto dhcp metric 100
10.0.0.100 dev ens33 proto static scope link metric 100
# Our new route is active
192.168.0.0/24 via 10.0.0.100 dev ens33 proto static metric 100
192.168.25.0/24 dev ens33 proto kernel scope link src 192.168.25.136 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1


# To remove the route
$   nmcli connection modify ens33 -ipv4.routes "192.168.0.0/24 10.0.0.100"
$   nmcli device reapply ens33
$   ip route show
default via 192.168.25.2 dev ens33 proto dhcp metric 100
192.168.25.0/24 dev ens33 proto kernel scope link src 192.168.25.136 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Set and synchronize system time using time servers</ins>

Time related operations can be performed with the **`timedatectl`** utility.

```sh
# To list the available time zones
$    timedatectl list-timezones

Africa/Abidjan
...


# To set a timezone
$   timedatectl set-timezone Europe/Berlin

# To check our timezone. We see we are synchronized with a NTP server
$    timedatectl

      Local time: Wed 2023-09-13 18:32:24 CEST
  Universal time: Wed 2023-09-13 16:32:24 UTC
        RTC time: Wed 2023-09-13 16:32:23
       Time zone: Europe/Berlin (CEST, +0200)
     NTP enabled: yes
NTP synchronized: yes
...
```

<br>

#### 🔖 <ins>If there is no synchronization with a NTP server</ins>

```sh
# Install systemd-timesyncd utility (Ubuntu)
$   apt install systemd-timesyncd

# Turn on synchronization with NTP server
$   timedatectl set-ntp true

# We can also check the service status
$   systemctl status systemd-timesyncd.service

# To edit the settings of systemd time synchro daemon + restart
# We can for example specify a NTP server
$   vi /etc/systemd/timesyncd.conf
$   systemctl restart systemd-timesyncd.service
```
```sh
$   apropos -s 1,5,8 timesync
$   man timesyncd.conf
$   vi /etc/systemd/timesyncd.conf

NTP=0.pool.ntp.org 1.pool.ntp.org
FallbackNTP=ntp.ubuntu.com 0.debian.pool.ntp.org
PollIntervalMaxSec=1000
ConnectionRetrySec=20
```

&nbsp;
&nbsp;
&nbsp;

## Service Configuration

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure a caching DNS server</ins>

**To avoid** having our Linux server **asking again and again the IP** address of Google.com, and so reduces our request time from 50 ms to 20, 10, or 5 ms it will be <span style="color:#06C258">**a good idea to create our own DNS server**</span>, just like the ISP's one.


**`Bind`** is a popular application for hosting a DNS server.   

```sh 
# Install
# With 'bind-utils' we have extra programs that can be helpful when working with DNS servers : we have for example the Dig utility which can be used to query DNS information stored in this server
$   dnf install bind bind-utils
```

Bind's main configuration is located at **`/etc/named.conf`**

```sh
# Before editing the file, we should take a look at the server's current IP address (192.168.25.137)
$   ip a
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:84:a7:96 brd ff:ff:ff:ff:ff:ff
    inet 192.168.25.137/24 brd 192.168.25.255 scope global noprefixroute dynamic ens33
       valid_lft 1500sec preferred_lft 1500sec
    inet6 fe80::6855:affd:a4a1:7345/64 scope link noprefixroute
       valid_lft forever preferred_lft forever


# Let's see why this IP address is important
# We also look  at some lines
$   vi /etc/named.conf

options {
        # It means we only accept incoming connections from the programs running on the same OS, not from the outside world
        # For any connections -> listen-on port 53 { any; };
        listen-on port 53 { 127.0.0.1; };
        ...
        # As set now Bind will only allow loalhost to query and ask for information from its DNS database
        # That means means only programs running on the same OS can request DNS data from Bind
        allow-query     { localhost; };


        
        # This lets our Bind server get DNS data from other DNS servers on Internet when it does not have it available in its cache
        recursion yes;
```

<br/>

To make sure it auto starts at each boot

```sh
$   systemctl start named.service
$   systemctl enable named.service
```

<br/>

By default, our firewall blocks any incoming connections. If we want external computers to connect to our DNS Daemon, we need to add a new rule using our **`firewall-cmd`** command :

```sh
# Now connections to our DNS service are allowed
$   firewall-cmd --add-service=dns --permanent
success
```

<br/>

Using **`dig`** utility retrieve all record types :

```sh
# It took 788 ms for Bind service to request as it gets its data from other DNS servers
$   dig @127.0.0.1 google.com
...
;; Query time: 788 msec


# If we run the same command after, it took 0 ms. In the deafult configuration of our DNS server, it automatically caches DNS data
$   dig @127.0.0.1 google.com
...

# 216.239.34.10 is the IP address of ns2.google.com
# 172587 is the TTL (Time to Live) : the response will be cached for another 172587 s
# when it expires, the cache will be deleted
ns2.google.com.         172587  IN      A       216.239.34.10
;; Query time: 0 msec
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Maintain a DNS zone</ins>

A **Zone** can group DNS data for a specific domain. We could have a zone for **`dot.com`** or another domain.
 

#### 🔖 <ins>Example</ins>

Lets' imagine the we bought **`example.com`** and we own thus domain so that we can tell the Internet that if it needs the DNS entries for `example.com` : 

- it has to go to our DNS / Name server **`ns1.example.com`** (10.11.12.9) and ask for information.
- People enter **`www.example.com`** and DNS requests are sent to ns1.example.com to ask what is the IP address of `www.example.com`.

<br/>

Currently our Bind server can't anwser because it has no such entry available. Let's see how we can do this :

- The 1st thing to do is to edit the Bind main configuration file :

```sh
$   vi /etc/named.conf

zone "example.com" IN {
# Typing 'master' indicates us that this is the master data, the main information about the zone that can be found on the Internet. master server is the source of truth, whith the slave server can auto synchonize with the master and keep a sort of backup in case the master goes down.
        type master;

# It simply tells Bind where our zone file can be found. We will need this exact name for the next step
        file "example.com.zone";
};
```

<br/>

- The next step is to add the zone file, by using a preexisting file as a template : **`/var/named/named.localhost`**

```sh
# We do a copy, while preserving the same user and group owners. Otherwise the Bind daemon won't have permission to read it.
$   cp --preserve=ownership /var/named/named.localhost /var/named/example.com.zone
$   ls /var/named/
data  dynamic  example.com.zone  named.ca  named.empty  named.localhost  named.loopback  slaves


# Now we're going to edit this zone file
$   vi /var/named/example.com.zone

# $TTL 1H means we keep the DNS data in cache for 1 hour
# 
# IN                             the domain (Internet)
# SOA                            the type of entry (Start of Authority)
# administrator.example.com.     the data for the entry
#                                - as it is preceded by the @, this is the email 
#                                  address where we can obtain contact info
#                                 (@administrator.example.com)
$TTL 1H
# In this 1st @ the entry applies to the original domain (example.com)
@       IN SOA  @ administrator.example.com. (
            # serial field, incremented each time we make a change to our zone
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum

# NS for Name Server. 
# We are going to start telling the Internet where it can find DNS data for example.com 
# With only this info, the Internet could not know where example.com is located. This is why we will add the authoritative entries below with the IP addresses.
@               NS      ns1.example.com.
@               NS      ns2.example.com.
# Adding a A (authoritative) record for indicating the address IP of ns1 and ns2 (to be discovered by external devices).
# ns1 and ns2 don't have the DOT at the end : they are called subdomains
ns1             A       10.11.12.9
ns2             A       10.11.12.10

# Imagine we want people to be able to access our website when typing 'example' in their Web browsers. We can add a like like this to point to the address or IP address. 
# We begin with @, because it refers to example.com.
# 203.0.113.15 represents the public IP address of our site
@               A       203.0.113.15

# If people want to access through www.example.com
# Another solution, using a CNAME (canonical name). CNAME is almost like a redirection : www.example.com should go to the same place as example.com
# Only one of both solutions can be chosen
# www             A       203.0.113.15
www             CNAME   203.0.113.15



# MX (Mail Exchanger) record is used for resolving SMTP addresses. It tells other servers where they should send their emails
# Compared to the others, we have 4 fields instead of 3. 
# 10 and 20 represent the priority of each entry : when we send a mail John@example.com, we try to send 1st to mail.example.com
example.com.    MX 10   mail.example.com.
                MX 20   mail2.example.com.
# Like above, we need to precise the IP address
mail            A       203.0.113.80
mail2           A       203.0.113.81

example.com.    TXT     "We can write anything in here!"
```

- After having save the changes, we restart the service

```sh
$   systemctl restart named.service

# We can check if all changes are saved
$   dig @localhost example.com ANY

;; QUESTION SECTION:
;example.com.                   IN      ANY

;; ANSWER SECTION:
example.com.            3600    IN      TXT     "We can write anything in here!"
example.com.            3600    IN      MX      10 mail.example.com.
example.com.            3600    IN      MX      20 mail2.example.com.
example.com.            3600    IN      SOA     example.com. administrator.example.com. 0 86400 3600 604800 10800
example.com.            3600    IN      NS      ns1.example.com.
example.com.            3600    IN      NS      ns2.example.com.
example.com.            3600    IN      A       203.0.113.15

;; ADDITIONAL SECTION:
mail.example.com.       3600    IN      A       203.0.113.80
mail2.example.com.      3600    IN      A       203.0.113.81
ns1.example.com.        3600    IN      A       10.11.12.9
ns2.example.com.        3600    IN      A       10.11.12.10
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure email aliases</ins>

#### 🔖 <ins>Postfix</ins>

We'll create a simple mail server with a program called **`postfix`**.

```sh
$   dnf install postfix
$   systemctl start postfix
$   systemctl enable postfix
```

<br/>

Postfix accept emails at localhost. Let's test :

```sh
# Our user is cento, so we are not using aaron@localhost
$   sendmail cento@localhost <<< "Hello, I'm just testing email."
$   cat /var/spool/mail/cento

From root@localhost.localdomain  Sat Sep 16 10:44:58 2023
Return-Path: <root@localhost.localdomain>
X-Original-To: cento@localhost
Delivered-To: cento@localhost.localdomain
Received: by localhost.localdomain (Postfix, from userid 0)
        id A5BE7208B5EC; Sat, 16 Sep 2023 10:44:58 +0200 (CEST)
Message-Id: <20230916084458.A5BE7208B5EC@localhost.localdomain>
Date: Sat, 16 Sep 2023 10:44:58 +0200 (CEST)
From: root@localhost.localdomain (root)

Hello, I'm just testing email.
```

<br>

#### 🔖 <ins>Alias</ins>

Aliases are defined in this file and we will add a line for creating them : **`/etc/aliases`**

```sh
# ALIAS: USERNAME
$   vi /etc/aliases
advertising: cento

# Inform our Mail daemon (Postfix) about the new alias
$   newaliases

# Let's try sending email
$   sendmail advertising@localhost <<< "Hello, I'm just testing ads email."
$   cat /var/spool/mail/cento
From root@localhost.localdomain  Sat Sep 16 10:44:58 2023
Return-Path: <root@localhost.localdomain>
X-Original-To: cento@localhost
Delivered-To: cento@localhost.localdomain
Received: by localhost.localdomain (Postfix, from userid 0)
        id A5BE7208B5EC; Sat, 16 Sep 2023 10:44:58 +0200 (CEST)
Message-Id: <20230916084458.A5BE7208B5EC@localhost.localdomain>
Date: Sat, 16 Sep 2023 10:44:58 +0200 (CEST)
From: root@localhost.localdomain (root)

Hello, I'm just testing email.

From root@localhost.localdomain  Sat Sep 16 11:00:20 2023
Return-Path: <root@localhost.localdomain>
X-Original-To: advertising@localhost
Delivered-To: advertising@localhost.localdomain
Received: by localhost.localdomain (Postfix, from userid 0)
        id 5EC16208B5EE; Sat, 16 Sep 2023 11:00:20 +0200 (CEST)
Message-Id: <20230916090020.5EC16208B5EE@localhost.localdomain>
Date: Sat, 16 Sep 2023 11:00:20 +0200 (CEST)
From: root@localhost.localdomain (root)

Hello, I'm just testing ads email.
```

```sh
# To create an alias to multiple users (for a contact@example.com)
#
# /var/spool/mail/cento
# /var/spool/mail/john
# /var/spool/mail/jane
$   vi /etc/aliases
contact: cento, john, jane


# To route mail to an external mailbox stored at some other service
# This takes email coming at our server and forward to cento@somewebsite.com
$   vi /etc/aliases
advertising: cento@somewebsite.com
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure an IMAP and IMAPS service</ins>

>-  An email client like Outlook logs into Dovecot 
> - After successful login, Dovecot retrieves emails for the user (stored on our server)
> - Then Dovecot sends the emails to the email client.

<br/>

```sh
$   dnf install dovecot
$   systemctl start dovecot
$   systemctl enable dovecot
Created symlink from /etc/systemd/system/multi-user.target.wants/dovecot.service to /usr/lib/systemd/system/dovecot.service.
```

<br/>

- When we first start out, the firewall is going to be blocking incoming connections. If we want the outside world to be able to log into our IMAP service, we'll need to allow those types of connections.

```sh
$   firewall-cmd --add-service=imap
$   firewall-cmd --add-service=imaps
success

# Make them permanent
$   firewall-cmd --runtime-to-permanent
success
```

<br/>

- Let's configure an IMAP and IMAPS server. For this let's take a look at the main configuration file of Dovecot :

```sh
# Will enable both IMAP and IMAPS
$   vi /etc/dovecot/dovecot.conf
protocols = imap
listen = 10.11.12.9

# You can see a lot of conf files here
$   ls /etc/dovecot/conf.d/

10-auth.conf      10-ssl.conf        20-pop3.conf                 auth-deny.conf.ext        auth-sql.conf.ext

...

# If we want to change default ports, for listening on for incoming connections
# Example for IMAPS : change 993 to 789
$   vi /etc/dovecot/conf.d/10-master.conf

service imap-login {
  inet_listener imap {
    #port = 143
  }
  inet_listener imaps {
    port = 789
    #ssl = yes
  }


# To edit the default location of our stored emails
 # /var/mail is symbolically linked with /var/spool/mail
$   vi /etc/dovecot/conf.d/10-mail.conf

#   mail_location = maildir:~/Maildir
   mail_location = mbox:~/mail:INBOX=/var/mail/%u              
#   mail_location = mbox:/var/mail/%d/%1n/%n:INDEX=/var/indexes/%d/%1n/%n



# To secure traffic between our server and client, Dovecot needs a TLS (Transport Layer Security) certificate
# You can generate a TLS certificate with Certbot utility
$   vi /etc/dovecot/conf.d/10-ssl.conf

ssl = required

ssl_cert = </etc/pki/dovecot/certs/dovecot.pem
ssl_key = </etc/pki/dovecot/private/dovecot.pem
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure SSH servers and clients</ins>


#### 🔖 <ins>SSH server</ins>

```sh
$   vi /etc/ssh/sshd_config

# Default port
Port 22

# The AddressFamily is a bit obscure but it is going to give us a chance to find how we can get more info about some config options.
# Let's use 'man sshd_config' and search for 'Family', typing '/Family'
# Then if we want to accept incoming connections only from IPv4
AddressFamily inet

# If we have 2 IP addresses (203.0.113.1 connected on Internet and 10.11.12.9 connected on Intranet), we could tell ou daemon to only listen to the internal IP address (to be only accessible to employees from internal network)
ListenAddress 10.11.12.9

# Allow root user to log in from an SSH connection. To disable it
PermitRootLogin no

# Disabling Password authentication means we can only log in though SSH Keys (like Gitlab)
PasswordAuthentication no

# Running graphical application on server but from our local screen
X11Forwarding yes

# To only allow a Password authentication for a specific User (cento), even if disabled
PasswordAuthentication no
Match user cento
        PasswordAuthentication yes
```

- Reload service after having saved configs

```sh
$   systemctl reload sshd.service
```

<br/>

#### 🔖 <ins>SSH client</ins>

```sh
# Config file from client
$   vi /home/USER/.ssh/config

Host centos
        HostName 10.11.12.9
        Port 2322
        User aaron


$   chmod 600 /home/USER/.ssh/config

# Instead of using 'ssh aaron@10.11.12.9 -p 2322'
$   ssh centos
```
```sh
# Generate private public key
$   ssh-keygen

# Log in into the server with private key : for this we have to copy the public key to the server
# Content of public key will be copied to cento's home directory, into the .ssh/authorized_keys file
$   ssh-copy-id cento@10.11.12.9


$   chmod 600 authorized_keys    
```
```sh
# To remove an old fingerprint
$   ssh-keygen -R 10.11.12.9
```
```sh
# Config file for SSH client from server
$   vi /etc/ssh/ssh_config

# We can customize SSH client connection in this folder by creating our .conf files
$   ls /etc/ssh/ssh_config.d/

# Here we define another default port
$   vi /etc/ssh/ssh_config.d/99-our-settings.conf
Port 229
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Restrict access to the HTTP proxy server</ins>

- Let's install **`Squid`** and auto-start the service

```sh
$   dnf install squid -y
$   systemctl start squid
$   systemctl enable squid
Created symlink from /etc/systemd/system/multi-user.target.wants/squid.service to /usr/lib/systemd/system/squid.service.
```


<br/>

- We add rules to our firewall

```sh
$   firewall-cmd --add-service=squid --permanent
success
```

<br/>

- Let's fine tune who can access the proxy server and what they can access, so we can add access rules to a specific file **`/etc/squid/squid.conf`**.

>These ACL lines, alone, don't do anything : they basically jsut define some filters but do not apply them. After added these lines, we need to specifically allow or deny access to each ACL.

<br/>

#### ⚠️ <mark>NOTE</mark> :

> When creating your rules, Squid will look at the 1st rule and so on : it means that you have to design well your rules to apply them **<ins>in a specific order</ins>**
```sh
# acl           Access Control List
# localnet      The name of the acl. We can choose any name we want
# src           Source type
# 10.0.0.0/8    The rul will apply to any network traffic coming from an Ip starting with 10. 
$   vi /etc/squid/squid.conf

# If we want a set of IP to have access to our proxy
acl external src 203.0.113.0/24

# This defines an ACL, based on the destination port.
acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp

# This rule says, when someone tries to access something though HTTP (http_access), access is denied if the port does not belong to 'Safe_ports' (80, 21, etc.)
http_access deny !Safe_ports


# To deny access to youtube.com and all of its subdomains
acl youtube dstdomain .youtube.com
http_access deny youtube

# To deny access ONLY to youtube.com, remove the DOT
acl youtube dstdomain youtube.com
http_access deny youtube
```

- Once chnages are saved, we reload the service.

```sh
$   systemctl reload squid.service

# If we want to make them available immedialtly, with the irsk in disrupting current sessions
$   systemctl restart squid.service
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure an HTTP server</ins>

- Install Apache server

```sh
$   dnf install httpd -y
```

- The Daemon listens for incoming connections on port 80 by default. We need to allow HTTP/HTTPS with firewall

```sh
$   firewall-cmd --add-service=http --permanent
$   firewall-cmd --add-service=https
$   firewall-cmd --runtime-to-permanent
```

<br/>

#### 🔖 <ins>HTTP</ins>

- Let's do some tests to navigate to some different Web pages, using the port 80 or 8080. Below are some important properties to pay attention.

```sh
$   vi /etc/httpd/conf/httpd.conf

# Listen 10.11.12.9:8080
Listen 80

ServerAdmin webserver@example.com

# ServerName www.example.com:80
# ServerName 10.11.12.9
ServerName localhost

DocumentRoot "/var/www/html"
```

<br/>

#### ⚠️ <mark>NOTE</mark> :

- For the following example : our HTTP server will host 2 websites, **Blog** & **Store**

```sh
# Let's create a new file
$   vi /etc/httpd/conf.d/two-websites.conf

# Let's add virtual hosts 
# Our Virtual Host is VirtualHost *:80, so our server might have 2 or more IP addresses.
# For example HTTp daemon might listen for incoming connections on both internal (10.11.12.9) and external network (203.0.113.5)
#
# That line tells our HTTPD it should use these settings in VirtualHost, no matter from which IP the website visitor is connected to (internal or external).
<VirtualHost *:80>
        ServerName store.example.com
        DocumentRoot "/var/www/store/"
</VirtualHost>

<VirtualHost *:80>
        ServerName blog.example.com
        DocumentRoot "/var/www/blog/"
</VirtualHost>


# For only access from private network, to serve a private website
<VirtualHost 10.11.12.9:80>
        xxx
        xxx
</VirtualHost>
```
```sh
# A tool to check your Apache Configs
$   apachectl configtest

AH00112: Warning: DocumentRoot [/var/www/store/] does not exist
AH00112: Warning: DocumentRoot [/var/www/blog/] does not exist
# AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```

<br/>

- Let's create some **`.html`** files

```sh
$   echo "This is the MAIN website" > /var/www/html/index.html
$   echo "This is the BLOG" > /var/www/blog/index.html
$   echo "This is the STORE" > /var/www/store/index.html
```

<br/>

#### 🔖 <ins>HTTPS</ins>

- To enable HTTPS, we're going to generate some TLS certificates and configure the Daemon to use HTTPS. We can also change the Web server administrator.

```sh
$   dnf install mod_ssl -y
$   dnf install certbot -y
```

```sh
$   vi /etc/httpd/conf.d/ssl.conf

Listen 443 https
```

<br/>

- Let's create our certificate (you need an official email account) :

```sh
# The Apache plugin will take care of reconfiguring Apache and reloading the configuration whenever necessary
$   certbot --apache
Saving debug log to /var/log/letsencrypt/letsencrypt.log
The requested apache plugin does not appear to be installed


$   dnf install python-certbot-apache
$   certbot --apache

Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator apache, Installer apache
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): admin@example.com
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

# Then fo thorugh the process ...
```

<br/>

- While using **`certbot`**, it will usually add new configuration files for https enabled websites.

```sh
# Config for Virtual host in HTTPS
<VirtualHost *:443>
        ServerName www.example.com
        SSLEngine on
        SSLCertificateFile "/path/to/file.cert"
        SSLCertificateKeyFile "/path/to/file.key"
</VirtualHost>
```

- As httpd is modular, you can find modules here. For enabling some functionnalities we have to edit a file and uncomment :

```sh
$   ls /etc/httpd/conf.modules.d/

00-base.conf  00-dav.conf  00-lua.conf  00-mpm.conf  00-proxy.conf  00-ssl.conf  00-systemd.conf  01-cgi.conf
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Configure HTTP server log files</ins>

**`httpd`** keeps track of important events : it stores data in :

- `error.log`
- `access.log` : who visits the website, what pages they accessed, what Web browser they used, etc.

<br/>

The default logging settings ared defined in :

```sh
$   cat /etc/httpd/conf/httpd.conf

# /etc/httpd is also a symlink (symbolic Linux/ UNIX link that points to another file or folder on your computer, or a connected file system). It points to /var/log/httpd/
ServerRoot "/etc/httpd"
ErrorLog "logs/error_log"

# LogLevel: Control the number of messages logged to the error_log.
# Possible values include: debug, info, notice, warn, error, crit,
# alert, emerg.
#
LogLevel warn


    #
    # If you prefer a logfile with access, agent, and referer information
    # (Combined Logfile Format) you can use the following directive.
    #
    CustomLog "logs/access_log" combined
```

<br/>

In our Virtual Hosts, we can configure the path to logs :

```sh
<VirtualHost *:80>
        ServerName store.example.com
        DocumentRoot "/var/www/store/"
        # Using the 'combined' format
        CustomLog "/var/log/httpd/store.example.com_access.log" combined
        ErrorLog "/var/log/httpd/store.example.com_error.log"
</VirtualHost>
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Restrict access to a web page</ins>

With the 2 created Virtual Hosts, we will learn about disabling them. 

- Let's create a disabled file

```sh
$   mv /etc/httpd/conf.d/two-websites.conf /etc/httpd/conf.d/two-websites.conf.disabled
$   ystemctl reload httpd.service
```

- Let's create 3 files into `/var/www/html/admin`

```sh
$   mkdir /var/www/html/admin
$   touch /var/www/html/admin/file{1..3}
```

```sh
# Let's take a look at how the default config file restricts or allows access to DocumentRoot
$   vi /etc/httpd/conf/httpd.conf

DocumentRoot "/var/www/html"

<Directory "/var/www/html">
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    # - Leaving 'Indexes' makes the page http://localhost/admin/ accessible 
    # - Removing 'FollowSymLinks' avoids us to download a file when clicking on a link
    # Options Indexes FollowSymLinks
    Options FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>


# This config will allow access to the Web page, only from these IP
<Directory "/var/www/html/admin/">
    Require ip 192.168.1.79 203.0.1.113
</Directory>
<Directory "/var/www/html/admin/">
    # Allow access for 192.168.1.1 through 192.168.1.255
    Require ip 192.168.1
</Directory>

#
# The following lines prevent .htaccess and .htpasswd files from being
# viewed by Web clients.
#
<Files ".ht*">
    Require all denied
</Files>
```

<br/>

- We can also protect a sensitive area of our website with a username and password

```sh
# We generate this file (a password for the website access)
$   htpasswd -c /etc/httpd/passwords cento
New password:
Re-type new password:
Adding password for user cento


$   cat /etc/httpd/passwords
cento:$apr1$QNnV/DyV$Pwj/d7yNxY1VP2sSkJ/Uv/


# To add another entry
$   htpasswd /etc/httpd/passwords john

# To delete  entry
$   htpasswd -D /etc/httpd/passwords john
```
```sh
$   vi /etc/httpd/conf/httpd.conf

<Directory "/var/www/html/admin/">
    AuthType Basic
    AuthBasicProvider File
    AuthName "Secret Admin Page"
    AuthUserFile /etc/httpd/passwords
    Require valid-user
</Directory>
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Manage and configure Virtual Machines (Qemu)</ins>

```sh
# libvirt includes utilities that let us interact with VM
# qemu-kvm lets us create and run them
$   dnf install libvirt qemu-kvm
```

<br/>

Next we create a <span style="color:#06C258">**configuration file**</span>. Here we neglect the hard disks and network card :

```sh
# hvm means harware virtual machine
$   vi testmachine.xml

<domain type="qemu">
  <name>TestMachine</name>
  <memory unit="GiB">1</memory>
  <vcpu>1</vcpu>
  <os>
    <type arch='x86_64'>hvm</type>
  </os>
</domain>
```

<br/>

And we create the virtual machine :

```sh
$   virsh define testmachine.xml
Domain TestMachine defined from testmachine.xml
```

<br/>

The machine doesn't start automatically. Let's start it :

```sh
$   virsh start TestMachine
Domain TestMachine started


$   virsh list --all

 Id    Name                           State
----------------------------------------------------
 1     TestMachine                    running
```

<br/>

To reboot the machine :

```sh
$   virsh reboot TestMachine

Domain TestMachine is being rebooted

# Forcing a reboot
$   virsh reset TestMachine
```

<br/>

To shutdown the machine :

```sh
$   virsh shutdown TestMachine

Domain TestMachine is being shutdown

# Forcing a shutdown
$   virsh destroy TestMachine
```

<br/>

To destroy the machine with all its storage (**`--remove-all-storage`**) :

```sh
$   virsh undefine --remove-all-storage TestMachine

Domain TestMachine has been undefined
```

<br/>

To get resource info :

```sh
$   virsh dominfo TestMachine

Id:             -
Name:           TestMachine
UUID:           a28a3b96-0671-486d-995c-ffb47d3d25e7
OS Type:        hvm
State:          shut off
CPU(s):         1
...


# Setting vCPU max to 2
$   virsh setvcpus TestMachine 2 --config
error: invalid argument: requested vcpus is greater than max allowable vcpus for the persistent domain: 2 > 1


$   virsh setvcpus TestMachine 2 --config --maximum
$   virsh setvcpus TestMachine 2 --config

# Setting RAM
$   virsh setmaxmem TestMachine 2048M --config
```

&nbsp;
&nbsp;
&nbsp;

## Storage Management

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>List, create, delete, and modify physical storage partitions</ins>

Imagine we have a 2 Tb SSD and we want to install Windows and Linux :

- Windows uses the NTFS file system
- Linux uses EXT4 file system

<br/>

We can divide (partition) the SSD into 2 partitions.

- To see what partitions exist on Linux system :

```sh
# S from 'SDA' means serial. That's where any devices that may be connected to a SATA port like SSD on motherboard.
#
# /dev/sda points to the device
# /dev/sda1 points to the partition
$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
sr0     11:0    1 1024M  0 rom


# This partition utilisty shows the list of partitions of the block device /dev/sda
# A storage is divided into sectors
#
# If /dev/sda1 is at sector 2048 (Start) and we multiply by 512 (sector size) that leaves us with 1,048,576 bytes, which is 1 Mbytes : it means that that this partition has 1 Mbytes of free space before it (scetor 0 to 2047 are not partitioned).
#
# This is done in case of a bootloader needs to be installed in that area. 
$   fdisk -l /dev/sda

Disk /dev/sda: 32.2 GB, 32212254720 bytes, 62914560 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009c911

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048      616447      307200   83  Linux
/dev/sda2          616448     6907903     3145728   82  Linux swap / Solaris
/dev/sda3         6907904    62914559    28003328   83  Linux
```

<br/>

- We've added a 2nd storage device : let's create the partition

```sh
# We can see there is no partition on /dev/sdb
$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
sr0     11:0    1 1024M  0 rom


# Before creating a partition, we need to decide if we'll use MBR or GPT.
# If we're working with VERY OLD hardware ---> we must use MBR partition tables
#                                         ---> we select 'dos' in our label type selection 
# If recent hardware                      ---> we select 'gpt' in our label type selection 
$   cfdisk /dev/sdb
```
```sh
# Step 01 : New -> Primary -> 2G -> Beginning -> Bootable -> Write
# Step 02 : New -> Logical -> 4G -> Beginning -> Write
# Step 03 : New -> Logical -> 2G -> Beginning -> Type -> 'Linux Swap' -> Write
# Step 04 : Quit
                        cfdisk (util-linux 2.23.2)

                           Disk Drive: /dev/sdb
                      Size: 8589934592 bytes, 8589 MB
           Heads: 255   Sectors per Track: 63   Cylinders: 1044

   Name        Flags     Part Type FS Type         [Label]       Size (MB)
 -------------------------------------------------------------------------
   sdb1        Boot       Primary  Linux                           1998.75    
   sdb5                   Logical  Linux                           3997.49
   sdb6                   Logical  Linux swap / Solaris            1998.75
                          Pri/Log  Free Space                       594.97*
```
```sh
# We have created the partition /dev/sdb6 to be used as swap. It is currently empty with no data inside.
# Before it can be used as swap, it has to be prepared.
# What we have done before was basically to write some small data on the partition labels, so the system knows this is meant to be used as a swap area.
$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom
```

<br> 

#### ⚠️ <mark>NOTE</mark> :

> We have /dev/vdb disk on this system. Perform the following actions on it.
>
> - Create a partition **`/dev/vdb1`** of **400MB** in size and format it as XFS file system. 
>
> - Create a partition **`/dev/vdb2`** of **100MB** in size, format it as ext4 file system and mount it in /mnt/ . 
   We want to keep some sensitive data on ext4 filesystem. 
>
> - Create a partition **`/dev/vdb3`** of **450MB** in size and format it with the xfs filesystem. 
   To make this easier to spot in the future, among the other filesystems, set the filesystem label to **ExamFS** when you format it.

<br/>

```sh
$  lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda    253:0    0   11G  0 disk 
└─vda1 253:1    0   10G  0 part /
vdb    253:16   0    1G  0 disk 
vdc    253:32   0    1G  0 disk 
vdd    253:48   0    1G  0 disk 
vde    253:64   0    1G  0 disk 
└─vde1 253:65   0 1023M  0 part 
```
```sh
$  fdisk /dev/vdb

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): 
Partition number (1-4, default 1): 
First sector (2048-2097151, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-2097151, default 2097151): 400M
Value out of range.
Last sector, +sectors or +size{K,M,G,T,P} (2048-2097151, default 2097151): +400M

Created a new partition 1 of type 'Linux' and of size 400 MiB.


Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (2-4, default 2): 
First sector (821248-2097151, default 821248): 
Last sector, +sectors or +size{K,M,G,T,P} (821248-2097151, default 2097151): +100M

Created a new partition 2 of type 'Linux' and of size 100 MiB.


Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (3,4, default 3): 
First sector (1026048-2097151, default 1026048): 
Last sector, +sectors or +size{K,M,G,T,P} (1026048-2097151, default 2097151): +450M

Created a new partition 3 of type 'Linux' and of size 450 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

<br/>

```sh
$  lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda    253:0    0   11G  0 disk 
└─vda1 253:1    0   10G  0 part /
vdb    253:16   0    1G  0 disk 
├─vdb1 253:17   0  400M  0 part 
├─vdb2 253:18   0  100M  0 part 
└─vdb3 253:19   0  450M  0 part 
vdc    253:32   0    1G  0 disk 
vdd    253:48   0    1G  0 disk 
vde    253:64   0    1G  0 disk 
└─vde1 253:65   0 1023M  0 part 
```

<br/>

```sh
# Format the 3 partitions
$  mkfs.xfs /dev/vdb1

meta-data=/dev/vdb1              isize=512    agcount=4, agsize=25600 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=102400, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=1368, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0


$  mkfs.ext4 /dev/vdb2

mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 102400 1k blocks and 25688 inodes
Filesystem UUID: 959786fa-1d0f-49c2-a230-abead2d1868b
Superblock backups stored on blocks: 
        8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done 


$  mkfs.xfs -L "ExamFS" /dev/vdb3

meta-data=/dev/vdb3              isize=512    agcount=4, agsize=28800 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1
data     =                       bsize=4096   blocks=115200, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=1368, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure and manage swap space</ins>

**Swap** is an area where Linux can temporarily move some data from RAM memory.

#### 🔖 <ins>Commands</ins>

To check if the system uses any kind of swap areas.

```sh
$   swapon --show

NAME      TYPE      SIZE USED PRIO
/dev/sda2 partition   3G   0B   -2
```

We tell Linux to use the partition to swap


```sh
# Using /dev/sdb6 for swaping
$   mkswap /dev/sdb6

Setting up swapspace version 1, size = 1951860 KiB
no label, UUID=02d3ad30-52a8-4383-ac1c-3ad587129175

# Then, swap /dev/sdb6
$   swapon --verbose /dev/sdb6

swapon /dev/sdb6
swapon: /dev/sdb6: found swap signature: version 1, page-size 4, same byte order
swapon: /dev/sdb6: pagesize=4096, swapsize=1998708736, devsize=1998710784
```
```sh
$   swapon --show

NAME      TYPE      SIZE USED PRIO
/dev/sda2 partition   3G   0B   -2
/dev/sdb6 partition 1.9G   0B   -3


$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part [SWAP]
```

#### ⚠️ <mark>NOTE</mark> :

> **However there is a problem :** if we reboot the machine, /dev/sdb6 won't be used as swap <span style="color:#FF8A8A"><ins>**anymore**</ins></span>. We will see later how to automatically use a swap every time the OS boots up. 

<br/>

To stop using a partition as swap

```sh
$   swapoff /dev/sdb6

$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom
```

<br/>

#### 🔖 <ins>File as swap</ins>

Instead of partitions, we can also use **<ins>simple files</ins>** as swap.

First we need to create an empty file and fill it with 0 : that is **binary zeros**.

```sh
# 'if=/dev/zero' tells the utility 'dd' to use the input file at /dev/zero
#                /dev/zero is a special device file that generates an infinite number of 0 when 
#                          an application reads from it.
# 'of=/swap' tells to copy the input file to output file /swap
# 'bs=1M count=128' tells to write a block size of 1M 128 times.
# 'status=progress' shows the progress status
$   dd if=/dev/zero of=/swap bs=1M count=128 status=progress

128+0 records in
128+0 records out
134217728 bytes (134 MB) copied, 0.209511 s, 641 MB/s
```
Regular Users should not be allowed to read this swap file : that is because this potentially gives them access to the memory contents of programs other users might be using.

We'll set permissions to only allow the root user to read and write this file.

```sh
$   chmod 600 /swap
```

<br/>

Formatting this file as a swap is the same as before. But instead of using a file like `/dev/sdb6` that points to a partition, we will just use a <span style="color:#FF8A8A">**regular file**</span> in our command.

```sh
$   mkswap /swap

Setting up swapspace version 1, size = 131068 KiB
no label, UUID=2f28013a-8384-4729-a1bc-f789ad880f9d


# Now we can use this as swap on the file system
$   swapon --verbose /swap

swapon /swap
swapon: /swap: found swap signature: version 1, page-size 4, same byte order
swapon: /swap: pagesize=4096, swapsize=134217728, devsize=134217728
```
```sh
$   swapon --show

NAME      TYPE      SIZE USED PRIO
/dev/sda2 partition   3G   0B   -2
/swap     file      128M   0B   -3
```


&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Create and configure file systems</ins>

To be able to store files and directories inside a partition, we first need to create a file system on it using the **`mkfs`** utility :

- `mkfs.xfs`
- `mkfs.ext4`

```sh
$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom


# Create XFS file system on CentOS 7
$   mkfs.xfs -L "BackupVolume" /dev/sdb5

meta-data=/dev/sdb5              isize=512    agcount=4, agsize=243985 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=975940, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

<br/>

XFS comes with built-in modules. Let's say we want to change the label.

```sh
$   xfs

xfs_admin      xfs_db         xfs_freeze     xfs_info       xfs_logprint   xfs_mkfile     xfs_repair

...


# Display label
$   xfs_admin  -l /dev/sdb5
label = "BackupVolume"


# To change label
$   xfs_admin  -L "FirstFS" /dev/sdb1

writing all SBs
new label = "FirstFS"


$   xfs_admin  -l /dev/sdb5
label = "FirstFS"
```

```sh
# To change label for EXT4, first see 'man mkfs.ext4' to find about tune2fs
$   tune2fs  -L "SecondFS" /dev/sdb2
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure systems to mount file systems during boot</ins>

Let's see how to configure Linux systems to mount file systems at ordering boot time. There is a directory often used for temporarily mounting a random file system : **`/mnt/`**.

<br/>

- We want to mount the XFS file system, stored on the partition `/dev/sdb5`

```sh
$   mount /dev/sdb1 /mnt/
```

- With the file system mounted at `/mnt`, we can easily create a file on it

```sh
$   touch /mnt/testfile
$   ls -l /mnt/

total 0
-rw-r--r--. 1 root root 0 Sep 21 19:07 testfile


$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part /mnt
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom
```

- To unmount

```sh
$   umount /mnt/

$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom
```

<br/>

#### ⚠️ <mark>NOTE</mark> :

> **If we want to know the UUID of a partition :** if we connect wrongly hardwares on motherboard, partitions will not match the real disks. 
>
> By specifying the UUID instead, we avoid a bad result at OS boot.

```sh
$   lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0 17.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part [SWAP]
sr0     11:0    1 1024M  0 rom


$   blkid /dev/sdb5
/dev/sdb5: LABEL="BackupVolume" UUID="4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4" TYPE="xfs"
```

<br/>

When a Linux OS boots up, it automatically mount some file systems (`/boot`) : it does this according to instructions in a file **`/etc/fstab`**.

```sh
# We create a directory where we will mount /dev/sdb5
$   mkdir /mybackups/

# The 1st field points to the block device that represents a partition or some kind of storage space
# The 2nd field (/boot) specifies the mount point (the directory where we want to mount our file system)
# The 3rd field specifies the file system type
# The 4th field (defaults) will contain the mount options. defaults is the default mounting.
# The 5th field (0) is an utility called dump. It should backup this file system. 0 means that backup is disabled.
#
# While systems can get sometimes corrupted, the last field (0) decides what happens when errors are detected. 3 possible values : 0,1 or 2.
# In practice we often write 1 for the root file system where the OS is installed, 2 for all other file systems.
$   vi /etc/fstab

UUID=6b82faad-2059-4ea8-bf1f-eb7c1fa74dc0 /                       xfs     defaults        0 0
UUID=fb381173-b9a2-4660-8d46-33e2740020ac /boot                   xfs     defaults        0 0
UUID=fe94ec59-524c-48d9-b1e0-efa52a69854f swap                    swap    defaults        0 0

#/dev/sdb5                                 /mybackups              xfs     defaults        0 2
UUID=4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4 /mybackups              xfs     defaults        0 2


# To mount a swap partition
# 0 0 --> swap is not meant to be backup or scanned for errors
/dev/sdb6                                 none                    swap    defaults        0 0



# After editingthis file, we need to update our system daemon
$   systemctl daemon-reload
```

<br/>

The `lsblk` doesn't show any changes. This is when we reboot that changes are taking effect

```sh
$   ls -l /mybackups/
total 0

$   lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    2G  0 part [SWAP]
└─sda3   8:3    0 17.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part /mybackups
└─sdb6   8:22   0  1.9G  0 part [SWAP]
sr0     11:0    1 1024M  0 rom


# SWAP partition
$   swapon --show
NAME      TYPE      SIZE USED PRIO
/dev/sda2 partition   2G   0B   -2
/dev/sdb6 partition 1.9G   0B   -3



# We reboot the OS
$   systemctl reboot

# We now verify the changes and we can see that our partition is auto mounted
$   ls -l /mybackups/
total 0
-rw-r--r--. 1 root root 0 Sep 21 19:07 testfile


$   lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk
├─sda1   8:1    0  300M  0 part /boot
├─sda2   8:2    0    3G  0 part [SWAP]
└─sda3   8:3    0 26.7G  0 part /
sdb      8:16   0    8G  0 disk
├─sdb1   8:17   0  1.9G  0 part /mybackups
├─sdb2   8:18   0    1K  0 part
├─sdb5   8:21   0  3.7G  0 part
└─sdb6   8:22   0  1.9G  0 part
sr0     11:0    1 1024M  0 rom 


# SWAP partition auto mounted
$   swapon --show

NAME      TYPE      SIZE USED PRIO
/dev/sdb6 partition 1.9G   0B   -2
/dev/sda2 partition   3G   0B   -3
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Configure systems to mount file systems on demand</ins>

> a computer can boot up and nothing will be mounted in `/backups/` but as soon as a user or a program tries to read or write to this directory, the OS mounts what is required. When not required, it is unmounted automatically.
>
> We call this **on demand mounting**

This method is used specifically **for remote file systems** : it is *<ins>for avoiding a lot of traffic network</ins>*, if we have 100 servers that must communicate with this remote file system.

<br/>

#### 🔖 <ins>Network File Sharing</ins>

#####  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Solution 1

When we want to mount a file system or a directory stored on a different server, the most often used solution is the **network file sharing**.

```sh
$   dnf install nfs-utils -y
$   systemctl start nfs-server.service
$   systemctl enable nfs-server.service
```
```sh
$   mkdir /remotedir
$   touch /remotedir/remote_file_1
$   touch /remotedir/remote_file_2
```
Next we need to tell the NFS server what directories it should share to the network, by using the file **`/etc/exports`**.
```sh
$   vi /etc/exports
# remotedir directory should be accessible to computers having the IP address 127.0.0.1. ro for read-only
/remotedir 127.0.0.1(ro)
```
```sh
# To apply changes
$   sudo exportfs -ra

# OR
$   systemctl reload autofs.service
```

<br/>

#####  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Solution 2

We can also use the **`sshf`** utility.

```sh
# Use SSHFS to mount directory /data-export from server app-srv1 to /app-srv1/data-export . 
# The mount should be read-write and option allow_other should be enabled.
$   sudo mkdir -p /app-srv1/data-export
$   sudo sshfs -o allow_other,rw app-srv1:/data-export /app-srv1/data-export
```

<br/>

#### 🔖 <ins>On Demand Mounting Utility</ins>

The most often used utility is **`autofs`**.

```sh
$   dnf install autofs -y
$   systemctl start autofs.service
$   systemctl enable autofs.service
```

To mount on demand, edit **`/etc/auto.master`** and **`/etc/auto.shares`**

```sh
$   vi /etc/auto.master
# We use /shares/ for auto mounting. If not exists, we automatically create it
# Automatically options for /shares/ are defined in /etc/auto.shares 
# We unmount the directory if not used for 400 s (by default : 300 s)
/shares/ /etc/auto.shares --timeout=400


# mynetworkshare is the resulting directoring, when mounting /shares/
# 'fstype=auto' : we tell autofs to auto detect the file system type
# '127.0.0.1:/remotedir' is the location of our NFS share. It tells us to mount the /remotedir directory from the NFS server that can be found at 127.0.0.1
$   vi /etc/auto.shares
mynetworkshare -fstype=auto 127.0.0.1:/remotedir


# We can also use this utility for local mounting
$   vi /etc/auto.shares
myext4files -fstype=auto :/dev/sdb2
```

<br/>

With `/etc/auto.master` and `/etc/auto.shares` edited, we can now reload autofs.

```sh
$   systemctl reload autofs.service

# We'll see that it's empty and nothing is mounted yet
$   ls /shares/
$   systemctl reload nfs-server.service


# shares is the parent directory, mynetworkshare is the child
$   ls -l /shares/
total 0
drwxr-xr-x. 2 root root 48 Sep 22 11:25 mynetworkshare

$   ls -l /shares/mynetworkshare
total 0
-rw-r--r--. 1 root root 0 Sep 22 11:25 remote_file_1
-rw-r--r--. 1 root root 0 Sep 22 11:25 remote_file_2
```

<br/>

#### ⚠️ <mark>NOTE</mark> :

> **If we don't want the parent-child scheme :** we just want to point directly to the child directory.

```sh
# With '/-' : we basically say there is no parent directory for the on demand mount points.
$   vi /etc/auto.master
# /shares/ /etc/auto.shares --timeout=400
/- /etc/auto.shares --timeout=400


# We need to define the absolute path
$   vi /etc/auto.shares
# mynetworkshare -fstype=auto 127.0.0.1:/remotedir
# myext4files -fstype=auto :/dev/sdb2

/mynetworkshare -fstype=auto 127.0.0.1:/remotedir
/localfiles/myext4files -fstype=auto :/dev/sdb2


$   systemctl reload autofs.service
$   systemctl reload nfs-server.service


$   ls -l /shares/
ls: cannot access /shares/: No such file or directory

$   ls -l /mynetworkshare/
total 0
-rw-r--r--. 1 root root 0 Sep 22 11:25 remote_file_1
-rw-r--r--. 1 root root 0 Sep 22 11:25 remote_file_2
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Evaluate and compare the basic file system features and options</ins>

`lsblk` gives us a nice overwiew of what is mounted and where. But what if we want more details ? 

- What file system is mounted ?
- What are the mount options for this file system ?

<br/>

First let's look at another command that shows everything that is mounted on the system.

```sh
$   findmnt

TARGET                                SOURCE           FSTYPE     OPTIONS
/                                     /dev/sda3        xfs        rw,relatime,seclabel,attr2,inode64,noquota
├─/sys                                sysfs            sysfs      rw,nosuid,nodev,noexec,relatime,seclabel
│ ├─/sys/kernel/security              securityfs       securityfs rw,nosuid,nodev,noexec,relatime
│ └─ ...
├─ ...
├─/mybackups                          /dev/sdb5        xfs        rw,relatime,seclabel,attr2,inode64,noquota
├─/var/lib/nfs/rpc_pipefs             sunrpc           rpc_pipefs rw,relatime
├─ ...
└─ ...

# Only show XFS file system
# OPTIONS represents the Mount options
$   findmnt -t xfs

TARGET       SOURCE    FSTYPE OPTIONS
/            /dev/sda3 xfs    rw,relatime,seclabel,attr2,inode64,noquota
├─/boot      /dev/sda1 xfs    rw,relatime,seclabel,attr2,inode64,noquota
└─/mybackups /dev/sdb5 xfs    rw,relatime,seclabel,attr2,inode64,noquota
```

<br/>

**Mount Options** tells the file system how to behave, what rules it should follow while it is mounted. We can mount a file system with specific options :

```sh
# We can make a partition like /dev/sdb2 read-only and mount it on /mnt/
#
# noexec : makes impossible to launch a program stored in this file system
# nosuid : disables the SID permission that allow programs to run with root privileges without needing the sudo command 
$   mount -o ro,noexec,nosuid /dev/sdb2 /mnt


$   findmnt -t xfs,ext4
TARGET       SOURCE    FSTYPE OPTIONS
/            /dev/sda3 xfs    rw,relatime,seclabel,attr2,inode64,noquota
├─/boot      /dev/sda1 xfs    rw,relatime,seclabel,attr2,inode64,noquota
├─/mnt       /dev/sdb2 ext4   ro,noexec,nosuid,relatime,seclabel
└─/mybackups /dev/sdb5 xfs    rw,relatime,seclabel,attr2,inode64,noquota


$   touch /mnt/testfile
touch: cannot touch '/mnt/testfile': Read-only file system
```

<br/>

If we want to remount a partition after option changes, use the **`remount`** option

```sh
$   mount -o remount,rw,noexec,nosuid /dev/sdb2 /mnt
```

For specifying the mount options at OS boot, edit **`/etc/fstab`**:

```sh
$   vi /etc/fstab
# UUID=4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4 /mybackups              xfs     defaults        0 2
UUID=4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4 /mybackups              xfs     ro,noexec        0 2


$   systemctl reboot
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Monitor storage performance✨</ins>

Like CPUs and RAMs, storage devices also have their own limits : 

- due to **size**
- also when it comes to **read / write** operations that can be performed over a period of time

<br/>

#### 🔖 <ins>To monitor CPUs and Memories</ins>

```
$   top
$   htop
```

<br/>

#### 🔖 <ins>To monitor storage</ins>

We have commands that allow us to track read / write operations.

- package **`systat`**

```sh
# I/O statistics
# We can see how many times we've written to a storage device
$   iostat

Linux 3.10.0-1160.el7.x86_64 (localhost.localdomain)    10/15/2023      _x86_64_        (1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.33    0.01    0.50    0.01    0.00   99.15

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda               6.52       219.20        26.13     987381     117708
sdb               0.08         2.40         0.45      10812       2048
...


# Showing stats only for the past 10 seconds
$   iostat 10
```

The `kB_read/s` displays an average value, since server's boot up :

```txt
Average usage / Total uptime
```

<br/>

```sh
# Process ID statistics
# We see per process how many times they read : write to a storage device
$   pidstat -d

Linux 3.10.0-1160.el7.x86_64 (localhost.localdomain)    10/15/2023      _x86_64_        (1 CPU)

07:14:47 PM   UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s  Command
07:14:47 PM     0         1    100.12     14.53      0.33  systemd
07:14:47 PM     0       304      0.04      0.00      0.00  xfsaild/sda3
```

<br/>

### ⚠️ <mark>WARNING</mark> :

Here a way for having <b><span style="color:#06C258">I/O statistics in live (per 1 second)</span></b> :

```sh
$   iostat 1
```
```sh
$   pidstat -d 1
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 📽️ <ins>Manage and configure LVM storage</ins>

LVM stands for **Logical Volume Manager** and it is a great way to give <span style="color:#06C258"><ins>**storage flexibility in Linux**</ins></span>. 

Whether we have free space on different portions of a disk or on multiple disks, we can combine them together en represent them as one regular continuous partition.

```sh
$   dnf install lvm2
```

Before we start, here are some common letters related to LVM :

- **`pv`** : physical volume (represents real storage devices like disk or partition)
- **`vg`** : volume group ()
- **`lv`** : logical volume
- **`pe`** : physical extent

<br/>

In the following example, we have added 3 new disks (**sdb**, **sdd** and **sde**). We will add 2 of them as pv to LVM.

```sh
$   lvmdiskscan

  /dev/sda1 [     300.00 MiB]
  /dev/sda2 [       2.00 GiB]
  /dev/sda3 [     <17.71 GiB]
  /dev/sdb1 [       1.86 GiB]
  /dev/sdb5 [       3.72 GiB]
  /dev/sdb6 [       1.86 GiB]
  /dev/sdc  [       5.00 GiB]
  /dev/sdd  [       5.00 GiB]
  /dev/sde  [       5.00 GiB]
  3 disks
  6 partitions
  0 LVM physical volume whole disks
  0 LVM physical volumes
```

<br/>

#### 🔖 <ins>Physical Volume and Volume Group</ins>

- First thing to do is to pass the **block device files** pointing to these 2 partitions.

```sh
$   pvcreate /dev/sdc /dev/sdd
  Physical volume "/dev/sdc" successfully created.
  Physical volume "/dev/sdd" successfully created.


# To see what pv are attached to LVM
# PFree shows how much this is still unpartitioned and not used by logical volumes
$   pvs
  PV         VG Fmt  Attr PSize PFree
  /dev/sdc      lvm2 ---  5.00g 5.00g
  /dev/sdd      lvm2 ---  5.00g 5.00g
```

- Now LVM has the physical devices where it can store data . So next we tell how to use this storage capacity : we'are going **to add the PVs to a Volume Group (VG)**

```sh
# Creating a VG and adding the 2 disks of 5G
# This is going to be seen as a single continuous 10G disk
$   vgcreate my_volume_1 /dev/sdc /dev/sdd
  Volume group "my_volume_1" successfully created
```

<br/>

- If we want to expand our VG, we would add a new PV to the LVM, <span style="color:#06C258"><ins>**by extending the VG**</ins></span>. 

```sh
# Current Vg size
$   vgs
  VG          #PV #LV #SN Attr   VSize VFree
  my_volume_1   2   0   0 wz--n- 9.99g 9.99g

# Adding the new disk to our VG
$   pvcreate /dev/sde
  Physical volume "/dev/sde" successfully created.

$   vgextend my_volume_1 /dev/sde
  Volume group "my_volume_1" successfully extended


$   sudo vgs
  VG          #PV #LV #SN Attr   VSize   VFree
  my_volume_1   3   0   0 wz--n- <14.99g <14.99g
```

<br/>

- To remove a PV from a VG

```sh
$   vgreduce my_volume_1 /dev/sde
  Removed "/dev/sde" from volume group "my_volume_1"

# If we don't need it anymore, we can remove from LVM entirely  
$   pvremove /dev/sde
  Labels on physical volume "/dev/sde" successfully wiped.
```

<br/>

#### 🔖 <ins>Logical Volume</ins>

The VG is <span style="color:#FF8A8A"><ins>**not partitioned yet**</ins></span> : so we need to add a **Logical Volume**.

```sh
# We create a LV (partition) on our VG
$   lvcreate --size 2G --name partiton_1 my_volume_1
  Logical volume "partiton_1" created.


# VFree was 9.99g, now it is 7.99g  
$   vgs
  VG          #PV #LV #SN Attr   VSize VFree
  my_volume_1   2   1   0 wz--n- 9.99g 7.99g  

```

- To list the LV

```sh
$   lvcreate --size 6G --name partition_2 my_volume_1
  Logical volume "partiton_1" created.


# HERE
$   lvs
  LV          VG          Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  partition_2 my_volume_1 -wi-a----- 6.00g
  partiton_1  my_volume_1 -wi-a----- 2.00g


$   vgs
  VG          #PV #LV #SN Attr   VSize VFree
  my_volume_1   2   2   0 wz--n- 9.99g 1.99g  
```

<br/>

#### 🔖 <ins>Physical Extent</ins>

Imagine we have 2 hard disks of 4 MB and we add them as a 8 MB VG ➡️ we create LV of 8MB on this VG.

Now we write a 8MB file :

- To an application it looks like it writes this file into a file system that exists on a single continuous disk.
- But in reality, <span style="color:#06C258">LVM has split this data into 2 **Physical Extents**</span> : a 4 MB pe will be written on the 1st hard disk and another 4 MB on the 2nd disk.

<br/>

Here we tell LVM to expand the 1st LV to all the extents that are available.

```sh
$   vgs
  VG          #PV #LV #SN Attr   VSize VFree
  my_volume_1   2   2   0 wz--n- 9.99g 1.99g  


$   lvs
  LV          VG          Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  partition_2 my_volume_1 -wi-a----- 6.00g
  partiton_1  my_volume_1 -wi-a----- 2.00g

# =====================================================================================

$   lvresize --extents 100%VG my_volume_1/partiton_1

  Reducing 100%VG to remaining free space 3.99 GiB in VG.
  Size of logical volume my_volume_1/partiton_1 changed from 2.00 GiB (512 extents) to 3.99 GiB (1022 extents).
  Logical volume my_volume_1/partiton_1 successfully resized.

# =====================================================================================

# We can see VFree is now at 0 
$   vgs
  VG          #PV #LV #SN Attr   VSize VFree
  my_volume_1   2   2   0 wz--n- 9.99g    0


$   lvs
  LV          VG          Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  partition_2 my_volume_1 -wi-a----- 6.00g
  partiton_1  my_volume_1 -wi-a----- 3.99g
```

<br/>

We can see it is easy to resize a partition or LV in a LVM compared to a normal partition.

Let's say we want to shrink it again.

```sh
$   lvs
  LV          VG          Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  partition_2 my_volume_1 -wi-a----- 6.00g
  partiton_1  my_volume_1 -wi-a----- 3.99g

# =====================================================================================

$   lvresize --size 2G my_volume_1/partiton_1

  WARNING: Reducing active logical volume to 2.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce my_volume_1/partiton_1? [y/n]: y
  Size of logical volume my_volume_1/partiton_1 changed from 3.99 GiB (1022 extents) to 2.00 GiB (512 extents).
  Logical volume my_volume_1/partiton_1 successfully resized.

# =====================================================================================

$   lvs
  LV          VG          Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  partition_2 my_volume_1 -wi-a----- 6.00g
  partiton_1  my_volume_1 -wi-a----- 2.00g
```

<br/>

To display the details about LV

```sh
$   lvdisplay
  --- Logical volume ---
  LV Path                /dev/my_volume_1/partiton_1
  LV Name                partiton_1
  VG Name                my_volume_1
  LV UUID                wP6NBl-jhgk-5JIO-HX4o-UWMM-uGqB-FPCZxZ
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2023-09-23 22:34:27 +0200
  LV Status              available
  # open                 0
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
...

  --- Logical volume ---
  LV Path                /dev/my_volume_1/partition_2
  LV Name                partition_2
  VG Name                my_volume_1
...
```

<br/>

> An empty LV is like an empty partition.
>
> To be able to store files and directories on a LV, we need to create a file system on it.

```sh
$   mkfs.xfs /dev/my_volume_1/partiton_1

meta-data=/dev/my_volume_1/partiton_1 isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

<br/>

### ⚠️ <mark>WARNING</mark> :

Now we want to resize a LV **with a filesystem on it**. <span style="color:#FF8A8A"><ins>**We should not use**</ins></span> this previous command : The XFS file system we just put on it, would still only use 2 GB.

```sh
# TO NOT USE
$   lvresize --size 3G my_volume_1/partiton_1
```

<br/>

So we can tell LVM to resize both the LV and the file system on it, by **passing another parameter** :

```sh
$   lvresize --resizefs --size 3G my_volume_1/partiton_1

Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
...
```
> One thing to keep in mind : some file systems **can grow in size but they can not shrink**.

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create and configure encrypted storage</ins>

We have 2 modes of encryption :

- `plain` : it just takes our password end encrypts all data with it.
- `luks` (**Linux unified key setup**)

<br/>

```sh
$   lvmdiskscan

  /dev/sda1 [     300.00 MiB]
  /dev/sda2 [       2.00 GiB]
  /dev/sda3 [     <17.71 GiB]
  /dev/sdb1 [       1.86 GiB]
  /dev/sdb5 [       3.72 GiB]
  /dev/sdb6 [       1.86 GiB]
  /dev/sdc  [       5.00 GiB] LVM physical volume
  /dev/sdd  [       5.00 GiB] LVM physical volume
  /dev/sde  [       5.00 GiB]
  1 disk
  6 partitions
  2 LVM physical volume whole disks
  0 LVM physical volumes
```

<br/>

#### 🔖 <ins>Plain</ins>

```sh
# The simplest way we can set up the disk to be encrypted in plain mode
#
# verify-passphrase : tells the utility to ask us for the password twice to make sure we didn't type it wrong.
# open : it tells the utility to open this device for reading encrypted data from it and writing encrypted data to it.
#
# --type plain : option added to 'open'. We tell it to use the encryption scheme called plain.
# my_secure_disk_e : a name we choose for our mapped device. We will find this created device at /dev/mapper/my_secure_disk_e
$   cryptsetup --verify-passphrase open --type plain /dev/sde my_secure_disk_e

Enter passphrase for /dev/sde: 123456a.
Verify passphrase: 123456a.


$   lvmdiskscan
  /dev/sda1                    [     300.00 MiB]
  /dev/sda2                    [       2.00 GiB]
  /dev/mapper/my_secure_disk_e [       5.00 GiB]
  /dev/sda3                    [     <17.71 GiB]
  /dev/sdb1                    [       1.86 GiB]
  /dev/sdb5                    [       3.72 GiB]
  /dev/sdb6                    [       1.86 GiB]
  /dev/sdc                     [       5.00 GiB] LVM physical volume
  /dev/sdd                     [       5.00 GiB] LVM physical volume
  /dev/sde                     [       5.00 GiB]
  2 disks
  6 partitions
  2 LVM physical volume whole disks
  0 LVM physical volumes
```

<br/>

We can create a XFS file system on **`/dev/sde my_secure_disk_e`** and mount it :

```sh
$   mkfs.xfs /dev/mapper/my_secure_disk_e

meta-data=/dev/mapper/my_secure_disk_e isize=512    agcount=4, agsize=327680 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=1310720, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0


$   mount /dev/mapper/my_secure_disk_e  /mnt
```


### ⚠️ <mark>NOTE</mark>

> So **`/dev/mapper/my_secure_disk_e`** is seen as regular unencrypted disk and this makes it easy for applications to write data to it and read data.
>
> **But behind the scenes, Linux does the following :**
> - It takes the data the utility `mkfs` wants to write to **`/dev/mapper/my_secure_disk_e`** disk
> - It encrypts it and then writes it in its encrypted form to **`/dev/sde`** disk
>
> <br/>
>
> - Whenever an application wants to read data from **`/dev/mapper/my_secure_disk_e`** disk, Linux reads the encrypted data from **`/dev/sde`**, decrypts it and sends it to the application.

<br/>

### ⚠️ <mark>WARNING</mark>

> **/dev/mapper/my_secure_disk_e** is a sort of FAKE disk. 
>
> It looks like a regular one with data clearly visible unencrypted. But every read and write happens on **`/dev/sde`** where data is decrypted and encrypted as needed.
>
> But this opens <span style="color:#FF8A8A"><ins>**a potential security hole :**</ins></span>
>
> If the mapping device **`/dev/mapper/my_secure_disk_e`** is opened, **encrypted data** from **`/dev/sde`** **is visible** /dev/mapper/my_secure_disk_e

<br/>

**<ins>1st Security solution : </ins>** Let's unmount the XFS file system and close the map device. With **`/dev/mapper/my_secure_disk_e`** gone, <span style="color:#06C258"><ins>**there is no way to get useful data**</ins></span> from **`/dev/sde`** :

```sh
$   umount /mnt
$   cryptsetup close my_secure_disk_e
```

If the mapping device is closed, the data is safe.

<br/>

#### 🔖 <ins>Luks</ins>

Luks encryption is much easier to use.

- First we need to format the disk or partition we intend to use with luks encryption. 
- <span style="color:#06C258">We don't need to specify the type of encryption.</span> 

When we format a device with luks, that writes a little data to the storage device called a **<span style="color:#FF8A8A">header</span>**. By reading this header, the crypsetup utility can automatically detect the encryption type.

```sh
$   cryptsetup luksFormat /dev/sde

WARNING!
========
This will overwrite data on /dev/sde irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase for /dev/sde:
Verify passphrase:

```

<br/>

To change the password (called the **encryption key**) :

```sh
# Contrary to plain mode, we don't need to re-encrypt all the data on the device.
$   sudo cryptsetup luksChangeKey /dev/sde

Enter passphrase to be changed:
Enter new passphrase:
Verify passphrase:
```

Once the device is formatted with `**luks**`, it can be openend with a command like this :

```sh
$   cryptsetup open /dev/sde my_secure_luks_e
Enter passphrase for /dev/sde:
```

<br/>

Creating a file system on it and closing the mapping device are the same as before.

```sh
$   mkfs.xfs /dev/mapper/my_secure_luks_e
$   cryptsetup close my_secure_luks_e
```

<br/>

#### 🔖 <ins>Encrypting partially a disk</ins>

We have 2 partitions on /dev/sde : sde1 and sde2. We want to encrypt sde2.

```sh
$   cryptsetup luksFormat /dev/sde2
$   cryptsetup open /dev/sde2 my_secure_luks_e_partition_2
```
```sh
$   cryptsetup --verify-passphrase open --type plain /dev/sde2 my_secure_disk_e
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create and manage RAID devices</ins>

#### 🔖 <ins>Commands</ins>

- First we need to wipe out the LVM.

```sh
# First we remove the volume group
$   vgremove --force my_volume_1

  Logical volume "partiton_1" successfully removed
  Logical volume "partition_2" successfully removed
  Volume group "my_volume_1" successfully removed


# Then the physical volumes  
$   pvremove /dev/sdc /dev/sdd /dev/sde
  No PV found on device /dev/sde.
  Labels on physical volume "/dev/sdc" successfully wiped.
  Labels on physical volume "/dev/sdd" successfully wiped.
```

<br/>

- To create arrays, we will use the utility called **`mdadm`** (Multiple Devices Administration)

```sh
# /dev/md0  : block device name assigned to this array
$   mdadm --create /dev/md0 --level=0 --raid-devices=3 /dev/sdc /dev/sdd /dev/sde
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

- We can now threat our array as a regular disk

> In case a 2nd array is added to the system, it should be assigned to /dev/md1, the 3rd to /dev/md2 and so on ...

```sh
# We could create a file system on it
$   mkfs.ext4 /dev/md0

mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=384 blocks
983040 inodes, 3928320 blocks
196416 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2151677952
...
```

<br/>

- To stop or deactivate the array

```sh
$   mdadm --stop /dev/md0
mdadm: stopped /dev/md0
```

### ⚠️ <mark>NOTE</mark>

When Linux boots up, it will scan what is called the super block of all devices : it will check which devices belong to a raid array. When they do, Linux will reassemble them into an array and they'll be found in **`/dev/md`** 1 to 7.

If we want to avoid that, we can wipe the data on these super blocks.

```sh
$   mdadm --zero-superblock /dev/sdc /dev/sdd /dev/sde
```

<br/>

### ⚠️ <mark>NOTE</mark>

Here is the command of how to add spare disk to an array

```sh
# If sdc fails, the OS will detect sde and add it to the mirrored array
$   mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd --spare-devices=1 /dev/sde

mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? yes
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

<br/>

### ⚠️ <mark>NOTE</mark>

We mount 2 disks at Level 1 and we want to increase the level of safety

```sh
# We stop the array and delete the super block
$   mdadm --stop /dev/md0
$   mdadm --zero-superblock /dev/sdc /dev/sdd /dev/sde

# We mount our level 1 with 2 disks
$   mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd


# FINALLY we add the sde disk
$   mdadm --manage /dev/md0 --add /dev/sde
mdadm: added /dev/sde


# TO REMOVE
$   mdadm --manage /dev/md0 --remove /dev/sde
mdadm: added /dev/sde
```

To look at the status the arrays of our system

```sh
$   cat /proc/mdstat
Personalities : [raid0] [raid1]
md0 : active raid1 sde[2](S) sdd[1] sdc[0]
      5237760 blocks super 1.2 [2/2] [UU]

unused devices: <none>
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Create, manage and diagnose advanced file system permissions</ins>

We have set permissions for the owner (`bob`) and the group (`ftp`): 

```sh
$  ls -l /opt/databases/

total 0
-rw-rw-r--. 1 bob ftp 0 Oct  1 07:43 file1
-rw-rw-r--. 1 bob ftp 0 Oct  1 07:43 file2
-rw-rw-r--. 1 bob ftp 0 Oct  1 07:43 file3
```

<br/>

#### 🔖 <ins>Specific permissions</ins>

Now we want to make exceptions for **<span style="color:#06C258">1 or 2 users (John and Jane)</span>** for writing : we have to use the **`setfacl` (Set File Access Control List)** utility.

```sh
$   setfacl --modify user:john:rw file3
# $   sudo setfacl --modify group:ftp:rw file3

# See the '+' mark on file3. It signals that ACL exists
$   ls -l /opt/databases/

total 0
-rw-rw-r--. 1 bob ftp 0 Oct  1 07:43 file1
-rw-rw-r--. 1 bob ftp 0 Oct  1 07:43 file2
-rw-rw-r--+ 1 bob ftp 0 Oct  1 07:43 file3
```

<br/>

To deny permissions to a specific user :

```sh
$   setfacl --modify user:john:--- file3
```

<br/>

To remove from ACL :

```sh
$   setfacl --remove user:john file3
```

<br/>

#### 🔖 <ins>Modifiy attribute</ins>

With the **`chattr`** utility, we can for example, make a file only accepting new content by appending them only :

```sh
# +a for append ONLY
$   chattr +a newfile 

# -a for removing the attribute from a file
$   chattr -a newfile
```
```sh
# +i to make a file immutable
$   chattr +i newfile 
```

<br/>

To display attributes :

```sh
$   lsattr newfile
----i----------- newfile
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Setup user and group disk quotas for filesystems</ins>

```sh
$   dnf install quota -y
```

- We choose which file systems we want to enforce quotas by editing our file

```sh
$   blkid /dev/sdb5
/dev/sdb5: LABEL="BackupVolume" UUID="4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4" TYPE="xfs"


$   vi /etc/fstab
# UUID=4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4 /mybackups              xfs     defaults        0 2
UUID=4c539fb2-e7c0-47c8-8464-ac1dc0b9bda4 /mybackups              xfs     defaults,usrquota,grpquota        0 2
```

- We save the file and reboot the machine. 

```sh
$   systemctl reboot
```

<br/>

Now the system will track how much space each user and group is using and enforce the limits we set up we will se later.


- Let's setup our environment test

```sh
[root@localhost ~]# sudo mkdir /mybackups/cento/
[root@localhost ~]# sudo chown cento:cento /mybackups/cento
[cento@localhost ~]# fallocate --length 100M /mybackups/cento/100Mfile
```

- We edit quotas for the user cento

```sh
$   edquota --user cento
Disk quotas for user cento (uid 1000):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb5                    102400       150M       200M          2        0        0
```

- Let's create a file of 60 M alongside with our 100M file.

  - The user is using 160M which exceeds 150M soft limit
  - A grace period of x days is allowed to the user for exeeding the soft limit : after this period, the user won't be able to write any more data.

- If we try to create 40 M alongside with our 160M file.

  - The hard limit is exceeded and the operation is aborted.
```sh
[cento@localhost ~]# fallocate --length 60M /mybackups/cento/60Mfile


# The * in block column tells us the quto has been exceeded the soft limit, so there is a grace period of 6 days.
$   quota --user cento
Disk quotas for user cento (uid 1000):
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
      /dev/sdb5  163840* 153600  204800   6days       3       0       0


$   fallocate --length 40M /mybackups/cento/40Mfile
fallocate: fallocate failed: Disk quota exceeded      
```

- We can set a limit on how many files and directories a user can create : Each directory or file uses an inode. So let's edit soft and hard limit for this.

```sh
# If we try to create a 5th file it will not work
$   edquota --user cento

Disk quotas for user cento (uid 1000):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb5                    163840     153600     204800          4        0        5


$   touch /mybackups/cento/TOTOfile2
touch: cannot touch ‘/mybackups/cento/TOTOfile2’: Disk quota exceeded
```

&nbsp;

###  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <ins>Build and install from source</ins>

> Install the text based terminal browser links2 from source on server app-srv1 . The source is provided at **/tools/links-2.14.tar.bz2** on
that server.
Configure the installation process so that:
>
> 1. The target location of the installed binary will be /usr/bin/links
> 2. Support for ipv6 will be disabled

```sh
$   cd /tools
$   tar xjf links-2.14.tar.bz2
$   cd links-2.14
```

<br/>

### ⚠️ <mark>NOTE</mark>

The usual process of installing from source is :

1. **`./configure`** (args...)
2. **`make`**
3. **`make install`**