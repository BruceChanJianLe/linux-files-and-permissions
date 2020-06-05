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
Digit [:digit:] | Matches numbers | `[:digit:]`
Uppercase [:upper:] | Matches uppercase characters | `[:upper:]`
Lowercase [:lower:] | Matches lowercase characters | `[:lower:]`
Alphabets (upper or lower) [:alpha:] | Matches upper and lower case characters | `[:alpha:]`
Alphabets and Numbers [:alnum:] | Matches upper, lower case characters and numbers | `[:alnum:]`
Spaces Tabs Newlines [:space:] | Matches spaces, tabs, newlines | `[:space:`
Character Classes [:xxx:] | Matches one character of a certain type | `[:digit:]` --> Numbers; `[:upper:]` --> Uppercase characters; `[:lower:]` --> Lowercase characters; `[:alpha:]` --> Uppercase and lowercase; `[:alnum:]` --> Uppercase and lowercase plus numbers; `[:space:]` --> Spaces, tabs, newlines

