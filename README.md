# Linux

# table of contents
1. user management
2. Permissions
3. Commands

## Course Overview
Basisc admin
installing and configuring
setting up Account
setting  up permissions
unix commands - a comprehensive prime

basic of shell scripting
overview 
editors - vim and notepad++
shell expansion
quotes
braces braces and parentheses
a starter shell script
input and output processing

### account management
1. Types of user in linux
 - root user : the highest level of user in linux
 - admin user : A user with sudo privileges to execute commands as root
 - Regular user : A user with limited access to the system

2. command
 - sudo : Run a command as root without switching to the root user
 - su : Switch to the root user
 - su - : Switch to the root user and change to the directory to /root

### Permissions

### Linux Commands
Types of commands
 - Internal commands (built-in)
 - External shell commands
 - Aliases commands
 - Functions commands
 - Keyword commands

### shell expansion

The diffent types of shell expansion
 - brace expansion
 - tilde expansion
 - parameter and variable expansion
 - command substitution
 - arithmetic expansion
 - process substitution
 - word splitting
 - filename expansion
 - quote removal

### basic shell scripting
1. shebang
 The first line of a shell script that tells the system which shell to use to execute the script. It could be `#!/bin/bash` or `#!/bin/sh`.
    `#!/bin/bash` is used to execute the script using the bash shell.
    `#!/bin/sh` is used to execute the script using the sh shell.
2. Variables
- Variables are used to store data that can be used later in the script.
- Variables in Bash are case-sensitive.
  - System variables: Upper case, e.g., $HOME, $PATH
  - User-defined variables: Lower case, e.g., $myvar, $my_var
* note: Variables are assigned without spaces between the variable name, the equal sign, and the value.
- Special variables:
```bash
$0 - The name of the script
$1-$9 - The first 9 arguments to the script
$# - The number of arguments to the script
$@ - All the arguments
$? - The exit status of the last command
$$ - The process ID of the current script
```
- The difference between single and double quotes
  - Single quotes: The text is treated as a literal string.
  - Double quotes: The text is treated as a string with variables expanded.

example:
```bash
var1="Hello"
var2="$var1 World"

echo $var2 -> Hello World
```
- If quoting variables include wildcards expansion `*` Bash will expand it to the list of files in the current directory.
example:
```bash
var1="*"
echo ${var1} -> list of files in the current directory
```
- To prevent the expansion of wildcards, use double quotes.
example:
```bash
var1="*"
echo "${var1}" -> *
```
- Using `{}` to group variables
example:
```bash
var1="Hello"
var2="World"
echo "${var1} ${var2}" -> Hello World
``` 
3. 


