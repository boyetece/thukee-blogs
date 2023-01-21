
# Screen Command

A full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells. Each virtual terminal provides the functions of the DEC VT100 terminal and, in addition, several control functions from the ISO 6429 (ECMA 48, ANSI X3.64) and ISO 2022 standards (e.g. insert/delete line and support for multiple character sets). There is a scrollback history buffer for each virtual terminal and a copy-and-paste mechanism that allows the user to move text regions between windows.

## Detaching and Resuming screen activiy:
| | |
---| ---|
|**`ctrl + d`**| To detach from the session without destroying it.|
|**`ctrl + r`** | To resume the screen session.
|

## Ensure that the screen package is installed before executing:
```
$ screen
.
.
```

**`Then Press Tab` :**


## Here is the list of options:
| | |
---| ---|
|**`ctrl + a`**| will put you on the command mode. After this, you can choose what options you would want to do.|
|**`shift + s`**| This will spit the screen horizontally only without a proper prompt terminal. **`ctrl + a`** again to go to the split screen, then you can type **`c`** to create a prompt terminal.|
|**`Shift + pipe symbol`**| This will spit the screen virtically only without a proper prompt terminal. **`ctrl + a`** again to go to the split screen, then you can type **`c`** to create a prompt terminal.|
|**`Tab key`**| will switch between terminal prompts.|
|**`shift + q`**| Ctrl-a Q (note: upper case Q). This will close the region that is inactive.
|

[**SCREEN REFERENCE**](https://manpages.org/screen)