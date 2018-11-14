## KiTTY-terminfo
A bit better terminfo file for [KiTTY (a PuTTY fork)](http://www.9bis.net/kitty/), based off comparison between xterm-256color, putty-256color and tmux-256color. This is supposed to correct the problems with arrows in tmux and vim, allow nice boxes (aptitude), and generally better represent capabilities of KiTTY than usual xterm\* or putty\* terminfo does.  
  
### Installation
This instruction assumes you have ncurses-term and libreadlinei6 or 7 installed (comes by default with basic Debian installation, and many other packages depend on it, so chances are you already have it installed).
Clone the repository to your remote machine, then run:  
```
sudo tic -x -o /lib/terminfo/ ~/kitty-terminfo/terminfo/xterm-kitty-256color.ti
```
Make sure the newly created file is /lib/terminfo/x/xterm-kitty-256color.
  
Make sure your remote system locales are set to UTF-8, preferably en_US.UTF-8.
  
Close KiTTY. In kitty.ini file, make sure you have shortcuts disabled:
```
shortcuts=no
```
It interferes with F7 key (used by midnight commander and other apps) and seems to be difficult to rebind.

Start KiTTY, make sure to keep following settings (last one is most important):
- Terminal > Keyboard:
 * Backspace key: Control-? (127)
 * The Home and End keys: Standard
 * The Function keys and keypad: ESC[n~
- Connection > Data:
 * Terminal-type string: xterm-kitty-256color  

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
  
Here's the key sequences KiTTY is sending when arrows and function keys are pressed:  
  
|   mode    |     up    |    down   |   right   |    left   |
| --------: | :-------: | :-------: | :-------: | :-------: |
| rmkx mode |   `^[[A`  |   `^[[B`  |   `^[[C`  |   `^[[D`  |
| smkx mode |   `^[OA`  |   `^[OB`  |   `^[OC`  |   `^[OD`  |
|   shift   | `^[[1;2A` | `^[[1;2B` | `^[[1;2C` | `^[[1;2D` |
|    ctrl   | `^[[1;5A` | `^[[1;5B` | `^[[1;5C` | `^[[1;5D` |
|     alt   | `^[[1;3A` | `^[[1;3B` | `^[[1;3C` | `^[[1;3D` |
  
If you disable "application cursor mode" in KiTTY > Terminal > Features, KiTTY won't switch into smkx mode (emulation of numpad sequences in function mode [numlockoff]), which I do not recommend.  
  
| key | code | key | code | key | code |
| :---: | :---: | :---: | :---: | :---: | :---: |
| ins | `^[[2~` | home | `^[[1~` | pgup | `^[[5~` |
| del | `^[[3~` | end | `^[[4~` | pgdn | `^[[6~` |
  
Modifiers don't work with those function keys.  

#### /etc/inputrc
Make sure to get rid of any other default bindings present here, as they **will** interfere with your arrow keys in other programs. Entire file should look more or less like this:
```
set input-meta on
set output-meta on
$if mode=emacs

# mappings for Ctrl-left-arrow and Ctrl-right-arrow for word moving
# PLACE YOUR BINDINGS HERE

$endif
```
And can be located either in `/etc/inputrc` or `~/.inputrc` ([docs](https://www.gnu.org/software/bash/manual/html_node/Readline-Init-File.html)). Keep in mind comments have to be on separate lines.
Use key sequences mentioned in table above this section to set up commands you wish the terminal to recognize. Remember to replace `^[` with `\e`, copypasting them will not work.

For example, settings below set shift+L/R arrow and ctrl+L/R arrow to skip words:  
```
"\e[1;5C": forward-word
"\e[1;5D": backward-word
"\e[1;2C": forward-word
"\e[1;2D": backward-word
```
Other bindable readline commands can be found [here](https://www.gnu.org/software/bash/manual/html_node/Bindable-Readline-Commands.html). Keep in mind that while PageUp and PageDown keys produce `~` in bash command line, these keys are used by other programs (like vim) correctly. I recommend not rebinding them at all here. If you need to scroll your buffer, use default KiTTY's shift+PageUp/PageDown.

#### ~/.tmux.conf
Similarly to readline library, two lines of settings below 
```
set -g default-terminal tmux-256color
set-window-option -g xterm-keys on
```
are necessary for tmux to register and pass on key sequences correctly. Tmux terminfo file `tmux-256color` is included in repository - compile it using 'tic' command just like before:
```
sudo tic -x -o /lib/terminfo/ ~/kitty-terminfo/terminfo/tmux-256color.ti
```

Feel free to set up any key bindings you wish here as well, remember default tmux bindings for ctrl+arrows is switching planes.  
Settings below remove ctrl+arrows bindings (so you can skip words with it instead) and binds switching planes to alt+arrows:    
```

unbind C-Left
unbind C-Right
unbind C-Up
unbind C-Down
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

#### bash
[Bash startup files](https://www.gnu.org/software/bash/manual/html_node/Bash-Startup-Files.html) can also interfere with key sequences registered, or even change the terminfo you are using. For our purposes, it is best to get rid of any unwanted key sequence changes from those files, if any are present. Specific files you need to browse through are `~/.bash_profile`, `~/.bash_login` and `~/.profile`.

### Resources
http://www.nesssoftware.com/home/mwc/manpage.php?page=terminfo  
https://www.freebsd.org/cgi/man.cgi?query=terminfo&sektion=5  
http://www.comptechdoc.org/os/linux/howlinuxworks/linux_hltermcommands.html  
http://invisible-island.net/ncurses/terminfo.src.html  
https://linux.die.net/man/5/terminfo  
http://pubs.opengroup.org/onlinepubs/7908799/xcurses/terminfo.html  
https://lists.gnu.org/archive/html/bug-ncurses/2016-01/msg00010.html
