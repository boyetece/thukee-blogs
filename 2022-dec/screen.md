
# Screen Command

A full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells. Each virtual terminal provides the functions of the DEC VT100 terminal and, in addition, several control functions from the ISO 6429 (ECMA 48, ANSI X3.64) and ISO 2022 standards (e.g. insert/delete line and support for multiple character sets). There is a scrollback history buffer for each virtual terminal and a copy-and-paste mechanism that allows the user to move text regions between windows. See **`man screen`**.


## Ensure that the screen package is installed before executing:
```
$ screen
.
.
```

**`Then Press Tab` :**


## Here are the list of options:

|  Shortcut  |	 | Function|
------ | ----- | ----- |
| **`[ctrl+a]`** | 	 |	 Will put you on the command mode. After this you can choose your functions. |
| **`[ctrl+a]+S`** | 	|	 This will spit the screen horizontally only without a proper prompt terminal. **`CTRL+A`** again to go to the split screen, then you can type **`C`**  to create a prompt terminal. |
| **`[ctrl+a]+[tab]`** | 	|	 Will switch between terminal prompt. |
| **`[ctrl+a]+\|`** | | Split Vertical (Top/Bottom) |
| **`[ctrl+a]+c`** | | Create Screen/Start Shell |
| **`[ctrl+a]+0,1,etc.`** | | Switch by Window ID |
| **`[ctrl+]+A`** | | Rename Window |
| **`[ctrl+a]+X`** | | Close Active Window |
| **`[ctrl+a]+Q`** | | Close All Inactive Windows
|

## Detaching screen activiy:
| Function | | Shortcut |
---| ---| ---|
| Detach Screen | |	**`[ ctrl+a ] + d`** |

## Resuming screen activiy:
```
$ screen -r {screen id number}
```


| Function | | Shortcut |
---| ---| ---|
| Detach Screen | |	**`[ ctrl+a ] + d`** |
| Quit/Kill Screen || **`[ ctrl+a ] + k`** |
| Switch to Next | | **`[ ctrl+a ] + n`** |
| Switch to Previous | | **`[ ctrl+a ] + p`** |
| All Keybindings | | **`[ ctrl+a ] + ?`**
|



[**SCREEN REFERENCE**](https://manpages.org/screen)