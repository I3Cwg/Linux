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
3. command line parameters
4. piping commands
5. child scripts
6. variables scope
7. exit codes
8. comments

### Braces, Brackets, and Parentheses
- subshell `( )` : A subshell is a child process that runs in a separate shell environment. It is not affected by the parent shell.
example:
```bash
var="Hello"
echo "Var outside: $var"  # Hello

( var="World"; echo "Var in subshell: $var" )  # Chạy trong subshell

echo "Var outside: $var"  # Vẫn là "Hello"
```

- Double parentheses `(( ))` : Double parentheses are used for arithmetic operations. No return value is required but can be used to return exit codes values 0 for true and 1 for false.
example:
```bash
i=1
(( i+=9 ))   # Tăng i lên 9
echo $i      # 10

(( i=3 ))  
echo $?      # 0 (thành công)

(( i=0 ))  
echo $?      # 1 (thất bại, vì giá trị bằng 0)
```
- Dollar parentheses `$( )` : Used to execute in a subshell and return the output of the command.
example:
```bash
echo "path: $(pwd)"
/home/user/scripts

var=$(inner_var=10; echo $inner_var)
echo $var
10
```
- Dolar double parentheses `$(( ))` : Used for arithmetic operations and return the result of the operation.
example:
```bash
i=1
echo $(( i+=9 ))   # 10
```
- Single brackets `[ ]` : Used for conditional expressions and pattern matching. The return value is 0 for true and 1 for false.
example:
```bash
[ -d /etc ] && echo "Directory exists" || echo "Directory does not exist"

a=10
b=20
[ $a -eq $b ] && echo "Equal" || echo "Not equal"
```
- Double brackets `[[ ]]` : Used for **advanced** conditional expressions and pattern matching. The return value is 0 for true and 1 for false. It is recommended to use double brackets over single brackets.
example:
```bash
var = "hello"
[$var = $var ]&& echo "true" # Error : too many arguments
[[ $var = $var ]] && echo "true" # true
[["hello"=H*]] && echo "true" # support wildcard  
```
- Brace expansion `{ }` : Used to generate strings or numbers in a sequence.
example:
```bash
echo {1..10} # 1 2 3 4 5 6 7 8 9 10
echo {a..z} # a b c d e f g h i j k l m n o p q r s t u v w x y z
echo {a..z..2} # a c e g i k m o q s u w y
```
- Dollar braces `${ }` : Used to expand variables and perform operations on variables.
example:
```bash
var="Hello"
echo "${var} World" # Hello World
```

### File Descriptors

### Shell Scripting
1. Interations 

Looping in shell scripting
 - for loop
``` shell
for var in (<numerical array>/<string array>)
do 
  <process>
done
```
or
``` shell
for ((initialization; condition; step))
do
  <process>
done
```
 - while loop
``` shell
while [condition]
do
  command
done
  ```
while loop are commonly used to read a file line by line.
``` shell
cat ${filename} | while read -r line
  do
    echo $line
  done
```
 - until loop
``` shell
until [condition]
do 
  <list of commands>
done
```
***Note***: The until loop is the opposite of the while loop. The until loop executes the commands until the condition is true.

2. Conditional Statements
 - if statement
``` shell
if [[condition]]
then 
  <list of commands>
elif [[condition]]
then
  <list of commands>
else
  <list of commands>
fi
```
 - case statement : is used instead of the IF statement when there are multiple branches depend on the value of a variable. The systax is:
``` shell
case <expression> in
  pattern1)
    <list of commands>
    ;;
  pattern2)
    <list of commands>
    ;;
  *)
    <list of commands>
    ;;
esac
```
3. Regular Expressions

4. Arrays
Bash supports both Indexed and Associative arrays. Indexed arrays are arrays that use numbers to index the elements. Associative arrays are arrays that use strings or numbers as indexes.
 - Indexed arrays are declared as follows:
``` shell
declare -a arr=(1 2 3 4 5)
or 
declare -a arr
arr=(1 2 3 4 5)
or
arr()
{
  arr[0]=1
  arr[1]=2
  arr[2]=3
  arr[3]=4
  arr[4]=5
}
```
 - Associative arrays are declared as follows:
``` shell
declare -A arr
arr=([key1]=value1 [key2]=value2)
or
arr()
{
  arr[key1]=value1
  arr[key2]=value2
}
```
* Array can be looped over using either: `${arr[@]} or ${arr[*]}`
* In case of string elements with spaces the arrays expansion should be enclosed in double quotes: `"${arr[@]}" or "${arr[*]}"`
* The entire array can be accessed using: `“${arr[@]} “`

* To access an indexed element: `${arr[0]}`

* To set a particular element: `arr[0]=”Value”`

* To unset a particular element: `unset arr[0]`

* To access all the indexes: `${!arr[@]}`

* To append a value to the array without using an index: `arr+=( new_val )`

* To find the length of an array: `${#arr[@]}`

* To get a subset of an array: `${arr[@]:<start_index>:<end_index>}`

### File processing
1. AWK
- awk is a powerful programming language that is used for processing text files.
- awk is used to search for a pattern in a file and perform an action when the pattern is found.
- The basic syntax of an awk command is:
``` shell
awk 'pattern { action }' filename
```
2. SED
3. GREP

### Cron Jobs
- The cron utility is used to schedule commands to run at a specific time.
- The common crontab commands are:
``` shell
crontab -e : Edit the crontab file
crontab -l : List the crontab for the current user
crontab -r : Remove the crontab for the current user
crontab -v : Display the last time the crontab was edited
```
- A started template for a cron job is:
``` shell
* * * * * command_to_execute
- - - - -
| | | | |
| | | | +---- Day of week (0 - Sunday, 6 - Thursday)
| | | +------ Month (1 - 12)
| | +-------- Day of month (1 - 31)
| +---------- Hour (0 - 23)
+------------ Minutes (0 - 59)
```
example:
``` shell
0 12 * * * /bin/echo "Hello World" # Run at 12:00 PM every day
```

### Processing input and output


