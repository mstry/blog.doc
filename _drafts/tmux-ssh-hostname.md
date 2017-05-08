---
title: tmux ssh显示主机
date: 2017-04-27 21:47:49
categories:
tags:
---

tmux里面ssh连接多台主机时，需要显示用户名和主机加以区分。

# 1. 重定义ssh命令 #

[不推荐]

```bash
# ${HOME}/.bashrc
function ssh()
{
    if [ "$(ps -p $(ps -p $$ -o ppid=) -o comm=)" = "tmux" ]; then
            tmux rename-window "$*"
            command ssh "$@"
            tmux set-window-option automatic-rename "on" 1>/dev/null
    else
            command ssh "$@"
    fi
}
```

# 2. 修改远端主机bashrc #

```bash
# /etc/bashrc
screen*)
  if [ -e /etc/sysconfig/bash-prompt-screen ]; then
	PROMPT_COMMAND=/etc/sysconfig/bash-prompt-screen
  else
	PROMPT_COMMAND='printf "\033k%s@%s:%s\033\\" "${USER}" "${HOSTNAME%%.*}" "${PWD/#$HOME/~}"'
  fi
  ;;
```

将`/etc/bashrc`，`case $TERM的screen)`分支改为`screen*)`，`PROMPT_COMMAND的\033]0;`改为`\033k`。
如果觉得全路径做为窗口标题太长，可以把`PROMPT_COMMAND的"${PWD/#$HOME/~}"`改为`$(basename "${PWD/#$HOME/~}")`
