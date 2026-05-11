---
title: "《白话机器学习的数学》和《白话深度学习的数学》的示例源代码勘误和本地化工作"
date: 2026-05-11
description: "整理并本地化两本书的示例源代码，让中文和英文读者更容易阅读、运行和对照学习。"
categories: [notes]
tags: [machine-learning, deep-learning, source-code]
---

我把《白话机器学习的数学》和《白话深度学习的数学》的示例源代码做了一次整理。

主要工作很简单：把原来以日文为主的注释和运行输出，整理成英文 / 中文 / 日文的形式；同时修了一些在当前 Python 环境里会影响运行的小问题。

例如，`nn.py` 里旧版 NumPy 的 `np.int` 已经不可用，所以替换成了 `int`；`cnn.py` 里原来的 MNIST 下载地址已经返回 404，所以改成了还能访问的镜像地址。

这些修改不改变书里的核心算法、公式和示例结构，目的只是让代码更容易读、更容易跑，也更方便和原始版本对照。

源码仓库在这里：

<https://github.com/fewarxrw/baihuasourcecode>

I cleaned up the sample source code for *The Math of Machine Learning in Plain Language* and *The Math of Deep Learning in Plain Language*.

The main work was straightforward: I localized the mostly Japanese comments and runtime messages into English / Chinese / Japanese, and fixed a few compatibility issues for running the examples in a current Python environment.

For example, `np.int` in `nn.py` was replaced with `int`, and the broken MNIST download URL in `cnn.py` was changed to a working mirror.

The core algorithms, formulas, and example structure remain unchanged. The goal is simply to make the code easier to read, easier to run, and easier to compare with the original version.

Source code repository:

<https://github.com/fewarxrw/baihuasourcecode>
