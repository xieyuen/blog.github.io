---
layout: post
title: python版粗制连点器
permalink: /posts/2023/8/python粗制连点器
---

```py
import time

import keyboard
import win32api
import win32con
from pykeyboard import PyKeyboard

kb = PyKeyboard()


def mouse_click(__x, __y):
    """
    模拟鼠标点击

    :param __x: 横坐标
    :param __y: 纵坐标
    """
    win32api.SetCursorPos([__x, __y])
    win32api.mouse_event(win32con.MOUSEEVENTF_LEFTDOWN, 0, 0, 0, 0)
    win32api.mouse_event(win32con.MOUSEEVENTF_LEFTUP, 0, 0, 0, 0)


def main():
    print(
        "这是抢装备的脚本，请先在游戏中打开交易之城，然后按回车键运行。\n"
        "建议在公示期结束前3~5分钟内运行\n"
        "\n"
        "若抢到装备，请按e退出\n"
        "按下Enter键的5秒后开始"
    )
    while not keyboard.is_pressed('Enter'):
        pass
    print("还有5秒开始")
    time.sleep(1)
    print("还有4秒开始")
    time.sleep(1)
    print("还有3秒开始")
    time.sleep(1)
    print("还有2秒开始")
    time.sleep(1)
    print("还有1秒开始")
    time.sleep(1)
    while True:
        if keyboard.is_pressed('e'):
            break
        mouse_click(400, 250)  # 把打印出来的结果写到这 括号 里
        time.sleep(0.1)
        kb.tap_key('f')
        time.sleep(0.1)
        kb.tap_key('f')
        time.sleep(0.1)
    print("Finish")
    while not keyboard.is_pressed('Enter'):
        pass

def test():
    print(
        "交易之城武器在屏幕的位置需要确定\n"
        "请现在在游戏中打开交易之城，选择任意一把武器关注，先按下回车键后点击鼠标，控制台会打印坐标\n"
        "然后将坐标写入代码中原来“400, 250”的位置\n"
        "别忘了把最下面的 “test()” 换成 “main()”"
    )
    while not keyboard.is_pressed('Enter'):
        pass
    print("请点击武器")
    while True:

if __name__ == '__main__':
    test()

```