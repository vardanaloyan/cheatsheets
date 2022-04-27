# common TMUX commands

## Session management

- `tmux` - Create a new tmux session
- `tmux ls` - list tmux sessions
- `Ctrl + b + d` - detach current session 
- `tmux detach` - detach current session
- `tmux at –t 0` - attach to the target (t) session
- `tmux new -s Vardan` - Create session with custom name
- `Ctrl + b + s` - show(list) sessions, you can navigate up/down using arrows to select the session and hit Enter, for switch

## Windows

- `Ctrl + b + c` - Create a new window
- `Ctrl + b + n` - Move to the next window
- `Ctrl + b + p` - Move to the previous window
- `Ctrl + b + {0, 1, …, N}` - Move to the i-th window
- `Ctrl + b + x` - Close current window (It will ask for a confirmation y/n on the status bar)
- `Ctrl + b + w` - Show(list) windows


## Panes

- `Ctrl + b + “` - vertical split
- `Ctrl + b + %` - horizontal split
- `Ctrl + b + o` - Move focus to other pane
- `Ctrl + b + arrow (up, down, left, right)` - Same as above
- `Ctrl + b + z` - Zoom in/out current pane
- `Ctrl + b + x` - Kill the current pane
- `Ctrl + b + esc + arrow` - Resize the current pane
- `Ctrl + b + t` - Show time on the current pane

## Customization

You can add your custom configs to `~/.tmux.conf` or set them directly inside the session
by typing

`Ctrl + b + :` - It will enter prompt for typing a command, then hit Enter

`tmux source-file ~/.tmux.conf` - When you update `.tmux.conf` you can reload your tmux configs


This is my personal `tmux` configuration.

`tmux` version on my laptop is `3.2`

```editorconfig
setw -g mouse on

set-window-option -g mode-keys vi
bind-key -T copy-mode-vi v send -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-pipe-and-cancel "pbcopy"
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "pbcopy"
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "pbcopy"

bind-key -n C-k clear-history
bind-key -n C-l send-keys 'C-l'

# Activity monitoring
set-option -g monitor-activity on


# default statusbar colors
# set -g status-fg white
# set -g status-bg cyan

set-option -g allow-rename off
```
