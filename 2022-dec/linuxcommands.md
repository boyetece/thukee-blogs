
# Linux Commands Productivity
August 08, 2022 by RB

The number of command combinations you can come up with on the Linux command-line is seemingly endless, and there are some real gems out there that you might not know about. In this video, Jay goes over some of his favorite command combos, time-saving tricks, and other various tips he wish he learned sooner. In this video, you'll find all kinds of things to add to your command-line toolbox!

## Returning to the previous working directory:
```
$ cd ~
```
## Changing directory in one go
```
$ pushd /var {To hop into a different directory in one go}
$ popd {To return to previous directory}
```
## Sending apps to the background, then back to the foreground
Assuming you are inside a configuration file or htop and wanted to go back to the terminal without quitting.
```
$ Ctrl + z
```
When you want to return to the configuration file again
```
$ fg
```
What to do when you forget to sudo
```
$ sudo !!
```
## Searching through the command history
```
$ ctrl + r
$ (reverse-i-search)'': {type anything that your previously type}
```
## Re-executing a specific item from your command history
```
$ history
$ !102 {number of the previous commands displayed by history command}
```
## Viewing an expected activity in progress such as disk space in realtime
```
$ watch df -h
``` 
## Viewing the date and time within the command history
```
$ HISTTIMEFORMAT="%Y-%m-%d %T "
$ history {This will now display the time and date for each previous command}
```
OR, you can put `[HISTTIMEFORMAT="%Y-%m-%d %T "]` inside the .bashrc file to make it permanent.

## Making your terminal full-screen
```
Press Press f11
```
## Increasing/Decreasing text in your terminal window
```
$ ctrl and Shift and + {To increase the screen Size}
$ ctrl and - {To decrease the screen Size}
```
## Emptying text from your command-line

You type a long command or executing wget url link and decided to undo it just press :
```
$ ctrl + u - UNDO
```
## Jumping to the beginning or end of the current command
```
$ctrl + a - START FROM THE BEGINNING
$ctrl + e - GOES TO THE END OF THE COMMAND
```
## Chaining commands together
```
$ sudo yum update; tee >> update.txt
or
$ sudo yum update && tee >> update.txt
```
## Following log files
```
$ tail -f
```
## Emptying the content of text files
```
$ truncate -s 0 hello.txt
```
## Formatting command output with columns
```
$ mount | column -t
```