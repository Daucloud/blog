+++
date = '2025-04-30T01:43:02+08:00'
draft = false
title = 'Chezmoi——一种优雅的点文件管理工具'
categories = ['Programming']
+++

想必经常在多台开发机器之间切换的朋友都有这样的烦恼：当使用一台新的服务器或者起了一份新的 Docker 时，从 0 开始的开发环境让人极不习惯，重新配一份又过于浪费时间。笔者经过一番检索，发现了 [Chezmoi](https://www.chezmoi.io/) 这个优雅的点文件管理工具，几乎完美解决了上述痛点，因此在这里安利给大家。

# 概述

Chez Moi 在法语中是“在我家”的意思。顾名思义，该工具也是用来管理位于家目录下各种配置文件（主要也就是各种点文件）的，并集成了诸如多设备同步、加密和脚本等各种高级功能。

chezmoi 的哲学是将所有文件托管到`~/.local/share/chezmoi`这个 Git 仓库，我们可以将已有的文件添加其中，也可以将其中的文件应用到实际的家目录，或者托管到 GitHub 等平台上。

# 安装

最简洁的安装方法只需要运行如下一行命令：

```shell
sh -c "$(curl -fsLS get.chezmoi.io)"
```

当然，身为大陆用户，有的时候连接 GitHub 有困难，可以运行如下命令：

```shell
sh -c "$(curl -fsLS https://github.moeyy.xyz/https://raw.githubusercontent.com/Daucloud/dotfiles/refs/heads/main/install.sh)"
```

如果你已经在`https://github.com/$GITHUB_USERNAME/dotfiles`托管了你的点文件，也可以运行如下命令一键安装+配置：

```shell
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply $GITHUB_USERNAME
```

# 基本使用

如果你是第一次使用 chezmoi，可以遵循如下步骤开始：

1. `chezmoi init`: 该命令会在`~/.local/share/chezmoi`下初始化一个 Git 仓库。
2. `chezmoi add ~/.bashrc`: 将`~/.bashrc`纳入 chezmoi 的管理。其在`~/.local/share/chezmoi`下对应的文件是`dot_bashrc`, 下文统一称为源文件。
3. `chezmoi edit ~/.bashrc`: 编辑源文件。
4. `chezmoi diff`: 查看源文件和实际家目录下的对应文件的差异。
5. `chezmoi -v apply`: 将源文件应用到家目录。其中`-v`选项会显示对实际文件所做的更改，建议添加。
6. `chezmoi cd`: 进入`~/.local/share/chezmoi`.
7. `git add .`然后`git commit`: 提交更改。
8. 在 GitHub 下新建一个仓库(建议命令为`dotfiles`，这样在新机器的安装的时候可以使用`sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply $GITHUB_USERNAME`一键应用配置)，然后:

```shell
git remote add origin git@github.com:$GITHUB_USERNAME/dotfiles.git
git branch -M main
git push -u origin main
```

至此，你已经完成了对点文件的托管！

# 在新机器上应用配置

1. `chezmoi init git@github.com:$GITHUB_USERNAME/dotfiles.git`
2. `chezmoi merge $FILE`: 将当前机器的`$FILE`合并到源文件
3. `chezmoi apply -v`.
4. `chezmoi update -v`: 如果`git@github.com:$GITHUB_USERNAME/dotfiles.git`有新的提交，可用该命令更新源文件。

# 模版

Chezmoi 使用 go 语言编写，因此可以使用 go 模版语法。可以使用如下命令将文件设为模版：

```shell
chezmoi add ~/.gitconfig --template
```

这会在`~/.local/share/chezmoi`下创建一个名为`dot_gitconfig.tmpl`的文件。
模版有很多妙用，譬如我在机器 A 上的 Git 使用邮箱`A@test.com`，在机器 B 上使用`B@test.com`，则我们可以这样实现：

1. `chezmoi edit ~/.gitconfig`，并编辑其中内容如下：

```shell
[user]
    email = {{ .email | quote }}
```

2. `chezmoi cd`
3. `vim .chezmoi.toml.tmpl`，编辑其中内容如下：

```toml
{{ $email := promptStringOnce . "email" "What is your email address" -}}

data:
    email: {{ $email | quote }}
```

这样，在 `A` 上`chezmoi apply`并询问 What is your email address 时，我们键入 `A@test.com`，在`B`中键入 `B@test.com`即可。

可以使用 `chezmoi data` 查看各种内置变量。除此之外，模版还有很多妙用，其余可参看 [chezmoi 官方文档](https://www.chezmoi.io/user-guide/templating/)。

# 脚本

chezmoi 支持添加`run_`开头的脚本，用于在每次 `chezmoi apply` 时执行。其中按照文件名开头的不同分为如下三种类型:

1. `run_`开头：每次`chezmoi apply`都会运行
2. `run_onchange_`开头：如果文件内容相比上次有所变化，则`chezmoi apply`时会运行
3. `run_once_`开头：如果该内容的文件从未执行过，则`chezmoi apply`时会运行

下面举个例子来说明脚本的使用：
身为大陆用户，难以避免需要给`pip`和`conda`等配置镜像源，[Oh-My-Tuna](https://tuna.moe/oh-my-tuna/) 就是一个方便的选择。我们可以写一个如下的脚本，让每次 chezmoi 自动为我们配置好镜像源：

```shell
wget https://tuna.moe/oh-my-tuna/oh-my-tuna.py -O ~/.local/share/chezmoi/run_once_configure_tuna_mirrors.py
```

> 可将所有脚本在 `~/.local/share/chezmoi/.chezmoiscripts`下统一管理，保持目录清洁。

# 加密

chezmoi 支持对管理的文件进行加密。下面以 [age](https://github.com/FiloSottile/age) 加密为例：

1. `chezmoi cd`
2. 创建加密的私钥(可能需要先参照 [age 官网](https://github.com/FiloSottile/age)安装 age 加密工具):

```shell
$ age-keygen | age --armor --passphrase > key.txt.age
Public key: age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl
Enter passphrase (leave empty to autogenerate a secure one):
Confirm passphrase:
```

注意记录公钥(`age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl`)，后面会用到。

3. `echo key.txt.age >> .chezmoiignore`: 防止 chezmoi 在家目录下创建 `key.txt.age`
4. 创建一个脚本，使得第一次 `chezmoi apply` 时解密 `key.txt.age`为私钥 `key.txt`

```shell
$ chezmoi cd
$ cat > run_once_before_decrypt-private-key.sh.tmpl <<EOF
#!/bin/sh

if [ ! -f "${HOME}/.config/chezmoi/key.txt" ]; then
    mkdir -p "${HOME}/.config/chezmoi"
    chezmoi age decrypt --output "${HOME}/.config/chezmoi/key.txt" --passphrase "{{ .chezmoi.sourceDir }}/key.txt.age"
    chmod 600 "${HOME}/.config/chezmoi/key.txt"
fi
EOF
```

5. 创建一份模版配置：

```shell
$ cat > .chezmoi.toml.tmpl << EOF
encryption = "age"
[age]
    identity = "~/.config/chezmoi/key.txt"
    recipient = "age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl"
EOF
```

`.chezmoi.<format>.tmpl`是 chezmoi 的一类特殊文件，用于创建配置文件 `~/.local/share/chezmoi/chezmoi.<format>`

6. 添加想要加密的文件

```shell
chezmoi add ~/.ssh/id_rsa --encrypt
```

如此一来，便不再需要在新的机器上配置各种 ssh 公私钥对了！

# 杂项

1. 对于 Oh-My-Zsh 用户，由于 Oh-My-Zsh 的配置都在 `~/.oh-my-zsh`中管理，所以可以运行 `chezmoi add ~/.oh-my-zsh --exact --recursive`将整个目录纳入 chezmoi 管理。
   > 其实 chezmoi 官方提供了每次动态从 Oh-My-Zsh 官方下载的方法[^1]，但我觉得不如上面的方法简洁。

[^1]: 参见 https://www.chezmoi.io/user-guide/include-files-from-elsewhere/

2. 欢迎参考我的配置： https://github.com/Daucloud/dotfiles
