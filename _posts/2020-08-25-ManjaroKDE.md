---
layout: post
title: ManjaroKDE 配置与美化
date: 2020-08-25
tags: manjaro linux
---

## 配置国内源

1. 切换镜像源至中国

	`sudo pacman-mirrors -i -c China -m rank` 

	在弹出的窗口中选一个最快的源

2. 更新

	`sudo pacman -Syyu`

## 好东西们

> **pacman** `-S` 安装， `-R` 卸载。

### 0. OvO

#### 总是最先要安装的东西们

```shell
sudo pacman -S git
sudo pacman -S neovim
```

#### 总是最先要搞定的东西们

- 把 `/home` 下的各种目录名改成英文

	先重命名。

	再修改目录映射文件 `~/.config/user-dirs.dirs`

	```
	XDG_DESKTOP_DIR="$HOME/Desktop"
	XDG_DOWNLOAD_DIR="$HOME/Download"
	XDG_TEMPLATES_DIR="$HOME/Templates"
	XDG_PUBLICSHARE_DIR="$HOME/Public"
	XDG_DOCUMENTS_DIR="$HOME/Documents"
	XDG_MUSIC_DIR="$HOME/Music"
	XDG_PICTURES_DIR="$HOME/Pictures"
	XDG_VIDEOS_DIR="$HOME/Videos"
	```

	重启

### 1. 输入法

基于 `fcitx5` 配置 `rime-cloverpinyin`

1. 安装 `fcitx5` 输入法框架

	`pacman -S fcitx5 fcitx5-qt fcitx5-gtk fcitx5-configtool`

	配置环境变量，编辑 `~/.xprofile` ，写入：

	```
	export GTK_IM_MODULE=fcitx5
	export QT_IM_MODULE=fcitx5
	export XMODIFIERS="@im=fcitx5"
	
	export LANG="zh_CN.UTF-8"
	export LC_CTYPE="zh_CN.UTF-8"
	```

	> 配置开机启动，系统设置->工作区->开机和关机->自动启动->添加程序，添加 `fcitx5`

2. 安装 `rime`

	`pacman -S fcitx-rime`

3. 配置 `cloverpinyin` 输入方案

	到 https://github.com/fkxxyz/rime-cloverpinyin/releases 下载最新的release，并直接解压到 `~/.local/share/fcitx5/rime` 目录中。

	> rime初次初始化完成后才会有这个目录

	在相同目录创建 `deafault.custom.yaml` ，写入：

	```yaml
	patch:
	  "menu/page_size": 9
	  schema_list:
	    - schema: clover
	```

	> `"manu/page_size"` 为每页候选词数目，可根据自己习惯设为1~9。

现在，找到一个可以打字的地方，切换到 `rime` 输入法，右键系统托盘的图标，点击重新部署，待加载完成就可以看到出现 `🍀四叶草简体拼音` 啦。



### 录视频相关

- 录屏 `sudo pacman -S simplescreenrecorder`
- 键盘按键显示 `sudo pacman -S screenkey`

### 编辑器

- neovim `sudo pacman -S neovim`

	