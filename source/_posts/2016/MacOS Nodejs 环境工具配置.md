title: MacOS Nodejs 环境工具配置
s: macos-nodejs
categories: 
- 软件开发
tags: 
- 软件开发
- nodejs
- macOS
date: 2016-07-25 12:00:00

---

本文讲述从零开始安装一系列Nodejs“环境工具”的方法。
涉及工具：

- Homebrew
- iTerm2
- nodejs, nvm, npm
- zsh / oh-my-zsh

<!-- more -->

## Homebrew - 应用安装和管理工具

首先安装软件包管理器。在 [Homebrew][lnk-brew] 与 Macports 之间，我们选择前者。

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

在安装Homebrew之后，软件包的安装、升级均可通过执行命令行 `brew install <package>` 来进行，详情可参阅参数帮助。

- 可选安装brew增强工具：[cask-room][lnk-cask], [launchrocket][lnk-rocket]

## NVM - Nodejs 包管理工具的使用

Nodejs 更新很快，加上中间 io.js 的历史，导致其版本混乱，管理一直是个问题。因此诞生了Nodejs版本管理工具，常见的为 nvm 和 n 两个工具。这里我们推荐 nvm。

要注意的是，不要使用 brew 来安装 node 和 nvm。如果之前已经用 brew 安装过 node，也请先卸载再继续。

nvm 脚本安装方式：

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.3/install.sh | bash
```

nvm 的更新，按照官方的方法只能手工检查，手工更新。对此，推荐使用 oh-my-zsh 的插件 [zsh-nvm][lnk-zshnvm] 安装。如果采用该方式，可跳过上面的安装脚本。具体安装方法请继续往下看。

## 终端增强

iterm2 + zsh + oh-my-zsh

Mac / Linux 默认的 Shell 为 Bash。一般应用来说足够。

但是我们想要更漂亮的界面，更方便管理的多窗口，更智能的自动完成，更帅的命令行等功能，可以切换为zsh并安装shell增强工具 

首先用 [iTerm2][lnk-iterm] 替换掉难看的系统终端
然后安装 [oh-my-zsh][lnk-omz]，选择喜欢的颜色主题。酷炫的命令行窗口就出现了。

上面说过，我们用 zsh-nvm 来管理 nvm。我们需要 [zsh-nvm][lnk-zshnvm]这个插件。完成上述几步，启用该插件后，我们的准备工作就完成了。

## 安装、更新 Nodejs 版本

现在你可以随时使用 `nvm upgrade` 来更新 NVM。用 NVM 来安装和管理Nodejs 版本，npm 来管理 node 模块。

至此，一个顺手的 Nodejs 环境准备完成。

## 参考

- [Homebrew][lnk-brew]
- [NVM][lnk-nvm]
- [oh-my-zsh][lnk-omz]
- [zsh-nvm][lnk-zshnvm]
- [iTerm2][lnk-iterm]
- [cask-room][lnk-cask]
- [launchrocket][lnk-rocket]

[lnk-brew]: http://brew.sh/
[lnk-iterm]: https://iterm2.com/
[lnk-nvm]: https://github.com/creationix/nvm
[lnk-zshnvm]: https://github.com/lukechilds/zsh-nvm
[lnk-omz]: https://github.com/robbyrussell/oh-my-zsh/
[lnk-cask]: https://caskroom.github.io/
[lnk-rocket]: https://github.com/jimbojsb/launchrocket

