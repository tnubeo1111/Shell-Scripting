Shell Scripting Basics

IN THIS PART

Chapter 11
Basic Script Building
Chapter 12
Using Structured Commands
Chapter 13
More Structured Commands
Chapter 14
Handling User Input
Chapter 15
Presenting Data
Chapter 16
Script Control

# CHAPTER 11 BASIC SCRIPT BUILDING

IN THIS CHAPTER

Using multiple commands
Creating a script fi le
Displaying messages
Using variables
Redirecting input and output
Pipes
Performing math
Exiting the script

Now that we’ve covered the basics of the Linux system and the command line, it’s time to
start coding. This chapter discusses the basics of writing shell scripts. You need to know
these basic concepts before you can start writing your own shell script masterpieces.

## Using Multiple Commands

So far you’ve seen how to use the command line interface (CLI) prompt of the shell to enter commands and view the command results. The key to shell scripts is the ability to enter multiple
commands and process the results from each command, even possibly passing the results of one
command to another. The shell allows you to chain commands together into a single step.

If you want to run two commands together, you can enter them on the same prompt line, separated
with a semicolon:

$ date ; who
Mon Feb 21 15:36:09 EST 2014
Christine   tty2    2014-02-21 15:26
Samantha    tty3    2014-02-21 15:26
Timothy     tty1    2014-02-21 15:26
user        tty7    2014-02-19 14:03 (:0)

Congratulations, you just wrote a shell script! This simple script uses just two bash shell
commands. The date command runs fi rst, displaying the current date and time, followed
by the output of the who command, showing who is currently logged on to the system.
Using this technique, you can string together as many commands as you wish, up to the
maximum command line character count of 255 characters.

Using this technique is fi ne for small scripts, but it has a major drawback: You must enter
the entire command at the command prompt every time you want to run it. Instead of having to manually enter the commands onto a command line, you can combine the commands
into a simple text fi le. When you need to run the commands, just simply run the text fi le.

## Creating a Script File

To place shell commands in a text fi le, fi rst you need to use a text editor (see Chapter 10) to
create a fi le and then enter the commands into the fi le.

When creating a shell script fi le, you must specify the shell you are using in the fi rst line of
the fi le. Here’s the format for this:

    #!/bin/bash
    
In a normal shell script line, the pound sign (#) is used as a comment line. A comment line
in a shell script isn’t processed by the shell. However, the fi rst line of a shell script fi le is
a special case, and the pound sign followed by the exclamation point tells the shell what
shell to run the script under (yes, you can be using a bash shell and run your script using
another shell).

After indicating the shell, commands are entered onto each line of the fi le, followed by a
carriage return. As mentioned, comments can be added by using the pound sign. An example looks like this:

#!/bin/bash
# This script displays the date and who's logged on
date
who

And that’s all there is to it. You can use the semicolon and put both commands on the same
line if you want to, but in a shell script, you can list commands on separate lines. The shell
processes commands in the order in which they appear in the fi le.

Also notice that another line was included that starts with the pound symbol and adds
a comment. Lines that start with the pound symbol (other than the fi rst #! line) aren’t

interpreted by the shell. This is a great way to leave comments for yourself about what’s
happening in the script, so when you come back to it two years later, you can easily remember what you did.

Save this script in a fi le called test1, and you are almost ready. You need to do a couple of
things before you can run your new shell script fi le.

If you try running the fi le now, you’ll be somewhat disappointed to see this:

$ test1
bash: test1: command not found
$

The fi rst hurdle to jump is getting the bash shell to fi nd your script fi le. If you remember
from Chapter 6, the shell uses an environment variable called PATH to fi nd commands. A
quick look at the PATH environment variable demonstrates our problem:

$ echo $PATH
/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/bin:/usr/bin
:/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/user/bin $

The PATH environment variable is set to look for commands only in a handful of directories. To get the shell to fi nd the test1 script, we need to do one of two things:
 ■ Add the directory where our shell script fi le is located to the PATH environment
variable.
 ■ Use an absolute or relative fi le path to reference our shell script fi le in the prompt.

--> TIP
Some Linux distributions add the $HOME/bin directory to the PATH environment variable. This creates a place in
every user’s HOME directory to place fi les where the shell can fi nd them to execute.

For this example, we use the second method to tell the shell exactly where the script fi le is
located. Remember that to reference a fi le in the current directory, you can use the single
dot operator in the shell:

$ ./test1
bash: ./test1: Permission denied
$

The shell found the shell script fi le just fi ne, but there’s another problem. The shell 
indi-cated that you don’t have permission to execute the fi le. A quick look at the fi le 
permis-sions should show what’s going on here: 

$ ls -l test1
-rw-rw-r--      1 user user       73 Sep 24 19:56 test1
$

When the new test1 fi le was created, the umask value determined the default permis-sion 
settings for the new fi le. Because the umask variable is set to 002 (see Chapter 7) in
Ubuntu, the system created the fi le with only read/write permissions for the fi le’s owner
and group.

The next step is to give the fi le owner permission to execute the fi le, using the chmod 
com-mand (see Chapter 7):

$ chmod u+x test1
$ ./test1
Mon Feb 21 15:38:19 EST 2014
Christine     tty2      2014-02-21 15:26
Samantha      tty3      2014-02-21 15:26
Timothy       tty1      2014-02-21 15:26
user          tty7      2014-02-19 14:03 (:0)
user          pts/0     2014-02-21 15:21 (:0.0) $

Success! Now all the pieces are in the right places to execute the new shell script fi le

## Displaying Messages

Most shell commands produce their own output, which is displayed on the console
moni-tor where the script is running. Many times, however, you will want to add your own text
messages to help the script user know what is happening within the script. You can do this
with the echo command. The echo command can display a simple text string if you add
the string following the command:

$ echo This is a test
This is a test
$

Notice that by default you don’t need to use quotes to delineate the string you’re displaying. 
However, sometimes this can get tricky if you are using quotes within your string:

$ echo Let's see if this'll work
Lets see if thisll work
$

The echo command uses either double or single quotes to delineate text strings. If you use
them within your string, you need to use one type of quote within the text and the other
type to delineate the string:

$ echo "This is a test to see if you're paying attention"
This is a test to see if you're paying attention
$ echo 'Rich says "scripting is easy".'
Rich says "scripting is easy".
$

Now all the quotation marks appear properly in the output.

You can add echo statements anywhere in your shell scripts where you need to display
additional information:

$ cat test1
#!/bin/bash
# This script displays the date and who's logged on
echo The time and date are:
date
echo "Let's see who's logged into the system:"
who
$

When you run this script, it produces the following output:

$ ./test1
The time and date are:
Mon Feb 21 15:41:13 EST 2014
Let's see who's logged into the system:
Christine     tty2      2014-02-21 15:26
Samantha      tty3      2014-02-21 15:26
Timothy       tty1      2014-02-21 15:26
user          tty7      2014-02-19 14:03 (:0)
user          pts/0     2014-02-21 15:21 (:0.0)
$

That’s nice, but what if you want to echo a text string on the same line as a command output? 
You can use the -n parameter for the echo statement to do that. Just change the fi rst
echo statement line to this:

echo -n "The time and date are: "

You need to use quotes around the string to ensure that there’s a space at the end of the
echoed string. The command output begins exactly where the string output stops. The output now looks like this:

$ ./test1
The time and date are: Mon Feb 21 15:42:23 EST 2014
Let's see who's logged into the system:
Christine     tty2      2014-02-21 15:26
Samantha      tty3      2014-02-21 15:26
Timothy       tty1      2014-02-21 15:26
user          tty7       2014-02-19 14:03 (:0)
user          pts/0     2014-02-21 15:21 (:0.0)
$

Perfect! The echo command is a crucial piece of shell scripts that interact with users. You’ll
fi nd yourself using it in many situations, especially when you want to display the values of
script variables. Let’s look at that next.

## Using Variables

Just running individual commands from the shell script is useful, but this has its limitations.
Often, you’ll want to incorporate other data in your shell commands to process
information. You can do this by using variables. Variables allow you to temporarily store
information within the shell script for use with other commands in the script. This section
shows how to use variables in your shell scripts.

Environment variables

You’ve already seen one type of Linux variable in action. Chapter 6 described the environment 
variables available in the Linux system. You can access these values from your shell
scripts as well.

The shell maintains environment variables that track specifi c system information, such as
the name of the system, the name of the user logged in to the system, the user’s system ID
(called UID), the default home directory of the user, and the search path used by the shell
to fi nd programs. You can display a complete list of active environment variables available
by using the set command:

$ set
BASH=/bin/bash
[...]
HOME=/home/Samantha
HOSTNAME=localhost.localdomain
HOSTTYPE=i386
IFS=$' \t\n'
IMSETTINGS_INTEGRATE_DESKTOP=yes
IMSETTINGS_MODULE=none
LANG=en_US.utf8
LESSOPEN='|/usr/bin/lesspipe.sh %s'
LINES=24
LOGNAME=Samantha
[...]

You can tap into these environment variables from within your scripts by using the environment variable’s name preceded by a dollar sign. This is demonstrated in the following
script:

$ cat test2
#!/bin/bash
# display user information from the system.
echo "User info for userid: $USER"
echo UID: $UID
echo HOME: $HOME
$

The $USER, $UID, and $HOME environment variables are used to display the pertinent
information about the logged-in user. The output should look something like this:

$chmod u+x test2
$ ./test2
User info for userid: Samantha
UID: 1001
HOME: /home/Samantha
$

Notice that the environment variables in the echo commands are replaced by their current
values when the script runs. Also notice that we were able to place the $USER system variable 
within the double quotation marks in the fi rst string, and the shell script still fi gured
out what we meant. There is a drawback to using this method, however. Look at what happens in this example:

$ echo "The cost of the item is $15"
The cost of the item is 5

That is obviously not what was intended. Whenever the script sees a dollar sign within
quotes, it assumes you’re referencing a variable. In this example, the script attempted to
display the variable $1 (which was not defi ned) and then the number 5. To display an
actual dollar sign, you must precede it with a backslash character:

$ echo "The cost of the item is \$15"
The cost of the item is $15

That’s better. The backslash allowed the shell script to interpret the dollar sign as an actual
dollar sign and not a variable. The next section shows how to create your own variables in
your scripts. 

--> NOTE

You may also see variables referenced using the format ${variable}. The extra braces around the variable name are
often used to help identify the variable name from the dollar sign.

User variables

In addition to the environment variables, a shell script allows you to set and use your own
variables within the script. Setting variables allows you to temporarily store data and use it
throughout the script, making the shell script more like a real computer program.

User variables can be any text string of up to 20 letters, digits, or an underscore character. User
variables are case sensitive, so the variable Var1 is different from the variable var1. This little
rule often gets novice script programmers in trouble.







































































































































































































