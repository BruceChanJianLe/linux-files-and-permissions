# Linux Files and Permissions

This is a summary of the Linux's files and permissions.

## File

Type | ll
--- | ---
Directory | drwxrwxr-x  3 chanjl chanjl  4096 Jun  5 15:37 linux-files-and-permissions
Regular File | -rw-rw-r--  1 chanjl chanjl  4366 May  1 19:04 .tmux.conf
Symbolic Link | lrwxrwxrwx  1 root root       4 Apr 15 19:04 rnano -> nano
Block Device File (Harddrives and USBs) | brw-rw---- 1 root disk 8, 0 Jun  5 13:44 ../sda
Socket File (Facilitates communication between applications) | srw-rw-rw-. 1 root root       0 Feb 2 18:58 log
Named Pipe File (FIFO) | prw-------. root root 0 Feb 2 18:58 /run/dmeventd-client

![img](summary-file.png)

### Metadata of a File
Data that describes other data are called metadata.
- File name  
- File size  
- Permissions  
- Ownership  
- Last access time  

ls -l | Explanation
--- | ---
`-`rw-r--r--. 1 root root 2315 Sep 12 14:39 /etc/passwd | First column explains the type of file
-`rw-`r--r--. 1 `root` root 2315 Sep 12 14:39 /etc/passwd | Second to fourth columns is the user owner permissions (here is root)
-rw-`r--`r--. 1 root `root` 2315 Sep 12 14:39 /etc/passwd | Fifth to seventh columns is the group owner permissions (here is root group)
-rw-r--`r--`. 1 root root 2315 Sep 12 14:39 /etc/passwd | Eighth to tenth columns is the other permision (here is others only have read access)
-rw-r--r--`.` 1 root root 2315 Sep 12 14:39 /etc/passwd | This is the Index Filesystem nodes or Inodes. An Inode stores the metadata of the file (most regular files only have one Inode)
-rw-r--r--. 1 `root` root 2315 Sep 12 14:39 /etc/passwd | User owner, one file can only have one user owner
-rw-r--r--. 1 root `root` 2315 Sep 12 14:39 /etc/passwd | Group owner, one file can only have one group owner
-rw-r--r--. 1 root root `2315` Sep 12 14:39 /etc/passwd | File size in bytes
-rw-r--r--. 1 root root 2315 Sep 12 14:39 `/etc/passwd` | The name of the file

Note that every file is owned by one user owner and one group owner.  
There multiple ways to display the metadata of a file.  
**Method 1**
```bash
file /etc/passwd
```
**Method 2**
```bash
stat /etc/passwd
```

### Extended Attribute Types
1. Extended security attributes
2. Extended system attributes
3. Extended user attributes

**Extended security attributes:**
SElinux security context
- A mandatory access control (MAC) system
- Layered over the Linux discretionary access control (DAC) system
**Method 1**  
```bash
ls -Z /etc/passwd
```

**Extended system attributes**
Access control lists
- Additional layer of discretionary permissions
- Permissions for multiple users on a file
- Permissions for multiple groups on a file
- Inheritance for user/group permissions
- Backing up and restoring permissions
**Method 1**
```bash
# Create a text file
touch aclfile.txt
# See the permissions
ls -l aclfile.txt
# Set acl attribute and allow root to have rwx rights on text file
setfacl -m user:root:rwx aclfile.txt
# View acl attribute on text file
getfacl -t aclfile.txt
```

**Extended user attributes**
Extended attributes
- Append only (Can add data to the file but not overwrite the original)
- Auto compress files (Set this attribute to compress and decompress automatically)
- Make files immutable (If the immutable flag is set means cannot be modified, deleted, renamed, no data can be written to the file)
- Auto backup on single files (If the backup flag is set means when it is deleted, it can be undelete because it is backup, which is not default by Linux)
**Method 1**
```bash
# Create a text file
touch afile.txt
# Change attribute for text file(need to elevate privilages to set the attribute)
# +i for immutable flag
sudo chattr +i afile.txt
# List attribute for text file
lsattr afile.txt
```

### File Globbing

File Glob | Explanation | Examples
--- | --- | ---
Asterisk * | Matches with any number of any characters | `file.*` --> file.txt, file.jpg, file.tar.gz
Question Mark ? | Matches with any one of the characters | `??.txt` --> 12.txt, 34.txt; `???.jpg` -- > hal.jpg, 123.jpg
Character Sets [] | Matches one character in the list | `file[0-9].txt` --> file1.txt, file8.txt; `file[abc123].jpg` --> filea.jpg, file3.jpg
Hyphen - | Matches a hyphen | `file[-0-9].txt` --> file-.txt, file1.txt
Negates ! | Negates a match | `file[!0-9].txt` --> filea.txt, fileb.txt but not file1.txt
Digit [:digit:] | Matches numbers | `file[[:digit:]].txt` --> file1002432.txt 
Uppercase [:upper:] | Matches uppercase characters | `file[[:upper:]]` --> fileXX.txt
Lowercase [:lower:] | Matches lowercase characters | `[:lower:]`
Alphabets (upper or lower) [:alpha:] | Matches upper and lower case characters | `[:alpha:]`
Alphabets and Numbers [:alnum:] | Matches upper, lower case characters and numbers | `[:alnum:]`
Spaces Tabs Newlines [:space:] | Matches spaces, tabs, newlines | `[:space:]`
Graph [:graph:] | Matches printable characters not including spaces | `[:graph:]`
Print [:print:] | Matches printable characters including spaces | `[:print:]`
Punctuation [:punct:] | Matches punctuation | `[:punct:]`
Non-printable [:cntrl:] | Matches non-printable control characters | `[:cntrl:]`
Hexadecimal Characters [:xdigit:] | Matches hexadecimal characters | `[:xdigit:]`

**Method 1**  
```bash
ls file[0-9].txt
```
**Method 2**  
```bash
ls file[[:digit:]].txt
```
**Method 3**  
```bash
ls file[[:xdigit:][:space:]].txt
```
**Method 4**
```bash
ls file[![:digit:]].txt
```

### File Globbing: Brace Expansion  
```bash
# Command
ls {*.jpg, *.gif, *png}
# Result
a.jpg
b.jpg
c.gif
d.png
```

### File Globbing: Extended Glob
```bash
# To check if Extended Glob is turn on
# Shell options, and look for extglob
shopt | grep extglob
# To turn on Extended Glob
shopt -s extglob
```

**Advantages**  
- Specify the number of matches  
- Allow grouping matches  
- Patterns can be more than one character  
- Logical OR

**Method 1**  
`?(match)` | 0 or 1 occurrence of pattern match  
```bash
# Example command
ls | grep file?(abc).txt
# Result
file.txt
fileabc.txt
```
**Method 2**  
`+(match)` | 1 or more occurrences of pattern match  
```bash
# Example command
ls file+(abc).txt
# Result
fileabc.txt
fileabcabc.txt
```
**Method 3**  
`+(match1|match2)` | match one or the other pattern  
```bash
# Example command
ls +(*.jpg|*gif)
# Result
photo.jpg
file.jpg
photo.gif
file.gif
```
**Method 4**  
`*(match)` | 0 or more occurrences of pattern match
```bash
# Example command
ls photo*(abc).jpg
# Result
photo.jpg
photoabc.jpg
photoabcabc.jpg
```
**Method 5**  
`!(match)` | invert the pattern match  
```bash
# Example command
ls !(*.jpg|*.gif)
# Result
file.txt
fileabc.txt
fileabcabc.txt
```
**Method 6**  
`!(+(match)*+(match))` | group pattern matches  
```bash
# Example command
ls !(+(photo|file)*+(.jpg|.gif))
# Result
All files that do not start with photo or file and do not end with .jpg or .gif
```

### Creating Links
**Hard Link**
Linking file with hard link mean these files will the exactly the same. They behave linke smartpointers, only when all pointers are destroyed are then the memory is freed. You can verify by the Inode which for regular file should be 1.
```bash
# Example
touch file.txt
# Hard link file.txt
ln file.txt filelink.txt
# View inode
ll
# Result
-rw-rw-r--  2 chanjl chanjl     0 Jun  6 15:06 filelink.txt
-rw-rw-r--  2 chanjl chanjl     0 Jun  6 15:06 file.txt
# Another way to view
stat file.txt
# Result
  File: 'file.txt'
  Size: 0               Blocks: 0          IO Block: 4096   regular empty file
Device: 10305h/66309d   Inode: 5512893     Links: 2
Access: (0664/-rw-rw-r--)  Uid: ( 1000/  chanjl)   Gid: ( 1000/  chanjl)
Access: 2020-06-06 15:06:29.312064233 +0800
Modify: 2020-06-06 15:06:29.312064233 +0800
Change: 2020-06-06 15:06:39.236064439 +0800
 Birth: -
```
**Disadvantages**  
- Cannot link to directories  
- Cannot link across filesystems  
- Hard to identify  
**Advantages**  
- Take up virtually no space  
- Do not break when target is deleted  

**Symbolic Link**
Symbolic linke is a file that points to another file.  
```bash
# Example
touch file.txt
# Symbolick link file.txt
ln -s file.txt filesymlink.txt
# View metadata of the file.txt
ll
# Result
lrwxrwxrwx  1 chanjl chanjl     8 Jun  6 15:15 filesymlink.txt -> file.txt
# Another way to view
stat filesymlink.txt
# Result
  File: 'filesymlink.txt' -> 'file.txt'
  Size: 8               Blocks: 0          IO Block: 4096   symbolic link
Device: 10305h/66309d   Inode: 5512991     Links: 1
Access: (0777/lrwxrwxrwx)  Uid: ( 1000/  chanjl)   Gid: ( 1000/  chanjl)
Access: 2020-06-06 15:15:25.936075388 +0800
Modify: 2020-06-06 15:15:25.932075388 +0800
Change: 2020-06-06 15:15:25.932075388 +0800
 Birth: -
```
**Disadvantages**  
- Take up a small amount of space  
- Break if the target is deleted  
**Advantages**
- Can link across filesystems  
- Can link to directories  
- Easy to identify (ls -l)  

### Standard Linux Permissions Overview
**Features of Standard Linux Permissions**  
- Users can belong to multiple groups (group cannot contain other group)  
- Files belong to one user owner  
- Files belong to one group owner  
- Permissions can be set for the user, group, or other  

**Features of Standard Linux**  
- Users can read, write, or execute files  
- Users can list, create new files and traverse directories  
- Permissions support privilege escalation  
- Permissions support group owner inheritance  
- They support default file permissions  

**Shortfalls of Standard Linux Permissions**  
- Files and directories can only belong to one user  
- Files and directories can only belong to one group  
- Permissions set for other are not concise  
- Inheritance only support group ownership and not permissions  
- There is no easy way to back up and restore permissions  
- There is no easy way to temporarily restrict permissions  

### File Ownership
The command to change the ownership of a file is `chown`.  
`chown [options] <user>:<group> <file>`
**Method 1**  
```bash
# Example
sudo chown bob file.txt
# Explanation
Setting user ownership to bob
```
**Method 2**  
```bash
# Example
sudo chown :bobgroup file.txt
# Explanation
Setting group ownership to bobgroup
```
**Method 3**  
```bash
# Example
sudo chown -r bob:bobgroup /home/bob
# Explanation
Setting every file inside /home/bob directory to bob:bobgroup ownership
Note that user and group need to exist before you can set them
# To add user
sudo useradd testuser
# To view user
cat /etc/passwd
# To add group
sudo groupadd testgroup
# To view group
cat /etc/group
```

### File Permissions in Numeric Mode
The command to set the permission of a file is `chmod`.  
`chmod [options] <permissions> <filename>`  

Command | Equals | Value
--- | --- | ---
Read | = | 4
Write | = | 2
Execute | = | 1

Therefore, `rwx` means `4 + 2 + 1 = 7`  

**Method 1**  
```bash
# First create a file
touch file.txt
# View permission
ls -l
# Result
-rw-rw-r-- 1 chanjl chanjl     0 Jun  6 15:45 file.txt
# Lets change to permission
chmod 750 file.txt
# View permission
ls -l
# Result
-rwxr-x--- 1 chanjl chanjl     0 Jun  6 15:45 file.txt
```