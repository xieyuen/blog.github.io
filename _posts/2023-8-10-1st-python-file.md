---
layout: post
title: 我自己的第一个 MCDR 插件
permalink: /posts/mcdr-plg/tools
---

对 MC 服务器卡死无法关闭的情况做了优化

我的服务器就不知为什么总是在关闭服务器时卡死，自然状态下需要 `WatchDog` 帮忙还不一定有用

所以最核心的代码就是这个：

```py
def stop(self, server: PluginServerInterface):
    server.logger.info('服务器关闭')
    server.stop()

    # wait 20 secs
    time.sleep(20)

    if server.is_server_running:
        server.logger.warning('服务器似乎卡死了')
        server.logger.warning('3s 后将会杀死服务端')
        time.sleep(3)

        server.kill()

    server.logger.info('服务端已停止')
```

当服务器关闭命令发送 $20s$ 后仍在运行状态就执行 `server.kill()` 方法杀死服务端进程

还有一个改写过的 `exit()` 方法

```py
def exit(self, server: PluginServerInterface):
    if server.is_server_running(): self.stop()
    server.exit()
```

这个方法会在关闭 MC 服务端后直接退出 MCDR，小小的改动（

---

不知道 [MCDR](<https://github.com/Fallen-Breath/MCDReforged> "全称 MCDReforged") 是什么？看[仓库](<https://github.com/Fallen-Breath/MCDReforged>)

---

源代码：

```py
import os
import time
from platform import system

from mcdreforged.api.all import *


PLUGIN_METADATA = {
    'id': 'tool_plugin',
    'version': '1.0.0',
    'name': 'Tool Plugin',
    'description': {
        'en_us': 'A tool plugin',
        'zh_cn': '服务器工具(单文件)'
    },
    'author': 'xieyuen',
    'dependencies': {
        'mcdreforged': '>=2.0.0',
    },
}

class Commands:
    def __init__(self):
        self.server = self.ServerControl()
    class ServerControl:
        def __init__(self): ...
        def start(self, server: PluginServerInterface):
            server.start()
            server.logger.info('服务器已开启')
        def stop(self, server: PluginServerInterface):
            server.logger.info('服务器关闭')
            server.stop()

            # wait 20 secs
            time.sleep(20)

            if server.is_server_running:
                server.logger.info('服务器似乎卡死了')
                server.logger.info('3s 后将会杀死服务端')
                time.sleep(3)

                server.kill()
            
            server.logger.info('服务端已停止')
        def exit(self, server: PluginServerInterface):
            if server.is_server_running(): self.stop()
            server.exit()
        def restart(self, server: PluginServerInterface):
            self.stop()
            self.start()
    def run(self, command: str):
        exec(command)

cmds = Commands()


def on_load(server: PluginServerInterface, prev_module):
    server.logger.info('插件已加载')
    server.logger.warning('此插件为单文件插件！')
    server.logger.warning('此插件为单文件插件！')
    server.logger.warning('此插件为单文件插件！')
    server.logger.info('Powered by xieyuen')

    server.register_help_message(
        '!!tools',
        '一个小工具'
        )

    server.register_command(
        Literal('!!tools')
        .requires(lambda src: src.has_permission(1))
        .runs(
            lambda src: src.reply(server.rtr('tool_plugin.help'))
        ).then(
            Literal('server')
            .requires(lambda src: src.has_permission(3))
            .then(
                Literal('start')
                .runs(cmds.server.start)
                .requires(lambda src: src.has_permission(4))
            ).then(
                Literal('stop')
                .runs(cmds.server.stop)
                .requires(lambda src: src.has_permission(3))
            ).then(
                Literal('exit')
                .runs(cmds.server.exit)
                .requires(lambda src: src.has_permission(4))
            ).then(
                Literal('restart')
                .runs(cmds.server.restart)
                .requires(lambda src: src.has_permission(3))
            )
        )
    )  # reg command
```