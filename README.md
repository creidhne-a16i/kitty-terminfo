## KiTTY-terminfo
A bit better terminfo file for KiTTY (a PuTTY fork), based off comparison between xterm-256color, putty-256color and tmux-256color. This is supposed to correct the problems with arrows in tmux and vim, allow nice boxes (aptitude), and generally better represent capabilities of KiTTY than usual xterm\* or putty\* terminfo does.  
  
### Installation
This instruction assumes you have ncurses-term and libreadlinei6 or 7 installed (comes by default with basic Debian installation, and many other packages depend on it, so chances are you already have it installed).
Clone the repository to your remote machine, then run:  
```
sudo tic -x -o /lib/terminfo/x/ ~/kitty-terminfo/terminfo/xterm-kitty-256color.ti
```
  
If everything is okay, you should get no messages. Verify with:  
```
infocmp -xT $TERM
```
Which should display slightly rearranged version of the .ti file you just cloned.  
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
  
Verify you're running xterm-kitty-256color
```
echo $TERM
```
  
### Settings  
  
Here's the key sequences KiTTY is sending when arrows and function keys are pressed:  
  
|   mode    |     up    |    down   |   right   |    left   |
| --------: | :-------: | :-------: | :-------: | :-------: |
| rmkx mode |   `^[[A`  |   `^[[B`  |   `^[[C`  |   `^[[D`  |
| smkx mode |   `^[OA`  |   `^[OB`  |   `^[OC`  |   `^[OD`  |
|   shift   | `^[[1;2A` | `^[[1;2B` | `^[[1;2C` | `^[[1;2D` |
|    ctrl   | `^[[1;5A` | `^[[1;5B` | `^[[1;5C` | `^[[1;5D` |
|     alt   | `^[[1;3A` | `^[[1;3B` | `^[[1;3C` | `^[[1;3D` |
  
If you disable "application cursor mode" in KiTTY > Terminal > Features, KiTTY won't switch into smkx mode, which I do not recommend.  
  
| key | code |
| ---: | :---: |
| ins | `^[[2~` |
| del | `^[[3~` |
| home | `^[[1~` |
| end | `^[[4~` |
| pgup | `^[[5~` |
| pgdn | `^[[6~` |
  
modifiers don't work with those function keys.  

#### /etc/inputrc
Use codes mentioned above to set up commands you wish the terminal to recognize. Remember to replace ^[ with \e, although entering escape sequence using ctrl+v (this is not paste in terminal!) will also work.
For example, to use ctrl+left/right arrow to move between words in terminal, makes sure you have following lines in your inputrc file:  
```
"\e[1;5C": forward-word
"\e[1;5D": backward-word
```
Other bindable readline commands can be found [here](https://www.gnu.org/software/bash/manual/html_node/Bindable-Readline-Commands.html)  

#### ~/.tmux.conf
Similarly, edit ~/.tmux.conf to at least set tmux terminal (included in repository, if you don't have it, compile it using 'tic' command). Feel free to set up any key bindings you wish here as well, remember default tmux bindings for ctrl+arrows is switching planes.  
Code below sets up terminal, removes ctrl+arrows bindings and binds switching planes to alt+arrows:    
```
set -g default-terminal tmux-256color
unbind C-Left
unbind C-Right
unbind C-Up
unbind C-Down
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D
```

### Resources
http://www.nesssoftware.com/home/mwc/manpage.php?page=terminfo  
https://www.freebsd.org/cgi/man.cgi?query=terminfo&sektion=5  
http://www.comptechdoc.org/os/linux/howlinuxworks/linux_hltermcommands.html  
http://invisible-island.net/ncurses/terminfo.src.html  
http://invisible-island.net/ncurses/terminfo.ti.html  
https://linux.die.net/man/5/terminfo  
http://pubs.opengroup.org/onlinepubs/7908799/xcurses/terminfo.html  

