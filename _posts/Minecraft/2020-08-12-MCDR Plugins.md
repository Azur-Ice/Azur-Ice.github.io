---
layout: post
title: MCDR插件
date: 2020-08-12
tags: Minecraft 生电
---

## [Locations](https://github.com/TISUnion/Locations)

- !!loc help -显示帮助消息
- !!loc add <独特名字> <地狱：-1，主世界：0，末地：1> - 加入一个路标
	- 名字不能为 add, del, help
	- tips:为方便搜索，可以加入别名，尽量使用全称，如“僵尸猪人塔/僵尸猪人农场/刷金塔”，而不仅是“猪人塔”
- !!loc add <独特名字> here - 加入自己所处位置、维度的路标
- !!loc del <独特关键词> - 删除路标，要求全字匹配
- !!loc <名字> - 搜索坐标，返回所有匹配项
- !!loc - 列出所有坐标

## [wssChat](https://github.com/TISUnion/wssChat)

可以实现跨服聊天 安装后请用vim打开源码，自行填写密码和serverUrl 注意要pip install websocket_client 指令： !!wss 查看是不是装了跨服聊天 !!wss help 列出帮助列表 !!wss status 查看是否连线 !!wss ping ping其他的服务器

## [joinMOTD](https://github.com/TISUnion/joinMOTD)

一个 MCDaemon 插件，与 MCDReforged 兼容

当玩家加入游戏时向其发送欢迎信息

需要前置插件 [daycount](https://github.com/TISUnion/daycount)

别忘了填写配置文件 `MCD/config/joinMOTD.json`

## [daycount](https://github.com/TISUnion/daycount)

a plugin for MCDaemon, compatible with MCDReforged (0.6.1-alpha+)

u can import getday() in this file

u can also use `!!day` to get the day count since server started

the day when the server started can be set in the script

## [StatsHelper](https://github.com/TISUnion/StatsHelper)

### 格式说明

`!!stats` 显示帮助信息

`!!stats query` <玩家> <统计类别> <统计内容> [<-uuid>] [<-tell>]

`!!stats rank` <统计类别> <统计内容> (-bot) [<-tell>]

`!!stats scoreboard` <统计类别> <统计内容> (标题) (-bot)

`!!stats scoreboard show` 显示该插件的计分板

`!!stats scoreboard hide` 隐藏该插件的计分板

### 参数说明

<统计类别>: killed, killed_by, dropped, picked_up, used, mined, broken, crafted, custom, killed, killed_by 的 <统计内容> 为 [生物id]

picked_up, used, mined, broken, crafted 的 <统计内容> 为物品/方块id

custom 的 <统计内容> 详见统计信息的json文件

上述内容无需带minecraft前缀

[<-uuid>]: 用uuid替换玩家名; (-bot): 统计bot与cam; [<-tell>]: 仅自己可见

### 例子

```
!!stats query Fallen_Breath used water_bucket
!!stats rank custom time_since_rest -bot
!!stats scoreboard mined stone 挖石榜
```

## [Task](https://github.com/TISUnion/Task)

### Usage

`!!task help` 显示帮助信息

`!!task list` 显示任务列表

`!!task detail [任务名称]` 查看任务详细信息

`!!task add [任务名称] [任务描述]` 添加任务

`!!task del [任务名称]` 删除任务

`!!task rename [旧任务名称] [新任务名称]` 重命名任务

`!!task change [任务名称] [新任务描述]` 修改任务描述

`!!task done [任务名称]` 标注任务为已完成

`!!task undone [任务名称]` 标注任务为未完成

注: 上述所有 `[任务名称]` 可以用 `[任务名称].[子任务名称]` 的形式来访问子任务

例: `!!task add 女巫塔.铺地板 挂机铺黑色玻璃`

## [Calculator](https://github.com/TISUnion/Calculator)

A simple in game calculator plugin for [MCDaemon](https://github.com/kafuuchino-desu/MCDaemon) or [MCDReforged](https://github.com/Fallen-Breath/MCDReforged)

For MCDR it needs MCDR version >= `0.9.1-alpha`

### Usage

It will calculate the expression with an extra prefix `==` such as `==1+1`. All unrelated chars will be ignored

Try input `==3*4-(1+3.0)` in game chat and that's it

If module [simpleeval](https://pypi.org/project/simpleeval/) is installed and constant `ENABLE_SIMPLEEVAL` is set to `True` it will use `simpleeval.simple_eval` with names and functions in `math` module for advanced calculation

## [QuickBackupM](https://github.com/TISUnion/QuickBackupM)

备份的存档将会存放至 qb_multi 文件夹中，文件目录格式如下：

```
mcd_root/
    server.py
    
    server/
        world/
        
    qb_multi/
        slot1/
            info.json
            world/
            
        slot2/
            ...
        ...
        
        overwrite/
            info.txt
            world/
```

### 命令格式说明

`!!qb` 显示帮助信息

`!!qb make [<comment>]` 创建一个储存至槽位 `1` 的备份，并将后移已有槽位。`<comment>` 为可选存档注释

`!!qb back [<slot>]` 回档为槽位 `<slot>` 的存档。

`!!qb del [<slot>]` 删除槽位 `<slot>` 的存档。

`!!qb confirm` 在执行 `back` 后使用，再次确认是否进行回档

`!!qb abort` 在任何时候键入此指令可中断回档

`!!qb list` 显示各槽位的存档信息

当 `<slot>` 未被指定时默认选择槽位 `1`

在 MCDR 环境下，默认配置下 `!!qb back` 以及 `!!qb share` 需要权限等级 `helper`

### 一些常量说明

调整这些常量的数值也就是在配置 QuickBackupM 插件

#### SizeDisplay

默认值: `SizeDisplay = True`

查看备份列表是否显示占用空间

#### SlotCount

默认值: `SlotCount = 5`

存档槽位的数量

#### Prefix

默认值: `Prefix = '!!qb'`

触发指令的前缀

#### BackupPath

默认值: `BackupPath = './qb_multi'`

备份储存的路径

#### TurnOffAutoSave

默认值: `TurnOffAutoSave = True`

是否在备份时临时关闭自动保存

#### IgnoreSessionLock

默认值: `IgnoreSessionLock = True`

是否在备份时忽略文件 `session.lock`。这可以解决 `session.lock` 被服务端占用导致备份失败的问题

#### WorldNames

默认值:

```
WorldNames = [
    'world',
]
```

需要备份的世界文件夹列表，原版服务端只会有一个世界，在默认值基础上填上世界文件夹的名字即可

对于非原版服务端如水桶、水龙头服务端，会有三个世界文件夹，此时可填写：

```
WorldNames = [
    'world',
    'world_nether',
    'world_the_end',
]
```

#### MinimumPermissionLevel

默认值:

```
MinimumPermissionLevel = {
	'make': 1,
	'back': 2,
	'confirm': 1,
	'abort': 1,
	'share': 2,
	'list': 0,
}
```

一个字典，代表使用不同类型指令需要权限等级。数值含义见[此处](https://github.com/Fallen-Breath/MCDReforged/blob/master/doc/readme_cn.md#权限)

把所有数值设置成 `0` 以让所有人均可操作

## [Here](https://github.com/TISUnion/Here)

一个 兼容 [MCDaemon](https://github.com/kafuuchino-desu/MCDaemon) 以及 [MCDReforged](https://github.com/Fallen-Breath/MCDReforged) 的插件

当玩家输入 `!!here` 时，玩家的坐标将被显示并且将被发光效果高亮

调整代码中的 `HIGHLIGHT_TIME` 即可控制玩家是否高亮，以及自定义高亮的时长

当 MCDR 启动 rcon 时此插件可使用 rcon 来获得玩家信息，响应更快