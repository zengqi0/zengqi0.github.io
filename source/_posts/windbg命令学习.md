---
title: windbg命令学习
date: 2022-11-19 22:31:27
tags:
categories:
- win内核
---





# r 命令 

  

windbg文档

https://learn.microsoft.com/zh-cn/windows-hardware/drivers/debugger/memory-window

# 查看寄存器

可以通过在调试器命令窗口中输入 [**r (Registers)** ](https://learn.microsoft.com/zh-cn/windows-hardware/drivers/debugger/r--registers-) 命令来查看和编辑寄存器。 可以使用多个选项或使用 [**rm (Register Mask)** ](https://learn.microsoft.com/zh-cn/windows-hardware/drivers/debugger/rm--register-mask-) 命令自定义显示。

r ds 就显示 ds的值 

# 查看和编辑内存

可以通过在调试器命令窗口中输入其中一个 [**显示内存**](https://learn.microsoft.com/zh-cn/windows-hardware/drivers/debugger/d--da--db--dc--dd--dd--df--dp--dq--du--dw--dw--dyb--dyd--display-memor) 命令来查看内存。 可以通过在调试器命令窗口中输入其中一个 [**Enter Values**](https://learn.microsoft.com/zh-cn/windows-hardware/drivers/debugger/e--ea--eb--ed--ed--ef--ep--eq--eu--ew--eza--ezu--enter-values-) 命令来编辑内存。

https://blog.csdn.net/qq_41610493/article/details/123028250?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-123028250-blog-82458330.pc_relevant_3mothn_strategy_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-123028250-blog-82458330.pc_relevant_3mothn_strategy_recovery&utm_relevant_index=2
