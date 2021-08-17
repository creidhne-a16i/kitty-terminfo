### KiTTY-terminfo
A bit better terminfo file for [KiTTY (a PuTTY fork)](http://www.9bis.net/kitty/), based off various documentation, comparison between xterm-256color, putty-256color, tmux-256color and on my own testing. This is supposed to:
* fix arrows and function key problems
* allow using modifier combinations with those keys
* allow nice boxes
* generally better represent capabilities of KiTTY than usual xterm\* or putty\* terminfo does

Tested on debian.  
  
### Installation
This instruction assumes you have ncurses-term and libreadlinei6 or 7 installed (comes by default with standard Debian installation, and many other packages depend on it, so chances are you already have it installed).
Clone the repository to your remote machine, then run:  
```
sudo tic -x -o /lib/terminfo/ ~/kitty-terminfo/terminfo/xterm-kitty-256color.ti
```
Make sure the newly created file is /lib/terminfo/x/xterm-kitty-256color.
  
Make sure your remote system locales are set to UTF-8, preferably en_US.UTF-8.
  
Close KiTTY. Download this kitty.ini configuration file, and replace your original ini file with it; OR follow the information provided [here](docs/kitty-config.md).

Reconnect to your remote machine, and verify you're running `xterm-kitty-256color`:
```
echo $TERM
```

Command: 
```
infocmp -xT $TERM
```
should display slightly rearranged version of the .ti file you cloned at the beginning.  
**For complete installation, it is very important to take care of the settings in various files mentioned below, as the raw key sequences sent by KiTTY will be modified by default and/or overwritten by other software before they are interpreted by the program you are attempting to control.**  

### Settings  
#### key sequences

In the table are the key sequences KiTTY is sending when arrows and function keys are pressed (`\e` is escape character, displayed as `^[` when you see raw key sequence). These will come useful later on when rebinding your keys:  
  
| ---                | up        | down      | right     | left      |
| -----------------: | :-------: | :-------: | :-------: | :-------: |
| rmkx mode          |   `\e[A`  |   `\e[B`  |   `\e[C`  |   `\e[D`  |
| smkx mode          |   `\eOA`  |   `\eOB`  |   `\eOC`  |   `\eOD`  |
| Shift              | `\e[1;2A` | `\e[1;2B` | `\e[1;2C` | `\e[1;2D` |
| Alt                | `\e[1;3A` | `\e[1;3B` | `\e[1;3C` | `\e[1;3D` |
| Shift + Alt        | `\e[1;4A` | `\e[1;4B` | `\e[1;4C` | `\e[1;4D` |
| Ctrl               | `\e[1;5A` | `\e[1;5B` | `\e[1;5C` | `\e[1;5D` |
| Shift + Ctrl       | `\e[1;6A` | `\e[1;6B` | `\e[1;6C` | `\e[1;6D` |
| Alt + Ctrl         | `\e[1;7A` | `\e[1;7B` | `\e[1;7C` | `\e[1;7D` |
| Shift + Alt + Ctrl | `\e[1;8A` | `\e[1;8B` | `\e[1;8C` | `\e[1;8D` |
  
If you disable "application cursor mode" in KiTTY -> Terminal -> Features, KiTTY won't switch into smkx mode (emulation of numpad sequences in numpad's function mode [numlockoff] - this is done by the software as remote terminal has no access to the state of your hardware), which I do not recommend, as it is used by programs like vim.  
  
Function keys + their modifier sequences:

| ---                | insert  | delete  | home    | end     | page up   | page down |
| -----------------: | :-----: | :-----: | :-----: | :-----: | :-------: | :---: |
| raw                | `\e[2~`   | `\e[3~`   | `\e[1~`   | `\e[4~`   | `\e[5~`     | `\e[6~` |
| Shift              | paste   | `\e[3;2~` | `\e[1;2H` | `\e[1;2F` | scroll up | scroll down |
| Alt                | `\e[2;3~` | `\e[3;3~` | `\e[1;3H` | `\e[1;3F` | `\e[5;3~`   | `\e[6;3~` |
| Shift + Alt        | `\e[2;4~` | `\e[3;4~` | `\e[1;4H` | `\e[1;4F` | `\e[5;4~`   | `\e[6;4~` |
| Ctrl               | `\e[2;5~` | `\e[3;5~` | `\e[1;5H` | `\e[1;5F` | `\e[5;5~`   | `\e[6;5~` |
| Shift + Ctrl       | `\e[2;6~` | `\e[3;6~` | `\e[1;6H` | `\e[1;6F` | `\e[5;6~`   | `\e[6;6~` |
| Alt + Ctrl         | `\e[2;7~` | system  | `\e[1;7H` | `\e[1;7F` | `\e[5;7~`   | `\e[6;7~` |
| Shift + Alt + Ctrl | `\e[2;8~` | `\e[3;8~` | `\e[1;8H` | `\e[1;8F` | `\e[5;8~`   | `\e[6;8~` |
  
F-keys sequences:  

| F1 | F2 | F3 | F4 | F5 | F6 | F7 | F8 | F9 | F10 | F11 | F12 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| `\e[11~` | `\e[12~` | `\e[13~` | `\e[14~` | `\e[15~` | `\e[17~` | `\e[18~` | `\e[19~` | `\e[20~` | `\e[21~` | `\e[23~` | `\e[24~` |

Modifiers for F-keys have been disabled due to terminfo file size constraints.

#### `~/.inputrc`
Make sure to get rid of any other default bindings present here, as they **will** interfere with your arrow keys in other programs. Entire file should look have this at minimum:
```
set input-meta on
set output-meta on
"\e[": skip-csi-sequence

# PLACE YOUR BINDINGS HERE
```
And can be located either in `/etc/inputrc` or `~/.inputrc` ([docs](https://www.gnu.org/software/bash/manual/html_node/Readline-Init-File.html)). Keep in mind comments have to be on separate lines. `input-meta` and `output-meta` settings are necessary for this library to not mess with 8th-bit of the characters sent (affects shift key). `skip-csi-sequence` causes the function keys to produce no output into the buffer if they have no functionality bound to them.
Use key sequences mentioned in tables above to set up commands you wish the terminal to recognize. Remember to replace `^[` with `\e`, copypasting them will not work.

For example, settings below set shift+L/R arrow and ctrl+L/R arrow to skip words:  
```
"\e[1;5C": forward-word
"\e[1;5D": backward-word
"\e[1;2C": forward-word
"\e[1;2D": backward-word
```
Other bindable readline commands can be found [here](https://www.gnu.org/sotfware/bash/manual/html_node/Bindable-Readline-Commands.html). 

#### bash
[Bash startup files](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html) can also interfere with key sequences registered, or even change the terminfo you are using. For our purposes, it is best to get rid of any unwanted key sequence changes from those files and terminfo changes, if any are present. Specific files you need to browse through are `~/.bash_profile`, `~/.bash_login` and `~/.profile`.

#### `~/.tmux.conf`
Similar to readline library, you can create custom keybindings in `.tmux.conf` using modified keys. Examples:  

```
# clear ctrl+arrows so they pass to the console and get interpreted as forward/backward-word
unbind C-Left
unbind C-Right
unbind C-Up
unbind C-Down

# alt+shift+arrow resizing 1x (without prefix key)
bind -n M-S-Down resize-pane -D
bind -n M-S-Up resize-pane -U
bind -n M-S-Left resize-pane -L
bind -n M-S-Right resize-pane -R

# alt+ctrl+arrow resizing 5x (without prefix key)
bind -n C-M-Down resize-pane -D 5
bind -n C-M-Up resize-pane -U 5
bind -n C-M-Left resize-pane -L 5
bind -n C-M-Right resize-pane -R 5

# alt+arrow to switch panes (without prefix key)
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

I recommend also putting in this line:  
```
set-window-option -g xterm-keys on
```  
so the modified keys get passed to the underlying application (like vim).  
I recommend not setting any particular terminfo for tmux to use for the moment. Tmux seems to be doing just fine on its own picking up all the available keys and modes. If you need to specify some changes, use `terminal-overrides` option in `.tmux.conf`.  
Remember tmux has two different keybindings styles in copy mode - vi and emacs. See `man tmux` for more information on how to change this to your liking.  

#### `~/.vimrc`
As with others, Vim also requires some additional configuration to get the most out of all the key combinations available.


### Known problems
 * shift+page up/down are a built-in KiTTY functions that control client-side scrollback. Using it in tmux or vim will scroll them out of view. Unfortunately, neither readline nor tmux have pure scrollback management commands (scrolling file in vim is not a problem though). In tmux, you have to enter copy mode to do that (refer to tmux keybindings listing to see which combinations it uses for you). If you know how to set this up without going through copy mode, let me know!    
 * terminfo file size constraints prevented me from putting F-key modifiers in. If you know how to set up thse up via KEY_DEFINE during the initial handshake in KiTTY, let me know!

### Resources
http://www.nesssoftware.com/home/mwc/manpage.php?page=terminfo  
https://www.freebsd.org/cgi/man.cgi?query=terminfo&sektion=5  
http://www.comptechdoc.org/os/linux/howlinuxworks/linux_hltermcommands.html  
http://invisible-island.net/ncurses/terminfo.src.html  
https://linux.die.net/man/5/terminfo  
http://pubs.opengroup.org/onlinepubs/7908799/xcurses/terminfo.html  
https://lists.gnu.org/archive/html/bug-ncurses/2016-01/msg00010.html
