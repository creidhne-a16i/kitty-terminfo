#### kitty-terminfo
A bit better terminfo file for KiTTY (a PuTTY fork), based off comparison between xterm-256color, putty-256color and tmux-256color. This is supposed to correct the problems with arrows in tmux and vim, allow nice boxes (aptitude), and generally better represent capabilities of KiTTY than usual xterm\* or putty\* terminfo does. 

###### installation
At the moment I heavily discourage the use of this file. There are some issues with vim running in tmux.

Clone the repository to your remote machine, then run:
> tic -x kitty-terminfo/terminfo/xterm-kitty-256color.ti

If everything is okay, you should get no messages. Verify with:
> infocmp -xT xterm-kitty-256color
Which should display slightly rearranged version of the .ti file you just cloned.

To use ctrl+left/right arrow to move between words in terminal, makes sure you have following lines in /etc/inputrc:
> "\e[1;5C": forward-word
> "\e[1;5D": backward-word
You should not need any other bindings in there anymore.

Edit ~/.tmux.conf to set tmux terminal, clear default tmux bindings for ctrl+arrows so that it passes to the console and allows jumping words, and bind switching planes to alt+arrows:

> set -g default-terminal tmux-256color
> unbind C-Left
> unbind C-Right
> unbind C-Up
> unbind C-Down
> bind -n M-Left select-pane -L
> bind -n M-Right select-pane -R
> bind -n M-Up select-pane -U
> bind -n M-Down select-pane -D

Close KiTTY. In kitty.ini file, make sure you have
> shortcuts=no
as it interferes with F7 key (used by midnight commander and other apps) and seems to be difficult to rebind.

Start KiTTY, make sure to keep following settings:
- Terminal > Keyboard:
 * Backspace key: Control-? (127)
 * The Home and End keys: Standard
 * The Function keys and keypad: ESC[n~
- Connection > Data:
 * Terminal-type string: xterm-kitty-256color

###### resources
http://www.nesssoftware.com/home/mwc/manpage.php?page=terminfo
https://www.freebsd.org/cgi/man.cgi?query=terminfo&sektion=5
http://www.comptechdoc.org/os/linux/howlinuxworks/linux_hltermcommands.html
http://invisible-island.net/ncurses/terminfo.src.html
http://invisible-island.net/ncurses/terminfo.ti.html
https://linux.die.net/man/5/terminfo
http://pubs.opengroup.org/onlinepubs/7908799/xcurses/terminfo.html

