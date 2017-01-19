---
title: tmux安装配置使用
date: 2017-01-19 16:35:05
tags: tmux
---

## 1.安装
```
# ubuntu
sudo apt-get install tmux
```
或者  
[点击下载tmux安装包](https://github.com/tmux/tmux/releases/download/2.3/tmux-2.3.tar.gz)


## 2.配置文件
~/.tmux.conf
如果文件不存在直接`touch`就可以。
**下面是我的配置文件**
```
unbind C-b
set -g prefix C-a
bind C-a send-prefix

bind r source-file ~/.tmux.conf \; display "tmux.conf reload!"

bind | split-window -h
bind - split-window -v

bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R


/*bind -n C-x setw synchronize-panes */
bind C-e command-prompt -p "session?,message?" "run-shell \"tmux list-windows -t %1 \| cut -d: -f1\|xargs -I\{\} tmux send-keys -t %1:\{\} %2\""

set -g base-index 1
set -g pane-base-index 1

set -g status-fg white
set -g status-bg black

setw -g window-status-fg cyan
setw -g window-status-bg default
setw -g window-status-attr dim

setw -g window-status-current-fg white
setw -g window-status-current-bg red
setw -g window-status-current-attr bright

set -g message-fg green
set -g message-bg black
set -g message-attr bright

set -g status-left "#[fg=green]#S #[fg=yellow]#I #[fg=cyan]#P"
set -g status-left-length 40
set -g status-left "#[fg=green]Session: #S #[fg=yellow]#I #[fg=cyan]#P"
set -g status-right "#[fg=cyan]%d %b %R"
set -g status-utf8 on

set -g status-interval 60
set -g status-justify centre

setw -g monitor-activity on
set -g visual-activity on

setw -g mode-keys vi
```

## 3.tmux部分快捷键
| 按键           | 说明                                                     |
| -------------- | -------------------------------------------------------- |
| Ctrl+a ?       | 列出所有快捷键                                           |
| Ctrl+a c       | 新建窗口                                                 |
| Ctrl+a 数字    | 切换到对应的窗口                                         |
| Ctrl+a n       | 切换到下一个窗口                                         |
| Ctrl+a p       | 切换到上一个窗口                                         |
| Ctrl+a w       | 列出所有窗口                                             |
| Ctrl+a f       | 查找窗口                                                 |
| Ctrl+a ,       | 修改该窗口的名字                                         |
| Ctrl+a %/竖线  | 垂直分隔窗口                                             |
| Ctrl+a "/-     | 水平分隔窗口                                             |
| Ctrl+a o       | 交换光标所在窗格                                         |
| Ctrl+a x       | 关闭窗格                                                 |
| Ctrl+a 空格    | 切换布局                                                 |
| Ctrl+a q       | 显示每个窗格是第几个，当数字出现时按数字就选中第几个窗格 |
| Ctrl+a {       | 与上一个窗格交换位置                                     |
| Ctrl+a }       | 与下一个窗格交换位置                                     |
| Ctrl+a z       | 切换当前窗格最大化/最小化                                |
| Ctrl+a [       | 进入复制模式                                             |
| Ctrl+a [ 空格  | 开始选择内容 回车复制                                    |
| Ctrl+a ]       | 粘贴                                                     |

