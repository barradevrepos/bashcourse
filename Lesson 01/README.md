# Lesson 1: Bash scripting

Bash is a programming language and, at the same time, a command interpreter. Its main characteristic is that it is designed to interact directly with the operating system and with the tools it provides.

In other languages, such as Python or PHP, it is common to include libraries that add functions to perform specific tasks. For example, a library may allow us to work with files, connect to a database, make network requests, or process information.

In Bash, many of these functions are already available through the operating system's own tools. Commands such as `grep`, `find`, `sed`, `awk`, `curl`, `ps`, or `ip` can be seen, in a certain sense, as the tools Bash uses to build its solutions.

In this way, Bash allows us to combine small, specialized programs, passing information from one to another and controlling their execution through the language's own structures.

For this reason, learning Bash is not only about learning its syntax. It also involves progressively becoming familiar with the tools available on a GNU/Linux system and understanding how they can work together.

### First contact with system tools

We can begin by running a few simple commands to obtain information about different aspects of the operating system.

For example:

```bash
uname -a
```

Displays information about the system and the kernel.

```bash
free -h
```

Displays information about memory usage.

```bash
df -h
```

Displays information about used and available space on file systems.

We can execute each command independently, but Bash also allows us to write several commands on the same line by separating them with `;`.

For example:

```bash
uname -a ; free -h ; df -h
```

In this case, Bash will execute the three commands one after another.

When we open a terminal and work from the command line, we are normally interacting with a running Bash instance. Bash acts as an interpreter: it receives what we type, interprets the instruction, and executes the corresponding commands. This happens both when we run a single command and when we combine several commands on the same line.

### Our first script

A Bash script is, essentially, a file that contains a sequence of commands arranged according to the task we want to perform.

When we execute that file, we invoke a Bash interpreter that reads the instructions and executes them one by one, in the order in which they appear.

Conceptually, this is not very different from what we did earlier when we wrote several commands separated by `;`:

```bash
uname -a ; free -h ; df -h
```

In a script, we can write those same commands one below another and save the sequence so that we can run it whenever we need it.

The file in this example will be:

```text
/home/user/commands.sh
```

Its contents will be:

```bash
#!/bin/bash

# File: /home/user/commands.sh
# Replace 'user' with the corresponding username

# Display system and kernel information
uname -a

# Display memory usage information
free -h

# Display file system space information
df -h
```

The first line:

```bash
#!/bin/bash
```

indicates which interpreter should be used to execute the contents of the file.

In this way, Bash goes through the script and executes each command one after another.

### Ways to execute a script

One way to execute our script is to explicitly invoke a new Bash interpreter and pass it the file it should process:

```bash
bash /home/user/commands.sh
```

In this case, a new Bash instance is started. It opens the file, reads its instructions, and executes them one by one.

Another possibility is to give the file execution permission:

```bash
chmod +x /home/user/commands.sh
```

and execute it directly:

```bash
/home/user/commands.sh
```

Or, if we are currently in the same directory as the file, we can use a relative path:

```
./commands.sh
```

In this case, the system uses the first line of the script:

```bash
#!/bin/bash
```

to determine which interpreter should execute the file.

It is important to note that this second method does not execute the script inside the Bash instance we currently have open either: the interpreter specified by `#!` is started. To execute the script within the current Bash interpreter, we would use `source`:

```bash
source /home/user/commands.sh
```

### Output redirection

Bash allows us to redirect the output of a command to a file using the `>` and `>>` operators.

The `>` operator stores the output in a file. If the file already exists, its contents are replaced.

```bash
uname -a > system.txt
```

The `>>` operator also stores output in a file, but appends the new information to the end without deleting the existing contents.

```bash
free -h >> system.txt
```

We can apply this to our script to store all the information obtained in a single file:

```bash
#!/bin/bash

# File: /home/user/commands.sh

# Create the report file and indicate which user generated it
# $USER is an environment variable that contains your username
echo "Report generated by $USER" > /home/$USER/system-info.txt

# Add the generation date and time
# $HOME is an environment variable that contains the path to your home directory
date >> $HOME/system-info.txt

# Add system and kernel information
uname -a >> $HOME/system-info.txt

# Add memory usage information
free -h >> $HOME/system-info.txt

# Add file system information
df -h >> $HOME/system-info.txt

```

In this case, `system-info.txt` is created or replaced when the first command is executed, and the following commands append their output to the end of the same file.

The contents of the generated file can be displayed in the terminal using the `cat` command:

```bash
cat /home/$USER/system-info.txt
```

`cat` reads the contents of the file and prints them directly to standard output.

### Standard output and error output

When we execute a command, Bash distinguishes between the program's normal output and its error messages.

We can see this with the following example:

```bash
ls /home /nofile > ~/output.txt
```

`/home` exists, so its contents are sent to `output.txt` inside our home directory. On the other hand, `/nofile` does not exist, and the error message is still displayed in the terminal.

This happens because `>` redirects only standard output.

Error messages use another channel, identified as `2`. To redirect only errors, we can use:

```bash
ls /home /nofile 2> ~/errors.txt
```

In this case, the information corresponding to `/home` is displayed normally in the terminal, while the error produced by `/nofile` is stored in `errors.txt`.

We can also redirect each output stream to a different file:

```bash
ls /home /nofile > ~/output.txt 2> ~/errors.txt
```

In this way, the successful output is stored in `~/output.txt` and the error messages are stored in `~/errors.txt`.

We can also send both standard output and error output to the same file:

```bash
ls /home /nofile > ~/result.txt 2>&1
```

In this case, `>` redirects standard output to `result.txt`, and `2>&1` indicates that error output should be sent to the same destination as standard output.

## Activities

### Environment

To complete the activities in this lesson, we will use a virtual machine running Debian or Ubuntu.

The goal is to work directly from a Bash terminal and apply the concepts covered so far in the same script: command execution, environment variables, and output redirection.

The script should use the `$USER` and `$HOME` environment variables introduced earlier. `$USER` contains the name of the current user, and `$HOME` contains the path to that user's home directory.

We will also use the `>`, `>>`, and `2>` redirection operators to control where standard output and the error messages produced by commands are stored.

### Activity: automate system updates

On Debian and Ubuntu, `apt` is one of the main tools used to manage software packages. It allows us to query configured repositories, install or remove packages, and keep the system up to date.

A typical update process may include several operations:

```bash
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
```

`apt update` refreshes the available information about packages and their versions. `apt upgrade` installs available updates. `apt autoremove` removes dependencies that are no longer required, and `apt clean` removes downloaded packages stored in the cache.

Some operations may ask for confirmation before continuing. `apt` provides the `-y` option to automatically answer yes to confirmation prompts:

```bash
sudo apt upgrade -y
```

This is especially useful when an operation is part of a script and we want to prevent execution from stopping while waiting for user input.

The activity consists of creating a script that automates this update process. In addition to executing the required commands, we are interested in recording what happens during its execution.

To do this, you should use the redirections studied earlier, separating standard output from error messages and storing both in files inside the user's home directory. You should also use the `$USER` and `$HOME` environment variables to identify the user who ran the process and determine where the generated files should be stored.

The objective is not only to automate the update, but also to leave a record that can later be reviewed to determine what happened during the process.

**Script tests**

Once the script has been created, it is not enough to check that it runs. We should also verify that the redirections work correctly and that we can distinguish what happened during the process.

Perform different tests and observe what information is recorded in each file.

1. Run the script with administrative privileges.

   Verify that the update can be performed and review what information was stored in the standard output file and what was stored in the error file.

2. Run the same script without administrative privileges.

   Observe which commands fail and verify that those messages are recorded in the file intended for error output.

3. Run the script with an Internet connection.

   Verify that `apt update` can access the repositories and review the information it produces during the process.

4. Temporarily disconnect the virtual machine from the network and run the script again.

   Observe the errors produced when attempting to access the repositories and verify that they are sent to the corresponding file.

5. Finally, compare the files generated by each test using `cat`.

The objective of these tests is to verify that the script not only automates the update process, but also documents what happened during a normal execution and what happened when a problem occurred.

### Activity: searching for files with `find`

The `find` command allows us to search for files and directories using different criteria, such as name, type, location, or modification date.

The general form is:

```bash
find path criteria
```

For example, to search inside `/etc` for all files with the `.conf` extension:

```bash
find /etc -type f -name "*.conf"
```

`/etc` indicates where the search begins, `-type f` limits the result to regular files, and `-name "*.conf"` selects only those whose names end in `.conf`.

We can also search only for files located directly inside `/proc`:

```bash
find /proc -maxdepth 1 -type f
```

The `-maxdepth 1` option prevents `find` from continuing the search inside subdirectories.

Another possible criterion is the modification date. To search inside `/var/log` for files modified during the last seven days:

```bash
find /var/log -type f -mtime -7
```

`-mtime -7` selects files whose contents were modified less than seven days ago.

**Assignment**

Create a script that automatically performs the three searches above:

```bash
find /etc -type f -name "*.conf"

find /proc -maxdepth 1 -type f

find /var/log -type f -mtime -7
```

The script should save the search results in files inside the user's home directory.

Use the `$USER` and `$HOME` environment variables and the redirection operators studied earlier.

Separate standard output from error messages so that both the files found and any problems that occurred during the searches can be reviewed later.

**Tests**

Once the script is finished, perform different executions to verify that the redirections work correctly.

First, run it normally, without administrative privileges:

```bash
./find-files.sh
```

Review the generated files and observe whether any of the searches produce error messages because the user does not have sufficient permissions for certain directories or files.

Then run the same script with administrative privileges:

```bash
sudo ./find-files.sh
```

Compare the results with the previous execution.

Finally, use `cat` to review both the standard output and the error messages that were recorded.

The objective of the activity is to verify how the same command can produce different results depending on the user's permissions and how redirections make it possible to preserve the information obtained and the errors produced during execution in separate files.